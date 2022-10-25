# 机密 HelloWorld

在本章中，我们将看到如何：

编写一个智能合约，将数据存储到机密存储区和从机密存储区加载数据，以及
实例化并调用智能合约而不透露调用参数。

## 机密单元

在hello world示例中，我们用于 `PublicCell<T>`访问该合约实例的键值对存储。在这种情况下，该值未加密地存储在与我们提供给构造函数的密钥的哈希相关联的区块链上（例如，`counter`在 `PublicCell::new(b"counter")`）。

Cipher 支持另一个原语`ConfidentialCell<T>` ，它使您能够通过硬件级加密保密地存储和加载数据。此外，该值与随机数一起加密，因此每次对区块链观察者来说都是不同的，即使解密后的值保持不变。也就是说，nonce 是从以下位置生成的：

- 整数，
- 当前智能合约执行期间的子调用次数，
- 当前区块中来自智能合约的机密存储访问次数。

> 危险！合约状态中机密单元的位置 仍然基于传递给构造函数的初始化密钥。因此，如果您声明多个机密单元并在每次调用时写入同一个单元，区块链观察者会注意到每次都在更改同一个单元。

要调用机密单元格获取器和设置器，您需要提供机密存储的实例。存储是通过调用 confidential_store()合约的上下文对象获得的。例如，如果节点操作员将尝试在非机密环境中执行您的代码，他们将无法获得执行解密所需的密钥，因此操作将失败。

现在，让我们看看 hello world 智能合约的机密版本是什么样子的：

src/lib.rs

```
//! A confidential hello world smart contract.
extern crate alloc;

use oasis_contract_sdk as sdk;
use oasis_contract_sdk_storage::cell::ConfidentialCell;

/// All possible errors that can be returned by the contract.
///
/// Each error is a triplet of (module, code, message) which allows it to be both easily
/// human readable and also identifyable programmatically.
#[derive(Debug, thiserror::Error, sdk::Error)]
pub enum Error {
    #[error("bad request")]
    #[sdk_error(code = 1)]
    BadRequest,
}

/// All possible requests that the contract can handle.
///
/// This includes both calls and queries.
#[derive(Clone, Debug, cbor::Encode, cbor::Decode)]
pub enum Request {
    #[cbor(rename = "instantiate")]
    Instantiate { initial_counter: u64 },

    #[cbor(rename = "say_hello")]
    SayHello { who: String },
}

/// All possible responses that the contract can return.
///
/// This includes both calls and queries.
#[derive(Clone, Debug, Eq, PartialEq, cbor::Encode, cbor::Decode)]
pub enum Response {
    #[cbor(rename = "hello")]
    Hello { greeting: String },

    #[cbor(rename = "empty")]
    Empty,
}

/// The contract type.
pub struct HelloWorld;

/// Storage cell for the counter.
const COUNTER: ConfidentialCell<u64> = ConfidentialCell::new(b"counter");

impl HelloWorld {
    /// Increment the counter and return the previous value.
    fn increment_counter<C: sdk::Context>(ctx: &mut C) -> u64 {
        let counter = COUNTER.get(ctx.confidential_store()).unwrap_or_default();
        COUNTER.set(ctx.confidential_store(), counter + 1);

        counter
    }
}

// Implementation of the sdk::Contract trait is required in order for the type to be a contract.
impl sdk::Contract for HelloWorld {
    type Request = Request;
    type Response = Response;
    type Error = Error;

    fn instantiate<C: sdk::Context>(ctx: &mut C, request: Request) -> Result<(), Error> {
        // This method is called during the contracts.Instantiate call when the contract is first
        // instantiated. It can be used to initialize the contract state.
        match request {
            // We require the caller to always pass the Instantiate request.
            Request::Instantiate { initial_counter } => {
                // Initialize counter to specified value.
                COUNTER.set(ctx.confidential_store(), initial_counter);

                Ok(())
            }
            _ => Err(Error::BadRequest),
        }
    }

    fn call<C: sdk::Context>(ctx: &mut C, request: Request) -> Result<Response, Error> {
        // This method is called for each contracts.Call call. It is supposed to handle the request
        // and return a response.
        match request {
            Request::SayHello { who } => {
                // Increment the counter and retrieve the previous value.
                let counter = Self::increment_counter(ctx);

                // Return the greeting as a response.
                Ok(Response::Hello {
                    greeting: format!("hello {} ({})", who, counter),
                })
            }
            _ => Err(Error::BadRequest),
        }
    }

    fn query<C: sdk::Context>(_ctx: &mut C, _request: Request) -> Result<Response, Error> {
        // This method is called for each contracts.Query query. It is supposed to handle the
        // request and return a response.
        Err(Error::BadRequest)
    }
}

// Create the required Wasm exports required for the contract to be runnable.
sdk::create_contract!(HelloWorld);

// We define some simple contract tests below.
#[cfg(test)]
mod test {
    use oasis_contract_sdk::{testing::MockContext, types::ExecutionContext, Contract};

    use super::*;

    #[test]
    fn test_hello() {
        // Create a mock execution context with default values.
        let mut ctx: MockContext = ExecutionContext::default().into();

        // Instantiate the contract.
        HelloWorld::instantiate(
            &mut ctx,
            Request::Instantiate {
                initial_counter: 11,
            },
        )
        .expect("instantiation should work");

        // Dispatch the SayHello message.
        let rsp = HelloWorld::call(
            &mut ctx,
            Request::SayHello {
                who: "unit test".to_string(),
            },
        )
        .expect("SayHello call should work");

        // Make sure the greeting is correct.
        assert_eq!(
            rsp,
            Response::Hello {
                greeting: "hello unit test (11)".to_string()
            }
        );

        // Dispatch another SayHello message.
        let rsp = HelloWorld::call(
            &mut ctx,
            Request::SayHello {
                who: "second call".to_string(),
            },
        )
        .expect("SayHello call should work");

        // Make sure the greeting is correct.
        assert_eq!(
            rsp,
            Response::Hello {
                greeting: "hello second call (12)".to_string()
            }
        );
    }
}
```
该合同的构建方式与其非机密对应物相同：

```
cargo build --target wasm32-unknown-unknown --release
```

> 包含所有已编译合约的区块链存储是公开的。这意味着任何人都可以反编译你的智能合约并查看它是如何工作的。不要在智能合约代码中放入任何敏感数据！

由于智能合约商店是公开的，因此上传 Wasm 代码与非机密代码相同：

```
oasis contracts upload hello_world.wasm
```

## 机密实例化并调用

为了生成机密事务，`oasis contracts`子命令接受一个`--encrypted`标志。机密交易具有加密的合约地址、函数名称、参数以及发送的代币数量和类型。但是，包含签名者信息的授权信息是公开的！也就是说，它包含您帐户的公钥或预期的多重签名密钥列表，以及气体限制和处理交易要支付的费用金额。

> 危险！ 虽然交易本身是保密的，但智能合约执行的影响可能会泄露一些信息。例如，账户余额是公开的。如果效果是从签名者的帐户中减去 10 个代币，这很可能意味着它们已作为此交易的一部分被转移。

在我们实例化合约之前，我们需要考虑我们的机密智能合约的 gas 使用情况。由于智能合约的执行取决于（加密的）智能合约状态，因此无法自动计算气体限制。目前，机密交易的气体限制是针对简单的交易执行量身定制的（例如，没有为访问合约状态保留气体）。对于更昂贵的交易，我们需要显式传递--gas-limit参数并暂时猜测足够的值，否则我们将得到`out of gas`错误。例如，要通过对合约状态的一次写入来实例化上面的智能合约，我们需要将 gas 限制提高到`60000`：

```
oasis contracts instantiate CODEID '{instantiate: {initial_counter: 42}}' --encrypted --gas-limit 60000
```

> 该out of gas错误可能会揭示智能合约的（机密）状态！如果您的智能合约包含一个依赖于存储在合约状态中的值的分支，则类似于 密码算法设计中已知的定时攻击的攻击可能会成功。为了克服这个问题，你的代码不应该包含依赖于秘密智能合约状态的分支。  
类似的气体限制攻击可能会泄露客户的交易参数。例如，如果调用函数A需要消耗50,000气体单位和函数B 300,000气体单位，则攻击者可以根据交易的气体限制（公开的）暗示执行了哪个函数调用。为了减轻这种攻击，客户端应该始终使用所有合约函数调用中的最大 gas 成本——在这种情况下300,000。

最后，我们进行一个加密调用：

```
oasis contracts call INSTANCEID '{say_hello: {who: "me"}}' --encrypted --gas-limit 60000
```

> 无论智能合约中使用何种机密存储，任何发出的事件都将是公开的。

> 您可以从 Oasis SDK 存储库查看和下载[完整的示例](https://github.com/oasisprotocol/oasis-sdk/tree/main/examples/contract-sdk/c10l-hello-world)。

> 2022/10/25 - moi

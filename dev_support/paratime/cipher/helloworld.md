# HelloWorld

本节将向您展示如何快速创建、构建和测试最小的 Oasis WebAssembly 智能合约。

## 存储结构和依赖项

首先，我们使用 Rust 为 hello world 合约创建基本目录结构`cargo`：

```
cargo init --lib hello-world
```

这将创建`hello-world`目录并使用描述 Rust 应用程序所需的一些样板来填充它。它还将使用 Git 设置用于版本控制的目录。本指南的其余部分假定您正在此目录中执行命令。

由于 Contract SDK 需要 Rust 工具链的夜间版本，因此您需要通过创建一个名为 `rust-toolchain`包含以下信息的特殊文件来指定要使用的版本：

```
nightly-2022-08-22
```
完成本指南后，最小的运行时目录结构将如下所示：

```
hello-world
├── Cargo.lock      # Dependency tree checksums (generated on first compilation).
├── Cargo.toml      # Rust crate definition.
├── rust-toolchain  # Rust toolchain version configuration.
└── src
    └── lib.rs      # Smart contract source code.
```

## 智能合约定义

首先，您需要声明一些依赖项才能使用智能合约 SDK。此外，您将需要指定一些优化标志，以使编译的智能合约尽可能小。为此，请将您的文件编辑`Cargo.toml`为如下所示：

Cargo.toml

```
[package]
name = "hello-world"
version = "0.0.0"
edition = "2018"
license = "Apache-2.0"

[lib]
crate-type = ["cdylib"]

[dependencies]
cbor = { version = "0.2.1", package = "oasis-cbor" }
oasis-contract-sdk = { git = "https://github.com/oasisprotocol/oasis-sdk" }
oasis-contract-sdk-storage = { git = "https://github.com/oasisprotocol/oasis-sdk" }

# Third party.
thiserror = "1.0.30"

[profile.release]
opt-level = 3
debug = false
rpath = false
lto = true
debug-assertions = false
codegen-units = 1
panic = "abort"
incremental = false
overflow-checks = true
strip = true
```

> 我们使用 Git 标签进行发布，而不是在 crates.io 上发布 Rust 包。

更新之后`Cargo.toml`，接下来要做的是定义 hello world 智能合约。为此，请`src/lib.rs`使用以下内容进行编辑：

src/lib.rs

```
//! A minimal hello world smart contract.
extern crate alloc;

use oasis_contract_sdk as sdk;
use oasis_contract_sdk_storage::cell::PublicCell;

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
const COUNTER: PublicCell<u64> = PublicCell::new(b"counter");

impl HelloWorld {
    /// Increment the counter and return the previous value.
    fn increment_counter<C: sdk::Context>(ctx: &mut C) -> u64 {
        let counter = COUNTER.get(ctx.public_store()).unwrap_or_default();
        COUNTER.set(ctx.public_store(), counter + 1);

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
                COUNTER.set(ctx.public_store(), initial_counter);

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

就是这个！您现在拥有一个简单的 hello world 智能合约，其中包含针对其功能的单元测试。您还可以查看[Oasis Contract SDK](https://github.com/oasisprotocol/oasis-sdk/blob/main/contract-sdk/src/contract.rs)支持的其他智能合约句柄。

> PublicCell<T>可以使用任何T实现`oasis_cbor::Encode`和`T` 的类型`oasis_cbor::Decode`。

## 测试

要运行单元测试类型：

```
RUSTFLAGS="-C target-feature=+aes,+ssse3" cargo test
```

> 在本地运行单元测试需要具有 AES 和 SSSE3 指令集的物理或虚拟化 Intel 兼容 CPU。
## 为部署而构建合约

为了在上传到目标链之前构建智能合约，运行：

```
cargo build --target wasm32-unknown-unknown --release
```

这将生成一个名为的二进制文件`hello_world.wasm`， `target/wasm32-unknown-unknown/release`其中包含编译成 WebAssembly 的智能合约。该文件可以直接部署在链上。

## 部署合约

使用 Oasis CLI 部署我们刚刚构建的合约很简单。本节假设您已经设置了 CLI 的实例，并且您将在现有的测试网上部署合约，您已经拥有一些 TEST 代币来支付交易费用。

首先，将默认网络切换到 Cipher Testnet 以避免需要将其传递给每个后续调用。

```
oasis network set-default testnet
oasis paratime set-default testnet cipher
```

对给定二进制文件只需要执行一次的第一个部署步骤是上传 Wasm 二进制文件。

```
oasis contracts upload hello_world.wasm
```

成功执行后，它将显示您需要用于同一合约的任何后续实例化的代码 ID。接下来，通过加载代码并使用一些虚拟参数调用其构造函数来创建合约实例。请注意，参数取决于正在部署的合约，在我们的 hello world 案例中，我们只是采用初始计数器值。

```
oasis contracts instantiate CODEID '{instantiate: {initial_counter: 42}}'
```

成功执行后，它会显示调用实例化合约所需的实例 ID。接下来，您可以测试调用合约。

```
oasis contracts call INSTANCEID '{say_hello: {who: "me"}}'
```

> 您可以从 Oasis SDK 存储库查看和下载[完整的示例](https://github.com/oasisprotocol/oasis-sdk/tree/main/examples/contract-sdk/hello-world)。

> 2022/10/25 - moi


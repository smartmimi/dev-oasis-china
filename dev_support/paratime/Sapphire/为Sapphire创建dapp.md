# 为 Sapphire 创建 dapp

这个页面主要描述了Sapphire 和以太坊之间的区别，因为有太多关于以太坊开发的优秀教程。 如果您不知道从哪里开始，Hardhat 教程、Solidity 文档和 Emerald dapp 教程是很好的起点。 设置好开发环境并将合约部署到非加密 EVM 网络（例如 Ropsten、Emerald）后，您可以继续遵循本指南。

## Oasis 共识层和 Sapphire ParaTime

Oasis 网络由共识层和多个 ParaTimes 组成。 ParaTimes 是独立的复制状态机，使用共识层来结算交易（要了解更多信息，请查看 [Oasis Network Overview] 概述章节）。 Sapphire 是实现以太坊虚拟机 (EVM) 的 ParaTime。

Sapphire 中的最小和预期阻塞时间为 6 秒。 任何 Sapphire 交易都将至少需要这个时间来执行，并且可能不会更多。

包括 Sapphire 在内的 ParaTimes 不允许直接访问您存储在共识层帐户中的代币。您需要将代币从您的共识账户存入 Sapphire *。*请参阅[如何将 ROSE 转移到 EVM ParaTime](https://docs.oasis.dev/general/manage-tokens/how-to-transfer-rose-into-evm-paratime)章节以了解更多信息。

## 测试网和主网

Sapphire ParaTime 目前部署在测试网上，计划在 2022 年晚些时候部署主网。测试网应该被认为是不稳定的软件，它的状态可能随时被清除。顾名思义，除非您要测试用户在擦除状态时的愤怒程度，否则只能使用测试网进行测试。

:::danger **永远不要在测试网上部署生产服务**

因为将来可以擦除 Testnet 状态，所以您**永远不**应该在 Testnet 上部署生产服务！只是不要这样做！

:::

:::tip 提示

出于测试目的，请访问我们的[Testnet 水龙头](https://faucet.testnet.oasis.dev/)以获取一些 TEST，然后您可以在 Sapphire Testnet 上使用这些 TEST 来支付 gas 费用。水龙头支持将 TEST 发送到您的共识层地址或您在 ParaTime 内的地址。

:::

## Sapphire vs 以太坊

Sapphire ParaTime 通常与以太坊、EVM 以及您已经使用的所有用户和开发人员工具兼容。 虽然有一些重大更改，但我们认为您会喜欢它们：

- 合约状态仅对编写它的合约可见。 对于合约 API，就好像所有状态变量都被声明为私有，但进一步的限制是，即使是完整节点也不能读取这些值。 而是通过显式 getter 提供公共或访问控制的值。
- 交易和调用被端到端加密到合约中。只有调用者和合约才能看到发送到/从 ParaTime 接收的数据。然而，这最终击败了区块浏览器的大部分实用程序。
- 呼叫使用的`from`地址来自附加到呼叫的签名。未签名的呼叫将其发件人设置为零地址。这允许合约作者编写向经过身份验证的调用者发布秘密的 getter，但不需要将交易发布到链上。

除了保密性，你还能得到一些额外的好处，包括产生私有熵的能力，以及在链上进行签名。一个使用这两者的dapp的例子是HSM合约，它生成一个Ethereum钱包，并通过交易对发送到它的交易进行签名。

否则Sapphire就像Emerald，它就像一个快速、廉价的以太坊。

## 集成 Sapphire

将 ROSE 代币[存入 Sapphire](https://docs.oasis.dev/general/manage-tokens/how-to-transfer-rose-into-evm-paratime)后，用户开始使用 dapp 应该很容易。为了实现这种理想的用户体验，我们必须稍微修改 dapp，但我们的兼容性库使它变得简单（即将推出）。

有其他语言的兼容层，可以在[repo](https://github.com/oasisprotocol/sapphire-paratime/tree/main/clients)中找到。

## 编写安全的 dapp

### 钱包

Sapphire 与流行的自托管钱包兼容，包括 MetaMask、Ledger、Brave 等。 您还可以使用 Web3.js 和 Ethers 等库来创建程序化钱包。 一般来说，如果它生成 secp256k1 签名，它会工作得很好。

### Languages & Frameworks语言和框架

Sapphire 可以使用任何针对 EVM 的语言进行编程，例如 Solidity 和 Vyper。 如果你更喜欢使用像 Hardhat 或 Truffle 这样的以太坊框架，你也可以将它们与 Sapphire 一起使用； 您需要做的就是设置您的 Web3 网关 URL。 您可以在此处找到 Oasis Sapphire Web3 网关的详细信息。

### 交易和呼叫

交易和调用必须加密和签名以获得最大的安全性。 您可以使用（即将推出）JS 包让您的生活更轻松。 它将为您处理加密和签名。

您应该知道，根据私有数据的价值采取行动可能会通过诸如花费的时间和气体使用等边渠道泄漏私有数据。如果您需要在私有数据上进行分支，在大多数情况下，您应该确保两个分支都表现出相似的时间/气体和存储模式。

要记住的另一件事是，`msg.sender`未签名的呼叫将归零。如果要`msg.sender`用于访问控制，则必须对调用进行签名。如果您想避免在用户钱包中弹出签名，只需将 `from`地址设置为全零即可。JS 库将为您执行此操作。****

### 合约状态

Sapphire 状态模型类似于以太坊的模型，除了所有状态都被加密并且除了合约之外的任何人都无法访问。该合约在一个活跃的（经过验证的）Oasis 计算节点中执行，是唯一可以从 Oasis 密钥管理器请求其状态加密密钥的实体。存储在 state 中的项目的键和值都是加密的，但两者的大小都*没有*隐藏。您的应用程序可能需要将状态项填充到恒定长度，或使用其他混淆。观察者也可能能够根据存储访问模式推断计算，因此您可能也需要对其进行混淆。
合同状态可以通过日志/事件或明确的 getter 提供给第三方。****

### 合约日志

合约日志/事件（例如，由`emit`Solidity 关键字发出的那些）与以太坊完全一样。事件中包含的未加密数据。但是，预编译的合同可帮助您加密数据，然后您可以将其打包到事件中。

:::danger 未修改的合约可能会通过日志泄露状态

像 OpenZeppelin 提供的基础合约通常会发出包含私人信息的日志。如果您不知道他们正在这样做，您可能会破坏您所在州的机密性。作为一个具体的例子，ERC-20 规范要求实现者发出一个`event Transfer(from, to, amount)`，如果你正在编写一个机密令牌，这显然是有问题的。相反，您可以做的是分叉该合同并消除有问题的排放。

:::

### Precompiles预编译

Sapphire 提供了一些您可能会觉得有用的额外预编译合约。
:::note

其中一些可能最初无法使用，但所有这些都将很快到来。

:::

- `0x010000000000000000000000000000000000000000randomBytes(uint256 numWords) returns (bytes)`
生成加密安全字节。如果没有足够的熵，则恢复。
- `0x010000000000000000000000000000000000000001k256Sign(bytes32 key, bytes32 dataHash) returns (bytes32 r, bytes32 s, bytes32 v)`
使用提供的 secp256k1 私钥对提供的数据哈希进行签名。
- `0x010000000000000000000000000000000000000002deoxysii256Encrypt(bytes32 key, bytes data) returns (bytes)`
使用 Deoxys-II 加密提供的数据，并返回提供的（随机统一的）密钥`nonce || ciphertext || tag`。
Cipher ParaTime 是用于执行 Wasm 智能合约的机密 ParaTime。

作为 Oasis 协议基金会官方支持的 ParaTime，Cipher 允许：

- 灵活性——开发人员可以定义哪些数据要存储在公共存储中，哪些数据要存储在（更昂贵的）机密存储中
- 安全性——主要用于编写 Wasm 智能合约的 Rust 语言以其严格的内存管理而闻名，并且是专门为避免内存泄漏而开发的
- 可扩展性——增加交易的吞吐量
- 低成本——比以太坊低 99% 以上的费用
- 跨链桥，实现跨链互操作（即将推出）

如果您正在寻找与 EVM 兼容的 ParaTimes，请查看 Emerald和机密的 Sapphire paratimes。

# 智能合约开发

Cipher ParaTime 实现了[Oasis Contract SDK API](https://github.com/oasisprotocol/oasis-sdk/tree/main/contract-sdk)。要了解如何在 Rust 中编写机密智能合约并将其部署在 Oasis Cipher ParaTime 上，请阅读相关的 Oasis Contract SDK 章节.

> 2022/10/25 - moi
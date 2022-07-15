# 钱包

本文将系统的介绍Oasis的钱包分类、使用场景、适用功能等。

## Oasis 分层结构

Oasis Network是于2020年11月19日上线的隐私公链。采用共识层与计算层分离的架构设计，能够实现可扩展性和灵活性，以部署低成本的以隐私为重点的智能合约。

共识层处理原生Token（ROSE）的转移、质押、委托及其他共识层事务，以及有限的Paratime更新条目值，Paratime层进行复杂计算，并将结果通过接口交给共识层，经过可验证计算验证后写入区块链。

![](wallet_1.png)

目前使用的Paratime中，本视频中以Emerald为例（Emerald为EVM兼容架构的Paratime）。

## 钱包分类及使用场景

依据在不同分层中的适用性，钱包分为共识层钱包、Emerald钱包

- 共识层钱包主要用于ROSE代币在共识层内流转，以及质押；
- Emerald钱包用于参与Emerald上的各种生态。。

共识层钱包：web钱包、插件钱包、bitpie等第三方钱包

Emerald钱包：MetaMask等可添加RPC网络的第三方钱包（App或插件）

插件钱包：可实现共识层与Paratime层的ROSE流动
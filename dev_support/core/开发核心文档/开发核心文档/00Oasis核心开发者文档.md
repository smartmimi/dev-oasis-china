# 00 Oasis 核心开发者文档

![https://github.com/oasisprotocol/oasis-core/raw/master/docs/images/oasis-core-high-level.svg](https://github.com/oasisprotocol/oasis-core/raw/master/docs/images/oasis-core-high-level.svg)

## **Development Setup 开发设置**

以下是有关如何设置本地环境构建、运行测试说明以及有关如何为 Oasis Core 组件的本地开发准备测试网络的一些示例。

- 配置开发环境和开发
    - [前提](https://github.com/oasisprotocol/oasis-core/blob/master/docs/development-setup/prerequisites.md)
    - [开发](https://github.com/oasisprotocol/oasis-core/blob/master/docs/development-setup/building.md)
- 运行测试和开发网络
    - [运行测试](https://github.com/oasisprotocol/oasis-core/blob/master/docs/development-setup/running-tests.md)
    - [简单运行时间的本地网络运行器](https://github.com/oasisprotocol/oasis-core/blob/master/docs/development-setup/oasis-net-runner.md)
    - [单一验证节点网络](https://github.com/oasisprotocol/oasis-core/blob/master/docs/development-setup/single-validator-node-network.md)
    - [部署运行环境](https://github.com/oasisprotocol/oasis-core/blob/master/docs/development-setup/deploying-a-runtime.md)

## 高级组件

在最高级别，Oasis Core 分为两个主要层：共识层和运行时层，如上图所示。

The idea behind the consensus layer is to provide a minimal set of features required to securely operate independent runtimes running in the runtime layer. It provides the following services:

共识层背后的想法是提供安全操作运行在运行时层中的独立运行时所需的最小功能集。 它提供以下服务：

- 基于纪元的时间保持和随机信标。
- 运营 PoS 区块链所需的质押操作。
- 分发公钥和元数据的实体、节点和运行时注册表。
- 运行时委员会调度、承诺处理和最小状态保持。

另外，每个运行时定义自己的状态和状态转换，独立于共识层，只提交简短的证明，证明计算被执行，结果被存储。这意味着运行时的状态和逻辑与共识层完全解耦，而共识层只提供关于什么状态（由Merklized数据结构的加密哈希总结）在任何特定时间点被认为是经典的信息。

有关具体组件及其实现的更多细节，请参见以下章节。

- [共识层](https://github.com/oasisprotocol/oasis-core/blob/master/docs/consensus/README.md)
    - [交易](https://github.com/oasisprotocol/oasis-core/blob/master/docs/consensus/transactions.md)
    - 服务
        - [纪元时间](https://github.com/oasisprotocol/oasis-core/blob/master/docs/consensus/services/epochtime.md)
        - [随机信标](https://github.com/oasisprotocol/oasis-core/blob/master/docs/consensus/services/beacon.md)
        - [质押](https://github.com/oasisprotocol/oasis-core/blob/master/docs/consensus/services/staking.md)
        - [注册](https://github.com/oasisprotocol/oasis-core/blob/master/docs/consensus/services/registry.md)
        - [委员会日程安排员](https://github.com/oasisprotocol/oasis-core/blob/master/docs/consensus/services/scheduler.md)
        - [治理](https://github.com/oasisprotocol/oasis-core/blob/master/docs/consensus/services/governance.md)
        - [根哈希](https://github.com/oasisprotocol/oasis-core/blob/master/docs/consensus/services/roothash.md)
        - [密钥管理](https://github.com/oasisprotocol/oasis-core/blob/master/docs/consensus/services/keymanager.md)
    - [创世文件](https://github.com/oasisprotocol/oasis-core/blob/master/docs/consensus/genesis.md)
    - [交易测试向量](https://github.com/oasisprotocol/oasis-core/blob/master/docs/consensus/test-vectors.md)
- [运行时](https://github.com/oasisprotocol/oasis-core/blob/master/docs/runtime/README.md)
    - [运行模式](https://github.com/oasisprotocol/oasis-core/blob/master/docs/runtime/README.md#operation-model)
    - [运行时主机协议](https://github.com/oasisprotocol/oasis-core/blob/master/docs/runtime/runtime-host-protocol.md)
    - [运行时ID](https://github.com/oasisprotocol/oasis-core/blob/master/docs/runtime/identifiers.md)
    - [消息](https://github.com/oasisprotocol/oasis-core/blob/master/docs/runtime/messages.md)
- Oasis 节点 (`oasis-node`)
    - [RPC](https://github.com/oasisprotocol/oasis-core/blob/master/docs/oasis-node/rpc.md)
    - [Metrics](https://github.com/oasisprotocol/oasis-core/blob/master/docs/oasis-node/metrics.md)
    - [CLI](https://github.com/oasisprotocol/oasis-core/blob/master/docs/oasis-node/cli.md)

## 通用组件

- [序列化](https://github.com/oasisprotocol/oasis-core/blob/master/docs/encoding.md)
- [密码学](https://github.com/oasisprotocol/oasis-core/blob/master/docs/crypto.md)
- 协议栈
    - 已[验证的 gRPC](https://github.com/oasisprotocol/oasis-core/blob/master/docs/authenticated-grpc.md)
- [MKVSMerklized 键值存储 (MKVS)](https://github.com/oasisprotocol/oasis-core/blob/master/docs/mkvs.md)

## **流程**

- [架构决策记录](https://github.com/oasisprotocol/adrs)
- [发布流程](https://github.com/oasisprotocol/oasis-core/blob/master/docs/release-process.md)
- [版本控制](https://github.com/oasisprotocol/oasis-core/blob/master/docs/versioning.md)
- [安全](https://github.com/oasisprotocol/oasis-core/blob/master/docs/SECURITY.md)
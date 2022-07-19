# 概述

以下文件旨在帮助个人或组织作为节点运营商参与Oasis网络。要加入网络，建议你先尝试在Testnet上运行一个节点。Testnet是我们的测试环境，在这里可以学习和实验，且不会有损失真实代币的风险。

[Oasis Network](notion://www.notion.so/oasis-network/overview.md) consists of the Consensus Layer and ParaTimes. Consensus and
ParaTime nodes can be operated by anyone.

[Oasis Network](notion://www.notion.so/oasis-network/overview.md) 由共识层(Consensus Layer)和ParaTimes组成。Consensus 和 ParaTime节点可由任何人操作。

> **提示**  
共识层(Consensus Layer) 是一个由120个验证器节点组成的去中心化集合，也是Oasis Network的核心骨干网络。目前验证器集的大小是由治理决定的--2020年网络开始时验证器集有80个节点，在过去的几次网络升级中已经扩大到120个节点。目前的节点运营商可通过[Oasis Scan](https://www.oasisscan.com/validators)等区块浏览器产看看到。

> **提示**  
在主网上运营一个ParaTime节点需要节点运营商的参与，他们在活跃的验证器集合中拥有验证器节点。ParaTimes有其特有的奖励制度，参与要求和结构。作为节点运营商可以参与任何数量 ParaTimes 节点运营工作。


如有任何关于运行节点的问题，可以通过[Discord](https://discord.gg/RwNTK8t)联系我们。

## Set Up Your Node overview

设置你的节点概述

要运行Validator节点，请确保系统满足一定的硬件和系统条件，并且安装了[Oasis Node](notion://www.notion.so/prerequisites/oasis-node.md)。

Then proceed by following the [Run a Validator Node](notion://www.notion.so/set-up-your-node/run-validator.md) guide to:

然后按照 "[运行验证器节点](https://www.notion.so/The-Daily-16ab1ee4391943feacf7a7b5396a22f2) "指南进行操作。

- 创建你的实例。
- 初始化并配置你的节点。
- 在你的托管账户中放入足够的股权。
- 在网络上注册你的实例。

要运行一个ParaTime节点，请确保首先设置一个Validator节点。然后。如果你想运行保密的ParaTimes节点，请设置一个可信的执行环境（TEE）。之后，进入运行ParaTime节点。

最后，如果在Oasis网络的基础上建立一个服务，我们建议建立自己的非验证器节点和ParaTime客户端节点（可选择），这样你的服务就不会依赖于任何第三方的端点，而这些第三方端点往往会进行流量限制。
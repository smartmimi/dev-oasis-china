# 初始文档

## 什么是初始文档?​

初始文档包含一组参数，这些参数概述了Oasis网络的初始状态。

网络初始文档中定义的状态包含启动特定网络（即Mainnet、Testnet）所需的所有信息，包括初始令牌分配、网络参数等。

>提示:有关初始文档的更深入解释，请参阅Oasis Core开发者文档中的初始文档部分。

需要注意的重要一点是，初始文档是用于计算初始文档的哈希。该哈希用于判定给定的事务是用于哪个网络的。

### 初始文件 vs. 初始文档

初始文件是对应于序列化初始文档的JSON文件。因此，它更便于分发和共享。

Oasis节点加载初始文件时，会将其转换为初始文档。

> 提示  
有关当前初始文件和当前初始文档哈希的最新信息可以在网络参数页面上找到。

## 参数

本节解释了初始文档的一些关键参数。

> 注意:  
以下章节中的具体参数值与Mainnet有关。其他Oasis网络（如Testnet）可能使用不同的值。

> 警告  
初始文档（或初始文件）中的令牌余额以基本单位计算。  
staking.token_value_exponent参数定义了token值的基数为10的指数。对于主网，它被设置为9，这意味着1个 ROSE 等于10^9（即十亿）个基本单位。

### 高度、初始时间和链ID​

高度参数指定网络的初始块高度。网络升级后，其高度将保持不变。例如，对于Cobalt升级，Mainnet状态转储的高度从3027600增加到3027601，增加了1。

genesis_time参数是一个ISO8601 UTC时间戳，用于指定网络何时正式启动。在初始之时，验证者预计会上线，并开始参与网络运营的共识过程。一旦代表初始共识委员会2/3以上股份的验证器上线，网络就会启动。

chain_id是网络的人类可读版本标识符。

> 注意  
需要注意的是，仅此值并不能决定Oasis网络的版本。相反，整个genesis文档的哈希值（例如genesis文档的哈希）是网络的唯一标识符。

### Epoch时间

> 注意  
在Cobalt升级中，epoch time部分将被删除，因为新的改进型随机信标已经过时。它将被下面描述的新信标部分所取代。

epochtime.params.interval指定Epoch中的块数。Epoch被用作质押奖励计划、脱粘间隔、网络到期时间等的时间度量。该值设置为600，表示每次生成600个新块时都会出现一个新Epoch。

### 注册

在registry对象中，有一系列参数指定节点操作符的初始集及其相应的初始节点状态。

- registry.params.max_node_expiration 节点注册持续的最长持续时间（以Epoch为单位）。起始值设置为2，以确保节点持续在线，因为节点的注册将在每次经过2个历元时过期，需要节点重新注册。
- registry.params.enable_runtime_governance_models 创建/更新注册时允许使用的运行时管理模型集。它被设置为 {"entity": true, "runtime": true}，这意味着运行时可以在实体治理和运行时定义的治理之间进行选择。
- registry.entities 实体初始节点操作符的实体注册，包括公钥和签名信息。
- registry.runtimes 初始节点运算符运行时注册器。每个项目描述运行时的操作参数，包括其标识符、种类、准入策略、委员会调度、存储、治理模型等。有关运行时描述符的完整描述，请参阅运行时结构文档。
- registry.suspended_runtimes 为初始节点操作符暂停运行时注册器。每个项目都描述了暂停运行时的操作参数，包括其标识符、种类、准入策略、委员会调度、存储、治理模型等。有关运行时描述符的完整描述，请参阅运行时结构文档。
- registry.nodes 初始节点运算符的节点注册，包括公钥和签名信息。

> 提示  
对于新网络，实体和节点注册通过实体包收集过程（例如Mainnet网络实体）获得。  
对于现有网络的升级，网络的状态转储工具会捕获网络的当前实体和节点注册器。

### Gas费
以下参数定义了网络上各种交易类型的Gas费：
- staking.params.gas_costs.add_escrow 添加托管（即股权代币）交易的成本。该值设置为1000。
- staking.params.gas_costs.burn 焚烧（即销毁代币）交易的成本。该值设置为1000。
- staking.params.gas_costs.reclaim_escrow 回收托管交易（即取消代币）的成本。该值设置为1000。
- staking.params.gas_costs.transfer 转账交易的成本。该值设置为1000。
- staking.params.gas_costs.amend_commission_schedule 修改或改变佣金计划的费用。该值设置为1000。
- registry.params.gas_costs.deregister_entity 注销实体交易的成本。该值设置为1000。
- registry.params.gas_costs.register_entity 注册实体交易的成本。该值设置为1000。
- registry.params.gas_costs.register_node 注册节点事务的成本。该值设置为1000。
- registry.params.gas_costs.register_runtime 注册辅助时间事务的成本。该值设置为1000。
- registry.params.gas_costs.runtime_epoch_maintenance 注册了ParaTime的节点在每个Epoch支付的维护费用。该值设置为1000。
- registry.params.gas_costs.unfreeze_node 解冻节点（即在节点被削减和冻结后）事务的成本。当前值为1000。
- registry.params.gas_costs.update_keymanager 更新keymanager事务的成本。该值设置为1000。
- roothash.params.gas_costs.compute_commit ParaTime 计算提交的成本。该值设置为10000。
- roothash.params.gas_costs.merge_commit ParaTime 合并提交的成本。该值设置为10000。

> 注意  
除了上述规定的Gas费外，每笔交易也会产生与其规模成比例的成本。  
consensus.params.gas_costs.tx_byte 参数指定事务每个字节的额外天然气成本。该值设置为1。
例如，大小为230字节的赌注转移交易的总天然气成本为1000+230。

### 根哈希
根哈希对象包含与根哈希服务相关的参数和与运行时相关的最小状态。
- roothash.params.max_runtime_messages 运行时在每一轮中可以发出的消息数的全局限制。该值设置为256。
- roothash.params.max_evidence_age 提交的计算节点删除证据的最长期限（以轮数为单位）。该值设置为100。

### Staking质押​
staking对象包含控制赌注服务的参数，以及与帐户、委托、特殊代币池相关的所有状态。

### 代币供应与分账​
以下参数指定了创世时整个网络的总代币供应量、为赌注奖励保留的总代币池以及账户余额：
- staking.total_supply 网络的总代币供应量（以基本单位计）。这是固定的100亿 ROSE 代币（该值设置为10,000,000,000,000,000,000个基本单位）。
- staking.common_pool 用于staking奖励的代币（以基本单位计）将随时间逐渐消耗。
- staking.governance_deposits 从治理提案存款中收集的代币（以基本单位计）。
- staking.ledger  staking分账，对创世时网络上的所有账户和相应账户余额进行编码，包括初始运营商、支持者、保管钱包等的账户。
- staking.delegations 创世时最初委托的编码。

> 提示  
staking.ledger 表示您的账户余额  
您账户中的 general.balance 包括您所有尚未质押或授权的代币。在您帐户的托管字段中，active.balanceholds 显示的所有代币会（主动）授予给你。

#### 委托
以下参数控制委托在网络上的行为：
- staking.params.debonding_interval 提取的押记或委托代币返回账户总余额之前必须经过的时间段（以纪元为单位）。该值设置为336个epoch，预计约为14天。
- staking.params.min_delegation 可以委托的最小代币数量。该值设置为100,000,000,000个基本单位，或100个 ROSE 代币。
- staking.params.allow_escrow_messages 表明是否启用对AddEscrow和Recrealscrow运行时消息的支持。该值被设置为true。

#### Node & ParaTime 代币阈值
有好几个 staking.params.thresholds 参数指定特定实体或特定类型节点参与网络所需的最小标记数。

实体、节点计算、节点密钥管理器、节点存储和节点验证器参数分别设置为10000000000个基本单位，这表明您需要持有至少100个ROSE令牌，才能使您的实体或任何指定节点在网络上运行。

staking.params.thresholds参数还指定了注册新ParaTime的最小阈值。runtime-compute和runtime-keymanager参数被设置为50000000000000个基本单位，这表明您需要持有至少50000个 ROSE 代币才能注册计算/密钥管理器ParaTime。

#### 奖励
以下参数控制网络上的staking的奖励：
- staking.params.reward_schedule staking奖励策略，表明赌注奖励率如何随时间变化，以epoch粒度为计算单位。奖励策略使用一个递减的公式，在较早的时期支付较高的奖励，然后随着时间的推移逐渐减少。有关更多详细信息，可以参阅Staking Incentives文档。
- staking.params.signing_reward_threshold_numerator 和 staking.params.signing_reward_threshold_denominator 这些参数定义了验证器在每个epoch必须签署才能获得staking奖励的区块比例。设定分数为3/4意味着验证器必须在一个epoch期间保持至少75%的正常运行时间，才能在该期间获得staking奖励。
- staking.params.fee_split_weight_propose 区块发起人在交易费用中的份额。该值设置为2。
- staking.params.fee_split_weight_next_propose 下一个区块发起人的交易费用份额。该值设置为1。
- staking.params.fee_split_weight_vote 区块签署人/投票人的交易费用份额。该值设置为1。
- staking.params.reward_factor_epoch_signed 分配给在给定epoch内至少签署了阈值区块的验证人的奖励系数。该值设置为1。
- staking.params.reward_factor_block_proposed The factor for rewards earned for block proposal. The value is set to 0, indicating validators get no extra staking rewards for proposing a block.区块发起人获得奖励的系数。该值被设置为0，表示验证器不会因发起区块而获得额外的赌注奖励。
#### 佣金规划

以下参数控制如何定义和更改佣金率和界限：
- staking.params.commision_schedule_rules.rate_change_interval 在佣金规划中可以指定利率变化的时间间隔（以epoch为单位）。该值设置为1，表示佣金率在每个历元都可能发生变化。
- staking.params.commision_schedule_rules.rate_bound_lead 更改佣金率界限所需的最短交付周期（以epoch为单位）。这是为了保护授权人不受运营商佣金率意外变化的影响。该值设置为336，预计约为14天。
- staking.params.commision_schedule_rules.max_rate_steps 佣金计划中费率阶跃变化的最大次数。该值设置为10，表示佣金计划最多可以有10个阶次的费率变化。
- staking.params.commision_schedule_rules.max_bound_steps 表示在佣金计划中，佣金率限制更改的最大阶次数量。该值设置为10，表示佣金计划最多可以有10个利率限制阶次。
#### Slashing 惩罚
这些参数指定网络slash机制的关键词：
- staking.params.slashing.consensus-equivocation.amount 因模棱两可（例如：双重签名）而产生的要slash的令牌数量。该值设置为10000000000个基本单位，或100个 ROSE 代币。
- staking.params.slashing.consensus-equivocation.freeze_interval 因模棱两可而被slash的节点，被“冻结”或被禁止参与网络共识的持续时间（以epoch为单位）。值18446744073709551615（64位无符号整数的最大值）意味着任何被拆分的节点实际上将被永远禁止进入网络。
- staking.params.slashing.consensus-light-client-attack.amount 针对轻客户端攻击要slash的token数量。该值设置为10000000000个基本单位，或100个 ROSE 代币。
- staking.params.slashing.consensus-light-client-attack.freeze_interval 因light client攻击而被slash的节点，“冻结”或被禁止参与网络共识的持续时间（以epoch为单位）。值18446744073709551615（64位无符号整数的最大值）意味着，任何因light client攻击而被删除的节点实际上都将被永久禁止进入网络。

### Committee调度器
scheduler对象包含控制各种参与者（验证器、计算、密钥管理器）如何定期调度的参数。
- scheduler.params.min_validators 协商参与者的最小规模。该值设置为15个验证者。
- scheduler.params.max_validators 协商参与者的最大规模。该值设置为100个验证者。
- scheduler.params.max_validators_per_entity 给定实体中在任何时候可以加入协商的最大节点数。该值设置为1。

### 随机Beacon​
beacon对象包含控制网络随机beacon的参数。
- beacon.base 网络的起始epoch。当一个网络升级时，它的epoch会被保留。例如，对于Cobalt upgrade，Mainnet的epoch状态转储的时间段从5046变到5047。
- beacon.params.backend 随机beacon的后端。该值设置为“pvss”，表示beacon实现了PVSS（可公开验证的秘密共享）方案。

#### PVSS Beacon
这些参数控制Cobalt升级中引入的新改进随机信标的行为：
- beacon.params.pvss_parameters.participants ：每个信标生成协议回合要选择的参与者数量。该值设置为20。
- beacon.params.pvss_parameters.threshold ：为使最终输出是有效的，必须设置成功贡献熵的最小参与人数。该值设置为10。
- beacon.params.pvss_parameters.commit_interval ：Commit阶段的时长（以区块为单位）. 该值设为400.
- beacon.params.pvss_parameters.reveal_interval：Reveal阶段的时长 (以区块为单位). 该值设为196.
- beacon.params.pvss_parameters.transition_delay __： post Reveal阶段的(以区块为单位). 该值设为4.

### 治理​
治理对象包含在Cobalt升级中介绍的负责控制 链上治理 的参数：
- governance.params.min_proposal_deposit：当提出一个新提案时质押的token数量。该值设置为 10,000,000,000,000 base units, 或者是10,000 ROSE.
- governance.params.voting_period：投票结束并计票的时间段（以epoch为单位）。该值设置为168，预计约为7天。 
- governance.params.quorum： 为使结果有效，提案所需的最低投票权百分比。该值设置为75（即75%）。
- governance.params.threshold：为使提案被接受，VoteYes投票的最低百分比。该值设置为90（即90%）。
- governance.params.upgrade_min_epoch_diff： 当前epoch和建议的升级epoch之间的最小epoch数，以使升级建议有效。此外，它还指定了两次连续挂起升级之间的最小epoch数。  
该值设置为336，预计约为14天。
- governance.params.upgrade_cancel_min_epoch_diff： 当前epoch和建议升级epoch之间的最小epoch数，以使升级取消提案有效。
该值设置为192，预计约为8天。

### 共识协议
以下参数用于定义网络共识协议的关键值：
- consensus.backend： 定义后端的共识协议。该值设置为 "tendermint" 意味着使用了Tendermint Core BFT协议。
- consensus.params.timeout_commit：指定提交块后等待多长时间（以纳秒为单位），然后再开始新的块高度（这会影响块间隔）。该值设置为5000,000,000纳秒或5秒。
- consensus.params.max_tx_size：共识层交易的最大大小（以字节为单位）。该值设置为32,768字节或32 KB。
- consensus.params.max_block_size：最大的区块大小 (以字节为单位). 该值设置为22,020,096字节或22 MB.
- consensus.params.max_block_gas： 最大的区块gas。该值设为0，规定了不限量的gas数量。
- consensus.params.max_evidence_size：最大的evidence大小（以字节为单位）。该值设置为51,200字节，或者50kB。
- consensus.params.public_key_blacklist：无法在网络上使用的公钥列表。目前，没有列入黑名单的公钥。
- consensus.params.state_checkpoint_interval：应在其上设置状态检查点的间隔（以块为单位）。该值设置为10000。
- consensus.params.state_checkpoint_num_kept： 要保留的历史状态检查点的数量。该值设置为2。
- consensus.params.state_checkpoint_chunk_size：创建状态检查点时应使用的块大小（字节）。该值设置为（8,388,608字节，或8 MB）。

### Halt Epoch​ 停止时代

halt_epoch参数指定网络计划停止的时间。此参数设置为在达到此epoch之前故意强制升级。例如，如果设置为9940，则表示网络应在epoch 9940之前升级，否则将停止。

> 2022/07/05 本文翻译自[官方技术文档](https://docs.oasis.dev/general/oasis-network/genesis-doc)
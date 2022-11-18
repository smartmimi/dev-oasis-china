# Random Beacon随机信标

随机信标服务负责在每个时期提供无偏随机性来源。 它使用由 PVSS 方案支持的 commit-reveal 方案，只要满足参与者的阈值，并且一个参与者是诚实的，就会生成安全熵。

服务接口定义位于 `go/beacon/api` 中。 它定义了支持的查询和事务。 更多信息，您还可以查看共识服务 API 文档和信标 ADR 规范。

## 具体操作[](https://docs.oasis.io/core/consensus/services/beacon#operation)

每个节点生成并维护一个长期的椭圆曲线点和标量对（公钥/私钥对），其点（公钥）包含在  [registry service](https://docs.oasis.io/core/consensus/services/registry) 存储的节点描述符中。在初始实施中，曲线为 P-256。

信标生成过程分为三个连续阶段。 *Commit* 和 *Reveal* 阶段的任何失败都会导致协议轮次失败，并且在取消导致失败的参与者的资格后，生成过程将重新开始。

### 提交阶段

在 epoch 转换或前一轮失败时，启动提交阶段，共识服务将从当前验证器集中选择“参与者”节点（按权益递减的顺序）作为熵贡献者。

信标状态是（重新）初始化的，并且会广播一个事件以向参与者发出信号，表明他们应该通过“beacon.SCRAPECommit”交易生成并提交他们的加密共享。

每个提交阶段都精确地持续“commit_interval”块，在该块结束时，该轮将关闭以进行进一步的提交。

在提交阶段结束时，评估协议状态以确保节点的“阈值”已发布加密共享，如果已发布的节点数量不足，则认为该轮失败。

以下行为目前是被标记为恶意/非参与节点的候选行为，并且会被排除在未来的轮次和削减之外：

- 不提交承诺。
- 格式错误的承诺（损坏/无法验证/等）。
- 尝试更改给定时期/回合的现有承诺。

### 展示阶段[](https://docs.oasis.io/core/consensus/services/beacon#reveal-phase)

当 `commit_interval` 过去时，假设已收到足够数量的提交，共识服务将过渡到揭示阶段并广播一个事件以通知参与者他们应该揭示从其他人收到的加密共享的解密值通过“beacon.PVSSReveal”交易的参与者。

每个揭示阶段都精确地持续 `reveal_interval` 块，在此阶段结束时，该轮将关闭以进一步揭示。

在揭示阶段结束时，评估协议状态以确保“阈值”节点已发布解密共享，如果在任一情况下发布的节点数量不足，则认为该轮失败。

以下行为目前是节点被标记为恶意/非参与的候选者，并且会被排除在未来的轮次和削减之外：

- 不提交披露。
- 格式错误的承诺（损坏/无法验证/等）。
- 尝试更改给定 Epoch/Round 的现有显示。

### 完成（转换等待）阶段

当`reveal_interval`过去了，假设已经接收到足够数量的揭示，信标服务恢复最终的熵输出（每个参与者共享的秘密的哈希值）并转换到完成（转换等待）阶段和广播 向参与者发出一轮完成信号的事件。

一旦一轮成功完成，除了下一个时期转换的调度之外，不会发生有意义的协议活动。

## 方法

以下部分描述了共识信标服务支持的方法。 请注意，这些方法只能由验证者调用，并且只能在它们是区块提议者时调用。

### PVSS 提交

提交 PVSS 提交。

**方法名:**

```
beacon.PVSSCommit

```

**主体:**

```
type PVSSCommit struct {
    Epoch EpochTime `json:"epoch"`
    Round uint64    `json:"round"`

    Commit *pvss.Commit `json:"commit,omitempty"`
}

```

### PVSS 展示

提交 PVSS 展示。

**方法名:**

```
beacon.PVSSReveal

```

**主体:**

```
type PVSSReveal struct {
    Epoch EpochTime `json:"epoch"`
    Round uint64    `json:"round"`

    Reveal *pvss.Reveal `json:"reveal,omitempty"`
}

```

## 共识参数

- `participants` 是每个信标生成协议轮次选择的参与者数量。
-“阈值”是必须成功贡献熵才能使最终输出被视为有效的最小参与者数量。 这也是从相应的解密共享中重建 PVSS 机密所需的最小参与者数量。
- `commit_interval` 是 *Commit* 阶段的持续时间，以块为单位。
- `reveal_interval` 是 *Reveal* 阶段的持续时间，以块为单位。
- `transition_delay` 是发布 *Reveal* 阶段延迟的持续时间，以块为单位。
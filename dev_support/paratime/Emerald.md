# Emerald 运行时（ParaTime）

Emerald ParaTime是我们的官方EVM兼容ParaTime，提供具有完全EVM兼容性的智能合约环境。

作为Oasis网络的官方EVM兼容ParaTime，Emerald允许：

- 全面兼容EVM
- 与基于 EVM 的 dapp 轻松集成，例如 DeFi、NFT、元宇宙和加密游戏
- 可扩展性——增加交易的吞吐量
- 低成本——比以太坊低 99% 以上的费用
- 跨链桥——实现跨链互操作性（即将推出）

如果您正在寻找具备加密性的 EVM，请查看[Sapphire ParaTime](/dev_support/paratime/Sapphire.md).

## ParaTime 激励机制

Emerald是完全去中心化的，节点运营商分布在全球各地，Oasis ROSE将是用于支付 Gas 费的原生代币。

ParaTime将在链上释放代币以奖励参与的节点。这些代币将在每个纪元释放，奖励为每个实体每个纪元 3 个 ROSE 代币。

目前正在以每小时一个的速度生产纪元。每个节点有大约30%的机会被初级委员会选中，以获得奖励。因此，一个节点实体每天可以赚取24个ROSE代币，每月720个ROSE代币。

奖励计划的期限为两年。

## Web3 网关

要在我们的Emerald ParaTime上开始构建，你可以使用我们的公共Web3网关，它与Ethereum的Web3网关完全兼容。

### Mainnet主网

- RPC HTTP endpoint: `https://emerald.oasis.dev`
- RPC WebSockets endpoint: `wss://emerald.oasis.dev/ws`
- Chain ID:
    - 十六进制: 0xa516
    - 十进制: 42262
- 区块浏览器: [https://explorer.emerald.oasis.dev](https://explorer.emerald.oasis.dev/)

### Testnet测试网

- RPC HTTP endpoint: `https://testnet.emerald.oasis.dev`
- RPC WebSockets endpoint: `wss://testnet.emerald.oasis.dev/ws`
- Chain ID:
    - 十六进制: 0xa515
    - 十进制: 42261
- 区块浏览器: [https://testnet.explorer.emerald.oasis.dev](https://testnet.explorer.emerald.oasis.dev/)

## 另见

[**在Emerald上编写dapp**](./Emerald/在Emerald上编写dapp.md)

[**集成BAND预言机智能合约**](./Emerald/集成BAND预言机智能合约.md)

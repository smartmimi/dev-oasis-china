# Emerald 运行时（ParaTime）

Emerald ParaTime 是我们的官方 EVM 兼容 ParaTime，提供具有完全 EVM 兼容性的智能合约环境。

作为Oasis网络的官方EVM兼容ParaTime，Emerald允许：

- 全面兼容EVM
- 与基于 EVM 的 dapp 轻松集成，例如 DeFi、NFT、元宇宙和加密游戏
- 可扩展性——增加交易的吞吐量
- 低成本——比以太坊低 99% 以上的费用
- 跨链桥——实现跨链互操作性（即将推出）

如果您正在寻找具备加密性的 EVM，请查看[Sapphire ParaTime](https://docs.oasis.dev/general/developer-resources/sapphire-paratime/).

## ParaTime 激励机制

Emerald是完全去中心化的，节点运营商分布在全球各地，Oasis ROSE将是用于支付 Gas 费的原生代币。

ParaTime将在链上释放代币以奖励参与的节点。这些代币将在每个纪元释放，奖励为每个实体每个时期 3 个 ROSE 代币。

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

<DocCard item={ findSidebarItem(‘manage-tokens/how-to-transfer-rose-into-evm-paratime’) } />

[**在 Emerald 上编写 dapp**](Emerald%20%E8%BF%90%E8%A1%8C%E6%97%B6%EF%BC%88ParaTime%EF%BC%89%20aad2bd882b42449094d725dc7bc0e9e0/%E5%9C%A8%20Emerald%20%E4%B8%8A%E7%BC%96%E5%86%99%20dapp%205645b15f10644d7b9a86cf62336e0fc8.md)

[集成 BAND 预言机智能合约](Emerald%20%E8%BF%90%E8%A1%8C%E6%97%B6%EF%BC%88ParaTime%EF%BC%89%20aad2bd882b42449094d725dc7bc0e9e0/%E9%9B%86%E6%88%90%20BAND%20%E9%A2%84%E8%A8%80%E6%9C%BA%E6%99%BA%E8%83%BD%E5%90%88%E7%BA%A6%20e576ae7f3c5344729de4d246a5d09c55.md)
# Wormhole简介及教程

[官网](https://wormholebridge.com/)/[跨链桥](https://portalbridge.com/#/transfer)

2021年12月7日，Oasis很高兴地宣布与WormHoleCrypto建立合作伙伴关系，未来通过整合Wormhole Bridge，利用Emerald(EVM),Oasis网络上的DeFi用户和开发者可以在诸如Avalanche、BSC、Terra、Ethereum、Polygon、Solana等各大去中心化网络之间实现资产的自由流通。

[Wormhole跨链操作](https://medium.com/@OasisNetworkCN/yuzuswap%E6%93%8D%E4%BD%9C%E6%95%99%E7%A8%8B-oasis%E7%94%9F%E6%80%81%E9%A6%96%E4%B8%AAdex%E4%B8%8A%E7%BA%BF-%E8%B5%A2%E5%8F%96%E4%B8%B0%E5%AF%8C%E5%A5%96%E5%8A%B1-9cb5fbbfe112) -- 摘自Oasis官方Medium

跨链代币及合约如下（仅列举Emerald上的合约地址，源链合约地址及支持的交易所请查看[Wormhole文档](https://docs.wormholenetwork.com/wormhole/overview-liquid-markets)

> 请万分注意，需要桥认可的代币才可以跨链后在对端使用。如BSC链上的USDT，需要经过Pancake转换成USDT(wormhole)代币后才可以跨链。

```
源ETH链：
WBTC:0xd43ce0aa2a29DCb75bDb83085703dc589DE6C7e

源ETH链/SOL链：
USDTet:0xdC19A122e268128B5eE20366299fc7b5b199C8e3

源ETH链/BSC链/MATIC链/AVAX链/TERRA链/SOL链：
WETH:0x3223f17957Ba502cbe71401D55A0DB26E5F7c68F

```

## 一、打开跨链桥

打开https://wormholebridge.com/,进入跨链桥页面。

![img](./wormholebridge跨链操作1.png)

选择你的资产来源链，目标链选择Oasis，确定好链后可以选择想要跨链的Token，点击NEXT。

## 二、确认交易信息。

这需要一些ROSE做为Gas费用（可以通过上面的CEX转入ROSE），确认之后点击NEXT。

![img](./wormholebridge跨链操作2.png)

## 三、发送代币。

点击Transfer，回到Ethereum，点击确认。

![img](./wormholebridge跨链操作3.png)

## 四、点击Redeem即可在Oasis主网领取已跨链的资产。

![img](./wormholebridge跨链操作4.png)

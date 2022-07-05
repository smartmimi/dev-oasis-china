# Oasis 网络参数

目前Mainnet的参数为：
- [Genesis file](https://github.com/oasisprotocol/mainnet-artifacts/releases/download/2022-04-11/genesis.json):
- SHA256: bb379c0202cf82404d75a3ebc6466b0c3b98f32fac62111ee4736a59d2d3f266

> 提示：Genesis文件由[网络目前的维护者](https://github.com/oasisprotocol/mainnet-artifacts/blob/master/README.md#pgp-keys-of-current-maintainers)签名。可以根据[PGP验证指令](https://github.com/oasisprotocol/mainnet-artifacts/blob/master/README.md#verifying-genesis-file-signatures)来验证它的权限。

- Genesis文档的哈希 ([释义](/general/genesis/genesis.md)):
    - b11b369e0da5bb230b220127f5e7b242d385ef8c6f54906243f30af63c815535
- Oasis种子节点的地址:
    - E27F6B7A350B4CC2B48A6CBE94B0A02B0DCB0BF3@35.199.49.168:26656

小技巧：除了这个种子节点外，也可以自由的使用其他节点
- [Oasis Core](https://github.com/oasisprotocol/oasis-core) 版本:
    - [22.1.7](https://github.com/oasisprotocol/oasis-core/releases/tag/v22.1.7)
- [Oasis Rosetta Gateway](https://github.com/oasisprotocol/oasis-rosetta-gateway) 版本:
    - [2.2.1](https://github.com/oasisprotocol/oasis-rosetta-gateway/releases/tag/v2.2.1)

> 提示：Oasis节点是Oasis Core所发布的内容中的一部分.

> 警告：不要使用更新版本的Oasis Core，因为它可能包含与其他节点使用的Oasis Core版本不兼容的更改。

如果您想加入我们的Testnet，请参阅Testnet文档以了解当前的Testnet参数。

## ParaTimes​

本节包含已知在Mainnet上部署的各种ParaTimes的参数。

### Cipher ParaTime​
- Oasis Core 版本:
    - [22.1.7](https://github.com/oasisprotocol/oasis-core/releases/tag/v22.1.7)
- Runtime 鉴别器:
    - 000000000000000000000000000000000000000000000000e199119c992377cb
- Runtime 二进制版本:
    - [1.1.0](https://github.com/oasisprotocol/cipher-paratime/releases/tag/v1.1.0)
- IAS 协议地址:
    - tnTwXvGbbxqlFoirBDj63xWtZHS20Lb3fCURv0YDtYw=@34.86.108.137:8650

> 提示 请随意使用除此处提供的代理之外的其他IAS代理，或运行自己的代理。

### Emerald ParaTime​
- Oasis Core 版本:
    - [22.1.7](https://github.com/oasisprotocol/oasis-core/releases/tag/v22.1.7)
- Runtime鉴别器:
    - 000000000000000000000000000000000000000000000000e2eaa99fc008f87f
- Runtime 二进制版本(或者 构建你自己的):
    - [8.2.0](https://github.com/oasisprotocol/emerald-paratime/releases/tag/v8.2.0)
- Emerald Web3 网关版本:
    - [2.2.0](https://github.com/oasisprotocol/emerald-web3-gateway/releases/tag/v2.2.0)
> 提示 查看Emerald ParaTime页面，了解如何访问公共Web3网关。

> 本文翻译于2022/07/5。文中的下载版本请于[原文](https://docs.oasis.dev/general/oasis-network/network-parameters)对比查看，避免下载到旧的版本。
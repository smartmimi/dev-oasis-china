# Bitpie钱包转移到官方钱包

## 钱包的区别

在区块链中，钱包地址对应着资产，私钥对应资产的所有权。助记词，顾名思义，是为了更方便的记住私钥。

助记词使用不同的算法，来生成私钥，所以同样的助记词，使用不用的算法，生成的私钥是不相同的。也就对应着不同的区块链链上地址。

**私钥与钱包地址是一一对应的，而“助记词+算法”与私钥一一对应**。

Bitpie钱包与官方钱包使用相同的BIP39算法,但未使用[ARD-0008](https://github.com/oasisprotocol/oasis-core/blob/master/docs/adr/0008-standard-account-key-generation.md)标准，因此同样的助记词，在两个钱包内对应的钱包地址不一样。

## 转移方法

从Bitpie钱包转移到官方钱包，可以选择以下两种做法:

- 从Bitpie钱包内，导出私钥（收款-右上角-显示私钥），然后用私钥在官方钱包内打开
- 在官方钱包内创建新的钱包，在Bitpie钱包内将ROSE转移到新的官方钱包地址

## 注意事项：

- 链上资产不会无故丢失，请不要惊慌。请务必保管好助记词/私钥，也需要知道不同的钱包/客户端使用的“助记词生成私钥”的算法会有所不同。

- Bitpie钱包可以导出私钥，然后导入到Oasis钱包内使用。但**Bitpie钱包不支持导入Oasis的私钥或助记词来在Bitpie钱包内使用**。因此需要使用转账的方式，将ROSE从官方钱包，转账到Bitpie钱包的新地址中。

- 官方web钱包近期出现问题，建议使用插件钱包或Bitpie钱包

更多细节，可参考[官方英文FAQ](https://docs.oasis.dev/general/manage-tokens/faq#how-can-i-export-my-bitpie-wallets-oasis-account-private-key)

> 作者：moioooo 最近更新：moioooo 20220227
# Sapphire教程

## 总览

在本教程中，你将在 10 分钟内移植一个 Eth 项目，然后继续部署一个需要机密性支持的独特 dapp。

在本教程结束时，你将可以轻松设置你的 Eth 开发环境来使用 Sapphire，并知道如何以及何时使用机密性。

本教程的预计完成时间为 30 分钟。
​
## 移植一个 ETH 项目
### 设置
​
从安装 [truffle](https://github.com/trufflesuite/truffle#install) 开始，​然后，在终端中运行如下命令：

```
mkdir MetaCoin
truffle unbox MetaCoin
cd MetaCoin
git init
git add :/ && git commit -m "Initial commit"
pnpm init && pnpm add -D @truffle/hdwallet-provider # npm or yarn also works
```
### 部署到 Emerald (非机密 EVM)
#### 获取 eROSE

为了在 Emerald 测试网中部署项目，我们需要获得一些 eROSE 代币。

前往 [Oasis 测试网水龙头](https://faucet.testnet.oasis.dev/) ，在下拉框中选择“Emerald”，下方填入你的地址（0x开头）。

提交申请并等待一段时间，你才能收到 eROSE 代币。
​
#### 将 Emerald 测试网添加到 Truffle

将此补丁应用于 `truffle-config.js`:

```diff
diff --git a/truffle-config.js b/truffle-config.js
index 68d534c..15c671d 100644
--- a/truffle-config.js
+++ b/truffle-config.js
@@ -22,7 +22,7 @@
 // const mnemonic = process.env["MNEMONIC"];
 // const infuraProjectId = process.env["INFURA_PROJECT_ID"];
​
-// const HDWalletProvider = require('@truffle/hdwallet-provider');
+const HDWalletProvider = require('@truffle/hdwallet-provider');
​
 module.exports = {
   /**
@@ -53,6 +53,14 @@ module.exports = {
     //   network_id: 5,       // Goerli's id
     //   chain_id: 5
     // }
+    emerald: {
+      provider: () =>
+        new HDWalletProvider([process.env.PRIVATE_KEY], "https://testnet.emerald.oasis.dev"),
+      network_id: 0xa515,
+    },
   },
​
   // Set default mocha options here, use special reporters etc.
```

#### Truffle 相关操作

你需要一个脚本来在合约上运行一些方法。打开你最喜欢的编辑器并插入以下字段：

```javascript
​
```
把它保存到 `scripts/exercise-contract.js`。我们稍后会用到它。

接下来，你可以运行以下命令并查看正在部署的合约。

```
PRIVATE_KEY="0x..." truffle migrate --network emerald
```

到目前为止，一切都应该一帆风顺。最后，运行这一行并观察输出。

```
> PRIVATE_KEY="0x..." truffle exec --network emerald scripts/exercise.contract.js`
​
Sent some coins in 0xf415ab586ef1c6c61b84b3bd803ae322f375d1d3164aa8ac13c9ae83c698a002
A Transfer(0x56e5F834F88F9f7631E9d6a43254e173478cE06a, 0x56e5F834F88F9f7631E9d6a43254e173478cE06a, 42) was emitted.
The balance storage slot contains 0x2a.
The contract now has balance: 42
```
​
很棒！这将是我们机密部署的基准。
​
### 移植到 Sapphire (机密 EVM)
#### 获得 sROSE

现在到了一个有意思的部分。如同在 Emerald 上一样，我们需要配置 Sapphire 网络并获得测试代币。

点击唯一的[Oasis 测试网水龙头](https://faucet.testnet.oasis.dev/)，这次选择“Sapphire”。提交表格。
​
#### 将 Sapphire 测试网添加到 Truffle

你提交的另一个不同之处在于：
​
```diff
diff --git a/truffle-config.js b/truffle-config.js
index 7af2f42..0cd9d36 100644
--- a/truffle-config.js
+++ b/truffle-config.js
@@ -58,6 +58,11 @@ module.exports = {
         new HDWalletProvider([process.env.PRIVATE_KEY], "https://testnet.emerald.oasis.dev"),
       network_id: 0xa515,
     },
+    sapphire: {
+      provider: () =>
+        new HDWalletProvider([process.env.PRIVATE_KEY], "https://testnet.sapphire.oasis.dev"),
+      network_id: 0x5aff,
+    },
   },
​
   // Set default mocha options here, use special reporters etc.
```
​
#### 移植到 Sapphire

这就是事情开始变得有趣的地方。我们将用两行代码为这个入门项目添加机密性。

你需要获取 Sapphire 兼容性库 ([@oasisprotocol/sapphire-paratime](https://www.npmjs.com/package/@oasisprotocol/sapphire-paratime))，因此通过发出

```
pnpm add @oasisprotocol/sapphire-paratime # npm also works
```

到目前为止，一切都很好。现在通过将此行添加到 `truffle-config.js` 的顶部来导入它：

```
const sapphire = require('@oasisprotocol/sapphire-paratime');
```
这是代码的第一行，接下来是第二行：
​
```diff
diff --git a/truffle-config.js b/truffle-config.js
index 0cd9d36..7db7cf8 100644
--- a/truffle-config.js
+++ b/truffle-config.js
@@ -60,7 +60,7 @@ module.exports = {
     },
     sapphire: {
       provider: () =>
-        new HDWalletProvider([process.env.PRIVATE_KEY], "https://testnet.sapphire.oasis.dev"),
+        sapphire.wrap(new HDWalletProvider([process.env.PRIVATE_KEY], "https://testnet.sapphire.oasis.dev")),
       network_id: 0x5aff,
     },
   },
```

这个 `wrap` 函数可以使用你拥有的任何类型的提供者或签名者，并将其转换为与 Sapphire 和机密性一起使用的提供者或签名者。

在大多数情况下，封装你的签名者/提供者是让你的 dapp 在 Sapphire 上运行的最少需要做的事情，但这并不一个完整，因为未经修改的合约可能会通过正常操作泄漏状态。

接下来是我们一直期待的事情：

```
> PRIVATE_KEY="0x..." truffle migrate --network sapphire
​
Sent some coins in 0x6dc6774addf4c5c68a9b2c6b5e5634263e734d321f84012ab1b4cbe237fbe7c2.
A Transfer(0x56e5F834F88F9f7631E9d6a43254e173478cE06a, 0x56e5F834F88F9f7631E9d6a43254e173478cE06a, 42) was emitted.
The balance storage slot contains 0x0.
The contract now has balance: 42.
```

所以基本上没有任何改变，这几乎就是我们想要的全部。但是请看一下倒数第二行，它说明了存储插槽中的内容。

以前，它说输出的是“0x2a”，但现在它输出“0x0”。

显然，该槽确实包含数据，否则无法返回合约余额。

这里发生的情况是 Web3 网关没有用于解密存储槽的密钥，因此返回了一个默认值。

事实上，网关甚至没有解密 MKVS 中的密钥所需的密钥； 它可以表明一个存储槽被写入，但不能标明哪个存储槽（尽管它可以通过阅读合约代码做出很好的猜测）。

所以你可以看到保密是有效的，不过最终用户不需要考虑太多。
​
## 创造一个 Sapphire 原生的 dapp

移植现有的 Eth 应用程序很酷，并且已经可以提供诸如保护 MEV 之类的好处。

然而，从头开始考虑机密性可以解锁一些真正新颖的 dapp 并提供 [更高级别的安全性](/dev_support/paratime/Sapphire/为Sapphire创建dapp.md)。

一个利用机密性的简单但有用的 dapp 是 [dead person's switch](https://en.wikipedia.org/wiki/Dead_man's_switch)，如果操作员未能在较长时间内启动，它揭示了一个秘密（假设是数据宝库的加密密钥）。

一起让它成为现实！
​
### 初始化一个 hardhat 项目

1.创建并进入一个新的目录
2.​ 输入 `npx hardhat`创建一个 TypeScript 项目，安装`@nomicfoundation/hardhat-toolbox`及其依赖。
​
### 添加 Sapphire 测试网到 Hardhat

打开你的 `hardhat.config.ts` 并放入这些行。它们会提醒你很多关于 Truffle 运行的情况。
​
```diff
diff --git a/hardhat.config.ts b/hardhat.config.ts
index 414e974..49c95f9 100644
--- a/hardhat.config.ts
+++ b/hardhat.config.ts
@@ -3,6 +3,15 @@ import "@nomicfoundation/hardhat-toolbox";
​
 const config: HardhatUserConfig = {
   solidity: "0.8.9",
+  networks: {
+    sapphire: {
+      chainId: 0x5aff,
+      url: "https://testnet.sapphire.oasis.dev",
+      accounts: [
+        process.env.PRIVATE_KEY ?? Buffer.alloc(0, 32).toString("hex"),
+      ],
+    },
+  },
 };
​
 export default config;
```
​
### 获得合约

这是一个 Sapphire 教程，而且你已经是 Solidity 专家，所以我们不会解释过多合约的细节来让你感到厌烦。

首先将 [Vigil.sol](https://github.com/oasisprotocol/sapphire-paratime/blob/main/examples/hardhat/contracts/Vigil.sol) 粘贴到 `contracts/Vigil.sol` 中。​​​

同时，再将 [run-vigil.ts](https://github.com/oasisprotocol/sapphire-paratime/blob/main/examples/hardhat/scripts/run-vigil.ts) 放入 `scripts/run-vigil.ts`。 我们稍后会需要它。
​
#### Vigil.sol, 有趣的部分
​
关键状态变量是：
​
```solidity
    SecretMetadata[] public _metas;
    bytes[] private _secrets;
```

- `_metas` 被标记为 `public` 可见性，因此尽管状态本身被加密并且不能直接读取，Solidity 会生成一个 getter 来为你进行解密。
- `_secrets` 是 `private`，因此是真正的秘密； 只有合约才能访问此映射中包含的数据。

而我们最关心的方法是：

- `createSecret`，向 `_metas` 和 `_secrets` 添加一个条目。
- `revealSecret`，它作为 `_secrets` 包含的数据的访问控制的 getter。 由于受信任的执行和机密性，揭示秘密的唯一方法是执行一直持续到函数结束并且不恢复。

如果你真的打算使用这个东西，其余的也方法很有用，并且它们证明了为 Sapphire 开发与为 Ethereum 开发基本相同。你甚至可以针对 Hardhat network 'n jazz 编写测试。
​
### 运行合约
​
首先，让我们看看实际发生了什么。

在文件的顶部，有我们的 import：`@oasisprotocol/sapphire-paratime`。

与 Truffle 不同，我们必须“手动”包装签名者，因为 Hardhat 配置只需要一个私钥。 我们在 `main` 的顶部执行此操作。

部署合约后，我们可以创建一个 secret，检查它是否不可读，稍等片刻，然后检查它是否变得可读。那就太酷了！

好吧，让我们“运行”来实现它
​
```
PRIVATE_KEY="0x..." pnpm hardhat run scripts/run-vigil.ts --network sapphire
```

如果你看到类似下面的东西，你就知道你已经走在部署机密 dapps到 Sapphire的正确道路上了。
​
```
Vigil deployed to: 0x74dC4879B152FDD1DDe834E9ba187b3e14f462f1
Storing a secret in 0x13125d868f5fb3cbc501466df26055ea063a90014b5ccc8dfd5164dc1dd67543
Checking the secret
failed to fetch secret: reverted: not expired
Waiting...
Checking the secret again
The secret ingredient is brussels sprouts
```
​
## 搞定！
​
恭喜，你完成了 Sapphire 的教程，谢谢你的到来。

如果你有更多的问题，可以从 [the docs](https://docs.oasis.dev/general/developer-resources/sapphire-paratime) 或者我们的 Discord 社区获得解答。

想获得关于在 Sapphire上编写 dapps 的细节，也可以从 [docs](https://docs.oasis.dev/general/developer-resources/sapphire-paratime/writing-dapps-on-sapphire) 中获得。

祝你未来涉足保密领域好运！
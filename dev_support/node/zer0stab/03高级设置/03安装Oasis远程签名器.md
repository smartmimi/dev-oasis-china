# 03安装Oasis远程签名器

> 提示  如果您打算使用远程签名者设置配置 Oasis 节点，则只需安装 Oasis Remote Signer 二进制文件。

[使用带有远程签名者的 Ledger-backed Consensus Key](https://docs.oasis.dev/general/run-a-node/advanced/using-ledger-backed-consensus-key-with-a-remote-signer)中描述了这种设置的示例。

Oasis Remote Signer 是从[Oasis Core](https://github.com/oasisprotocol/oasis-core)存储库的`[go/oasis-remote-signer`路径](https://github.com/oasisprotocol/oasis-core/tree/master/go/oasis-remote-signer)创建的二进制文件。

它包含实现各种 Oasis Core 签名者的逻辑（即基于分类帐的签名者、基于文件的签名者或通过复合签名者的组合）和一个 gRPC 服务，Oasis 节点可以通过该服务连接到它并从它请求签名。

> 警告  
Oasis Remote Signer 目前仅在 x86_64 Linux 系统上受支持。

## 下载源码发布版本

> 提示  
我们建议您自己从源代码构建 Oasis 远程签名器，以使用远程签名器设置进行 Oasis 节点的生产部署。

为方便起见，我们提供了由 Oasis Protocol Foundation 构建的二进制文件。[网络参数](https://docs.oasis.dev/general/oasis-network/network-parameters)页面中提供了二进制文件的链接。

## 从源码构建

尽管强烈建议，从源代码构建目前超出了本文档的范围。

有关详细信息，请参阅[Oasis Core 的构建环境设置和构建](https://docs.oasis.dev/oasis-core/development-setup/build-environment-setup-and-building)文档。

> 危险  
`[master`分支](https://github.com/oasisprotocol/oasis-core/tree/master/)中的代码可能与主网中其他节点使用的代码不兼容。

确保使用[Network Parameters](https://docs.oasis.dev/general/oasis-network/network-parameters)中指定的版本。

## 将`oasis-remote-signer`二进制文件添加到PATH中

要为当前用户安装`oasis-remote-signer`文件，请将其复制/符号链接到`~/.local/bin`.

要为系统的所有用户安装`oasis-remote-signer`文件，请将其复制到`/usr/local/bin`.
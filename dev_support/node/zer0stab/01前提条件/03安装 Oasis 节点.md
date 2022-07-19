# 安装 Oasis 节点

Oasis 节点是从[Oasis Core](https://github.com/oasisprotocol/oasis-core)库`go/`路径创建的源码文件

It contains both the logic for running an Oasis node and also provides a CLI for handling registry, staking and other operations.

它既包含运行 Oasis 节点的逻辑，也提供了一个 CLI 来处理注册、质押和其他操作。

:::警告

Oasis 节点目前仅在 x86_64 Linux 系统上受支持。

:::

## **下载源码版本**

:::提示

我们建议您自己从源代码构建 Oasis 节点，以用于 Oasis 节点的生产部署。

:::

为方便起见，我们提供了由 Oasis Protocol Foundation 构建的二进制文件。[网络参数](https://docs.oasis.dev/general/oasis-network/network-parameters)页面中提供了源码文件的链接。

## 从源码构建

尽管强烈建议，但从源码构建目前已超出本文档的范围。

有关详细信息，请参阅[Oasis Core 的构建环境设置和构建](https://docs.oasis.dev/oasis-core/development-setup/build-environment-setup-and-building)文档。

:::危险

`[master`分支](https://github.com/oasisprotocol/oasis-core/tree/master/)中的代码可能与主网中其他节点使用的代码不兼容。

确保使用[Network Parameters](https://docs.oasis.dev/general/oasis-network/network-parameters)中指定的版本。

:::

## 将`oasis-node` 源码添加到`PATH`路径

要为当前用户安装`oasis-node`二进制文件，请将其复制/符号链接到`~/.local/bin`。

要为系统的所有用户安装`oasis-node`二进制文件，请将其复制到`/usr/local/bin`。

## 运行ParaTimes

如果您打算[运行 ParaTime 节点](https://docs.oasis.dev/general/run-a-node/set-up-your-node/run-a-paratime-node)，则需要额外安装以下软件包:

- [Bubblewrap](https://github.com/projectatomic/bubblewrap) 0.4.1+, 用于创建进程沙箱.
    
    在 Ubuntu 20.04+ 上，您可以使用以下命令安装:
    
    ```
    sudo apt install bubblewrap
    
    ```
    
    在 Fedora 上，您可以通过以下命令安装:
    
    ```
    sudo dnf install bubblewrap
    
    ```
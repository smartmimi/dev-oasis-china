# 运行ParaTime客户端节点

> 提示  这些说明是为了设置一个ParaTime客户端节点，它只观察ParaTime活动并能提交交易。如果你想运行一个ParaTime节点，请参阅运行ParaTime节点的说明。同样，如果你想运行一个验证器或非验证器节点，请参阅运行验证器节点的说明或运行非验证器节点的说明。

> 提示  
如果你正在寻找一些你可以运行的具体ParaTimes，请看ParaTimes及其参数列表。

> 提示  
Oasis Core在内部将ParaTimes称为运行时间，因此所有配置选项的名称中都有运行时间。

本指南将介绍为Oasis网络设置ParaTime客户端节点的情况。本指南假定有一些关于使用命令行工具的基本知识。

## 前提

在遵循本指南之前，请确保您已遵循[先决条件](https://docs.oasis.dev/general/run-a-node/prerequisites/)和[运行非验证节点](https://docs.oasis.dev/general/run-a-node/set-up-your-node/run-non-validator)部分并具备：

- • 在您的系统上安装和配置 Oasis Node 二进制文件。
- 准备好所选的顶级`/node/`工作目录。除了`etc`和`data`目录，还要准备以下目录：
    - `bin`: 存储 Oasis 节点运行 ParaTimes 所需的二进制文件。
    - `runtimes`: 存储 ParaTime 软件包。

> 提示  
你可以随意命名你的工作目录，例如`/srv/oasis/`。

只要确保在下面的说明中使用正确的工作目录路径。

- 创世文件被复制到 `/node/etc/genesis.json`.

> 提示  
阅读其余的ParaTime节点设置说明也可能是有用的。

> 提示  为了加快启动你的新节点，我们建议从你现有的节点复制节点的状态，或者使用状态同步来同步它。

> 提示  
运行一个ParaTime客户端节点不需要注册实体或其节点。

它也不需要拥有任何质押。

:::tip

为在可信执行环境 (TEE) 中运行的 ParaTime 运行客户端节点不需要在 ParaTime 客户端节点上具有相同的可用 TEE。

例如，为启用 SGX 的 ParaTime（如 Cipher）运行 ParaTime 客户端节点不需要在 ParaTime 客户端节点上安装 SGX。

### The ParaTime 软件包

为了运行 ParaTime 节点，您需要获取需要来自可信来源的 ParaTime bundle。该软件包（通常带有`.orc` 代表 Oasis Runtime Container 的扩展名）包含所有需要的 ParaTime 二进制文件以及标识符和版本元数据，以简化部署。

当 ParaTime 在可信执行环境 (TEE) 中运行时，软件包还将包含所有必需的工件（例如二进制文件的 SGXS 版本和任何飞地签名）。

:::**危险**

与创世文档一样，请确保您从可信赖的来源获得这些文件。

> 警告  
### 从源代码编译ParaTime二进制文件

如果您决定自己从源代码构建 ParaTime 二进制文件，请确保遵循我们[的确定性编译指南，](https://docs.oasis.dev/oasis-sdk/runtime/reproducibility) 以确保您收到完全相同的二进制文件。

### 安装 ParaTime Bundle软件包

对于每个ParaTime，你需要获得它的软件包，并将其安装到你的节点工作目录的runtimes子目录下。

> 提示  例如，对于Cipher ParaTime，你必须获得cipher-paratime.oc bundle并将其安装到`/node/runtimes/cipher-paratime.oc`。

### 安装Bubblewrap 沙盒( 0.3.3版本以上)

ParaTime客户端节点在由Bubblewrap提供的沙盒环境中执行ParaTime二进制文件。为了安装它，请根据你的发行版，遵循以下说明：

<Tabs>
<TabItem value="Ubuntu 18.10+">

```
sudo apt install bubblewrap

```

</TabItem>
<TabItem value="Fedora">

```
sudo dnf install bubblewrap

```

</TabItem>
<TabItem value="other" label="Other Distributions">
在其他系统上，你可以下载最新的[Bubblewrap项目提供的源代码版本](https://github.com/containers/bubblewrap/releases)，然后自己构建。

请确保你的系统中安装了必要的开发工具和libcap开发头文件。在Ubuntu上，你可以用以下方式安装它们：

```
sudo apt install build-essential libcap-dev

```

在获得Bubblewrap源码压缩包，例如bubblewrap-0.4.1.tar.xz后，运行

```
tar -xf bubblewrap-0.4.1.tar.xz
cd bubblewrap-0.4.1
./configure --prefix=/usr
make
sudo make install

```

:::**警告**

请注意，Oasis Node 需要默认安装 Bubblewrap `/usr/bin/bwrap`

</TabItem>
</Tabs>

确保您有足够新的版本：

```
bwrap --version

```

:::**警告**

Ubuntu 18.04 LTS（以及更早的版本）提供了过旧的`bubblewrap`。请关注这些系统的其他发行版部分。

## 配置

为了配置 ParaTime 客户端节点，创建`/node/etc/config.yml`具有以下内容的文件：

```
datadir: /node/data

log:
  level:
    default: info
    tendermint: info
    tendermint/context: error
  format: JSON

genesis:
  file: /node/etc/genesis.json

consensus:
  tendermint:
    p2p:
      # List of seed nodes to connect to.
      # NOTE: You can add additional seed nodes to this list if you want.
      seed:
        - "{{ seed_node_address }}"

runtime:
  mode: client
  paths:
    # Paths to ParaTime bundles for all of the supported ParaTimes.
    - "{{ runtime_orc_path }}"

```

在使用此配置之前，您应该收集以下信息来替换配置文件中存在的变量：

- `{{ seed_node_address }}`: 子节点地址`ID@IP:port`
    - [您可以在网络参数](https://docs.oasis.dev/general/oasis-network/network-parameters)中找到当前的 Oasis 种子节点地址
- `{{ runtime_orc_path }}`: [ParaTime 包](https://docs.oasis.dev/general/run-a-node/set-up-your-node/run-a-paratime-client-node#the-paratime-bundle)`/node/runtimes/foo-paratime.orc`的路径
    - • [您可以在Network Paramers](https://docs.oasis.dev/general/oasis-network/network-parameters#paratimes)中找到当前 Oasis 支持的ParaTimes 。

## 启动Oasis节点

您可以通过运行以下命令来启动节点：

```
oasis-node --config /node/etc/config.yml

```

## 检查节点状态

为确保您的节点与网络正确连接，您可以在节点启动后运行以下命令：

```
oasis-node control status -a unix:/node/data/internal.sock

```
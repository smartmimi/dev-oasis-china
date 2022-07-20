# 运行ParaTime节点

> 提示  
这些说明是为了设置一个ParaTime节点，该节点参与一个或多个ParaTime计算委员会。如果你想运行一个ParaTime客户端节点，请参阅运行ParaTime客户端节点的说明。如果你想运行一个验证器节点，请参阅运行验证器节点的说明。同样地，如果你想运行一个非验证器节点，请看运行非验证器节点的说明。

:::

> 警告  

对于生产设置，我们建议将ParaTime计算/存储节点与验证器节点（如果你运行一个）分开运行。

将ParaTime和验证器节点作为独立的Oasis节点运行，可以防止影响一种节点类型的配置错误和/或（安全）问题影响另一种节点。

:::

> 提示  

如果你正在寻找一些你可以运行的具体ParaTimes，请看ParaTimes及其参数列表。

:::

> 提示  

Oasis Core在内部将ParaTimes称为运行时间，因此所有配置选项的名称中都有运行时间。

:::

本指南将涵盖为Oasis网络设置你的ParaTime计算节点。本指南假定了一些关于使用命令行工具的基本知识。

## 前提

在遵循本指南之前，请确保你已经遵循了先决条件和运行非验证器节点部分，并且已经。

- 在您的系统上安装和配置了Oasis Node二进制。
- 选择的顶层/node/工作目录准备好了。除了etc和data目录外，还要准备以下目录。
    - `bin`: 这将存储Oasis Node运行ParaTimes所需的二进制文件。
    - `runtimes`: 这将存储ParaTime的捆绑数据

> 提示  

你可以随意命名你的工作目录，例如/srv/oasis/。

只要确保在下面的说明中使用正确的工作目录路径。

:::

- 创世文件被复制到`/node/etc/genesis.json`。

> 提示  

阅读其余的验证器节点设置说明也可能是有用的。

:::

> 提示  
为了加快启动你的新节点，我们建议从你现有的节点复制节点的状态，或者使用状态同步来同步它。

:::

### 质押需求

为了能够在绿洲网络上注册成为ParaTime节点，你需要在你的实体托管账户上有足够的代币押注。

当前对特定ParaTime的最低赌注要求列在 "对网络的贡献 "部分 - 运行ParaTime节点页面。如果你想手动检查其他节点角色和已注册的ParaTime的分红要求，请使用Oasis CLI工具，如通用分红信息中所述。

最后，要质押代币，请使用我们的[Oasis CLI 工具](https://docs.oasis.dev/general/manage-tokens/advanced/oasis-cli-tools/delegate-tokens)。[如果一切设置正确，在为您的实体帐户运行Oasis Node Stake Account Info](https://docs.oasis.dev/general/manage-tokens/advanced/oasis-cli-tools/get-account-info)命令时，您应该会看到如下内容（这是测试网的示例）：

```
Balance:
  Total: 0.0 TEST
  Available: 0.0 TEST

Active Delegations to this Account:
  Total: 20000.0 TEST (20000000000000 shares)
  Delegations:
    - From:   oasis1qrwdwxutpyr9d2m84zh55rzcf99aw0hkts7myvv9
      Amount: 20000.0 TEST (20000000000000 shares)

Stake Accumulator:
  Claims:
    - Name: registry.RegisterEntity
      Staking Thresholds:
        - Global: entity
    - Name: registry.RegisterNode.HG5TB3dbY8gtYBBw/R/cHfPaOpe0vT7W1wu/2rtpk/A=
      Staking Thresholds:
        - Global: node-compute
      Staking Thresholds:
        - Global: node-storage

Nonce: 1

```

:::**警告**

股权要求可能因 ParaTime 和 ParaTime 的不同而有所不同，并且将来可能会发生变化。

:::

### 注册一个新的实体 或 更新您的实体

如果您还没有实体，请按照[创建您的实体](https://docs.oasis.dev/general/run-a-node/set-up-your-node/run-validator#creating-your-entity)说明创建一个新实体。

:::**危险**

本节中的所有内容都应在 上完成，`localhost`因为将创建敏感项目。

:::

如果您将在新的 Oasis 节点上运行 ParaTime，请按照[初始化节点](https://docs.oasis.dev/general/run-a-node/set-up-your-node/run-validator#initializing-a-node)说明初始化新节点。

然后通过在--entity.node.descriptor标志中列举所有节点的描述符（现有的和新的）的路径来更新你的实体描述符。比如说。

```
oasis-node registry entity update \\
    ... various signer flags ... \\
    --entity.node.descriptor /localhost/node1/node_genesis.json,/localhost/node2/node_genesis.json

```

:::**信息**

要确认所有节点都已添加到您的实体描述符中，请运行：

```
cat <PATH-TO-entity.json>

```

确保您在`"nodes"`列表下看到所有节点的 ID:

```
{
  "v": 2,
  "id": "QTg+ZjubD/36egwByAIGC6lMVBKgqo7xnZPgHVoIKzc=",
  "nodes": [
    "yT1h8/eN0VInQxn3YKcHxvSgGcsjsTSYmdTLZZMBTWI=",
    "HG5TB3dbY8gtYBBw/R/cHfPaOpe0vT7W1wu/2rtpk/A="
  ]
}

```

:::

然后按照生成实体注册交易的说明，通过实体注册交易生成并提交新的/更新的实体描述符。

> 警告  

确保你的实体描述符（即entity.json）被复制到你的在线服务器，并保存为/node/entity/entity.json，以确保节点的配置能找到它。

:::

> 提示  

你将通过worker.registration.entity配置标志，将节点配置为自动注册它所启用的角色（即存储和计算角色）。

不需要手动注册节点。

:::

> 提示  
运行计算节点的ParaTime奖励将被发送到你在ParaTime内的实体地址。要获取共识层的奖励，你需要先提取它们。阅读向/从ParaTime存入/提取代币章节以了解更多。

:::

### ParaTime软件包

为了运行ParaTime节点，你需要获得ParaTime软件包，该软件包需要来自一个可信的来源。软件包（通常以.oc为扩展名，代表Oasis Runtime Container）包含所有需要的ParaTime二进制文件，以及标识符和版本元数据，以方便部署。

当ParaTime在可信执行环境（TEE）中运行时，捆绑包也将包含所有需要的工件（例如二进制的SGXS版本和任何飞地签名）。

> 危险  
与创世文件一样，确保你从一个可信赖的来源获得这些文件。

:::

:::caution

### 从源代码编译ParaTime二进制文件

如果你决定自己从源代码构建ParaTime二进制文件，请确保你遵循我们的确定性编译指南，以确保你收到完全相同的二进制文件。

当ParaTime在TEE中运行时，与在共识层中注册的不同的二进制将无法工作，并将被网络拒绝。

:::

### 安装Oasis 核心Runtime Loader

对于在英特尔SGX可信执行环境中运行的ParaTimes，你需要安装Oasis Core Runtime Loader。

Oasis Core Runtime Loader二进制文件（oasis-core-runtime-loader）是Oasis Core二进制发布的一部分，所以请确保你下载指定网络参数页面的适当版本。

把它安装到你的节点工作目录的bin子目录下，例如/node/bin/oasis-core-runtime-loader。

### 安装ParaTime软件包

对于每个ParaTime，你需要获得它的捆绑包，并将其安装到你的节点工作目录的runtimes子目录下。

> 提示  
例如，对于Cipher ParaTime，你必须获得cipher-paratime.oc bundle并将其安装到/node/runtimes/cipher-paratime.oc。

:::

### 安装Bubblewrap沙盒（至少是0.3.3版本）。

ParaTime计算节点在由Bubblewrap提供的沙盒环境中执行ParaTime二进制文件。为了安装它，请根据你的发行版，遵循以下说明。

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
<TabItem value="Other Distributions">
在其他系统上，你可以下载最新的[Bubblewrap项目提供的源代码版本](https://github.com/containers/bubblewrap/releases)，然后自己构建。

请确保你的系统中安装了必要的开发工具和libcap开发头文件。在Ubuntu上，你可以用以下方式安装它们：

```
sudo apt install build-essential libcap-dev

```

在获得Bubblewrap源码压缩包，例如bubblewrap-0.4.1.tar.xz后，运行:

```
tar -xf bubblewrap-0.4.1.tar.xz
cd bubblewrap-0.4.1
./configure --prefix=/usr
make
sudo make install

```

> 警告  

注意，Oasis Node希望Bubblewrap默认安装在`/usr/bin/bwrap`下。

:::
</TabItem>
</Tabs>

确保你有一个足够的新版本：

```
bwrap --version

```

> 警告  

Ubuntu 18.04 LTS（以及更早的版本）提供了过旧的`bubblewrap`。请关注这些系统的其他发行版部分。

:::

### 设置可信的执行环境（TEE）

如果 ParaTime 需要使用 TEE，请确保按照[设置可信执行环境 (TEE)](https://docs.oasis.dev/general/run-a-node/prerequisites/set-up-trusted-execution-environment-tee)文档中的说明设置 TEE.

## 配置

为了配置节点，创建/node/etc/config.yml文件，内容如下:

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
    core:
      listen_address: tcp://0.0.0.0:26656

      # The external IP that is used when registering this node to the network.
      # NOTE: If you are using the Sentry node setup, this option should be
      # omitted.
      external_address: tcp://{{ external_address }}:26656

    p2p:
      # List of seed nodes to connect to.
      # NOTE: You can add additional seed nodes to this list if you want.
      seed:
        - "{{ seed_node_address }}"

runtime:
  mode: compute
  paths:
    # Paths to ParaTime bundles for all of the supported ParaTimes.
    - "{{ runtime_orc_path }}"

  # The following section is required for ParaTimes which are running inside the
  # Intel SGX Trusted Execution Environment.
  sgx:
    loader: /node/bin/oasis-core-runtime-loader

worker:
  registration:
    # In order for the node to register itself, the entity.json of the entity
    # used to provision the node must be available on the node.
    entity: /node/entity/entity.json

  p2p:
    # External P2P configuration.
    port: 30002
    addresses:
      # The external IP that is used when registering this node to the network.
      - "{{ external_address }}:30002"

# The following section is required for ParaTimes which are running inside the
# Intel SGX Trusted Execution Environment.
ias:
  proxy:
    address:
      # List of IAS proxies to connect to.
      # NOTE: You can add additional IAS proxies to this list if you want.
      - "{{ ias_proxy_address }}"

```

在使用这个配置之前，你应该收集以下信息，以取代配置文件中存在的变量：

- `{{ external_address }}`: 注册该节点时使用的外部IP
- `{{ seed_node_address }}`: 种子节点地址`ID@IP:port`
    - [您可以在网络参数](https://docs.oasis.dev/general/oasis-network/network-parameters)中找到当前的 Oasis 种子节点地址
- `{{ runtime_orc_path }}`: [ParaTime 包](https://docs.oasis.dev/general/run-a-node/set-up-your-node/run-a-paratime-node#the-paratime-bundle)`/node/runtimes/foo-paratime.orc`的路径
    - [您可以在Network Paramers](https://docs.oasis.dev/general/oasis-network/network-parameters#paratimes)中找到当前 Oasis 支持的ParaTimes.
- `{{ ias_proxy_address }}`: IAS 代理地址`ID@HOST:port`
    - [您可以在Network Parameters](https://docs.oasis.dev/general/oasis-network/network-parameters)中找到当前的 Oasis IAS 代理地址
    - 如果需要，您还可以[运行自己的 IAS 代理](https://docs.oasis.dev/general/run-a-node/set-up-your-node/run-an-ias-proxy)

> 警告  

确保worker.p2p.port（默认：9200）端口在互联网上是公开的，并且可以公开访问（用于TCP流量）。

:::

## 启动Oasis节点

你可以通过运行以下命令来启动该节点:

```
oasis-node --config /node/etc/config.yml

```

## 检查节点状态

为了确保你的节点与网络正确连接，你可以在节点启动后运行以下命令：

```
oasis-node control status -a unix:/node/data/internal.sock

```

## 故障排除

在继续进行 ParaTime 节点特定的故障排除之前，请参阅一般[节点故障排除](https://docs.oasis.dev/general/run-a-node/troubleshooting)和[设置 TEE 故障排除部分。](https://docs.oasis.dev/general/run-a-node/prerequisites/set-up-trusted-execution-environment-tee#troubleshooting)

### Bubblewrap版本过旧

仔细检查您安装的`bubblewrap`版本，并确保至少是**0.3.3**版本。本指南的先前版本未明确提及所需版本。有关详细信息，请参阅[安装 Bubblewrap 沙箱](https://docs.oasis.dev/general/run-a-node/set-up-your-node/run-a-paratime-node#install-bubblewrap-sandbox-at-least-version-0-3-3)部分。

### 质押需求

仔细检查您的节点实体是否满足 ParaTime 节点的质押要求。有关详细信息，请参阅[权益要求](https://docs.oasis.dev/general/run-a-node/set-up-your-node/run-a-paratime-node#stake-requirements)部分。
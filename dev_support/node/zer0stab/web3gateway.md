# Web3 网关

用于 EVM ParaTime 的 Oasis Web3 网关

本指南将引导您完成为 EVM 兼容的 ParaTimes（例如Emerald和Sapphire ）设置 Oasis Web3 网关所需的步骤。

> 每个 ParaTime 都需要自己的 Web3 网关实例！

## 先决条件

除了运行 Oasis 节点的最低硬件要求外，还应添加以下内容以运行 Web3 网关：

- 中央处理器：
    - 最低：2.0 GHz x86-64 CPU
    - 推荐：2.0 GHz x86-64 CPU，2 核/vCPU
- 内存：
    - 最低要求：4 GB ECC RAM
    - 推荐：8 GB ECC RAM
- 硬盘：
    - 最低要求：300 GB SSD 或 NVMe 快速存储
    - 推荐：500 GB SSD 或 NVMe 快速存储

> 从上面的数据来看，使用 PostgreSQL 的 Emerald 的 Web3 网关在 2021 年 11 月 18 日至 2022 年 4 月 11 日（自Emerald 主网启动以来）的约 5 个月内遇到了210 GB的数据库增长。

## Oasis ParaTime 客户端节点

Web3 网关需要本地部署的支持 ParaTime 的 Oasis 节点。首先，遵循 Oasis ParaTime 客户端节点 指南，了解如何使用一个或多个 ParaTime 配置 Oasis 客户端节点。始终使用在Mainnet或Testnet的网络参数页面上发布的 Oasis 节点/ParaTime 版本的精确组合。

除了发生在链上并产生一些影响的交易外，还有一些在 Oasis 协议和 EVM 中实现的只读查询。其中一些可能非常消耗资源，例如模拟 EVM 调用，并且默认情况下被禁用以避免 DDOS 攻击。如果您的 Oasis 节点实例仅供您和您的 Web3 网关使用，您可以 通过将以下内容添加到您的 Oasis 节点配置来安全地启用昂贵的事务：

配置yml

```
# ... sections not relevant are omitted ...
runtime:
  mode: client
  paths:
    - {{ emerald_bundle_path }}
    - {{ sapphire_bundle_path }}

  config:
    "{{ emerald_paratime_id }}":
      estimate_gas_by_simulating_contracts: true
      allowed_queries:
        - all_expensive: true
    "{{ sapphire_paratime_id }}":
      estimate_gas_by_simulating_contracts: true
      allowed_queries:
        - all_expensive: true

```

`{{ ... }}`在上面的配置中，用实际的 ParaTime ID替换占位符：

- `{{ emerald_paratime_id }}`：
    - 主网上的翡翠：`000000000000000000000000000000000000000000000000e2eaa99fc008f87f`
    - 测试网上的翡翠：`00000000000000000000000000000000000000000000000072c8215e60d5bca7`
- `{{ sapphire_paratime_id }}`：
    - 测试网上的蓝宝石：`000000000000000000000000000000000000000000000000a6d1e3ebf60dff6c`

## PostgreSql

Web3 网关将区块链数据存储在 PostgreSQL数据库版本13.3或更高版本中。按照特定于您的操作系统和环境的说明进行安装。

因为每个 ParaTime 都需要它自己的 Web3 网关实例，所以您必须为每个 Web3 实例创建一个单独的数据库和一个单独的用户。

## 下载 Oasis Web3 网关

检查您将部署到的网络所需的 Web3 网关版本：Mainnet、Testnet。接下来，从[官方 GitHub 存储库](https://github.com/oasisprotocol/emerald-web3-gateway/releases)下载 Oasis 提供的二进制文件。

或者，您可以下载源版本并自行编译。有关更多信息，请参阅[README.md](https://github.com/oasisprotocol/emerald-web3-gateway/blob/main/README.md#building-and-testing)文件。

## 运行 Web3 网关

gateway.yml

```
# Uncomment the runtime_id below.
runtime_id: {{ paratime_id }}
# Path to your internal.sock file in the root Oasis node datadir.
node_address: "unix:{{ oasis_node_unix_socket }}"

# By default, we index the entire blockchain history.
# If you are low on disk space or you use the gateway just for submitting transactions, enable
# pruning below.
enable_pruning: false
pruning_step: 100000
indexing_start: 0

log:
  level: debug
  format: json

database:
  # Change host and port, if PostgreSQL is running somewhere else.
  host: "127.0.0.1"
  port: 5432
  # Enter your database name, username and password.
  db: {{ postgresql_db }}
  user: {{ postgresql_user }}
  password: {{ postgresql_password }}
  dial_timeout: 5
  read_timeout: 10
  write_timeout: 5
  max_open_conns: 0

gateway:
  chain_id: {{ chain_id }}
  http:
    # Change host to your external IP address if you have users accessing Web3 from the outside.
    host: "localhost"
    # Use different port for each Web3 gateway instance, if all run locally.
    port: 8545
    cors: ["*"]
  ws:
    # Change host to your external IP address if you have users accessing Web3 from the outside.
    host: "localhost"
    # Use different port for each Web3 gateway instance, if all run locally.
    port: 8546
    origins: ["*"]
  method_limits:
    get_logs_max_rounds: 100

```

使用以下占位符值：

- `{{ paratime_id }}`：您正在为其配置 Web3 网关的翡翠或蓝宝石 ParaTime 的 ID（见上文）。
- `{{ oasis_node_unix_socket }}``internal.sock`：由 Oasis 节点创建的文件的路径。
- `{{ postgresql_db }}`, `{{ postgresql_user }}`, `{{ postgresql_password }}`: PostgreSQL 数据库的数据库名称和凭据。
- `{{ chain_id }}`: 你的 EVM 网络的链 ID：
    - 主网上的翡翠：`42262`
    - 测试网上的翡翠：`42261`
    - 测试网上的蓝宝石：`23295`

> 所有配置设置也可以通过环境变量进行设置。例如，您可以导出而不是在上面的配置文件中设置数据库密码：`DATABASE__PASSWORD=your_password_here`

要启动 Web3 网关调用：

```
./emerald-web3-gateway --config gateway.yml
```

Web3 网关将连接到您的 Oasis 节点并开始索引可用块（即从上次网络升级开始）。这可能——取决于你的硬件和区块链的大小——需要几个小时。

如果您的数据库包含由以前版本的 Web3 网关填充的任何表，迁移脚本将在启动时自动应用。

如果要单独迁移数据库，请运行：

```
./emerald-web3-gateway migrate-db --config gateway.yml
```

> 上面，我们`emerald-web3-gateway`直接从 shell 调用该过程，因此您可以快速开始使用它。如果您正在设置生产环境，则应将 Web3 网关配置为系统服务，并将其注册到您平台的服务管理器中。

## 归档 Web3 网关

每个 Oasis Web3 网关只能连接和同步来自单个 Oasis 节点实例的块。要启用对旧 EVM 块的访问，您可以将 Web3 网关配置为代理到另一个（存档）Web3 网关实例。

首先，设置Oasis 归档节点的实例。然后，像往常一样重复设置 Web3 网关的类似过程，但将其配置为使用新设置的 Oasis 存档节点。

假设 Web3 网关和 Oasis 节点的存档实例已启动并正在运行，并且存档 Web3 网关正在侦听本地端口8543。通过将以下内容添加到您的（实时）Web3 网关配置并重新启动它来启用历史块的代理：

gateway.yml

```
# URI of an archive web3 gateway instance for servicing historical queries.
archive_uri: 'http://localhost:8543'
```

如果查询需要有关未存储在 Web3 网关实时版本中的块的信息，网关会将查询传递给配置的存档实例并返回获得的结果。

> 不支持历史估算 gas 调用。

## 故障排除
### 擦除状态以重新索引

如果您遇到数据库或硬件问题，您可能需要擦除数据库并重新索引所有块。首先，运行truncate-db子命令：

```
emerald-web3-gateway truncate-db --config gateway.yml --unsafe
```

然后，emerald-web3-gateway正常执行以开始重新索引块。

> 危险！这将擦除 PostgreSQL 数据库中的所有现有状态，并可能在 Web3 网关重新索引块时导致停机时间延长。

> 2022/10/25 - moi
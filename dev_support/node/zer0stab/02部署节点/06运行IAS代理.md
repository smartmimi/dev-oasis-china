# 运行IAS代理

本指南将介绍为Oasis网络设置英特尔认证服务（IAS）代理节点的情况。本指南假定了一些关于使用命令行工具的基本知识。

## 前提

在遵循本指南之前，请确保你已经遵循先决条件部分，并在你的系统上安装了Oasis Node二进制。IAS代理连接到一个Oasis节点，所以请确保你首先有一个正在运行的节点。更多细节，请参阅如何运行一个非验证器节点的说明。

### 获得IAS服务提供商ID（SPID）和API密钥

运行英特尔认证服务（IAS）代理需要访问IAS的API。进入 IAS 强化隐私 ID（EPID）认证页面，注册生产访问。作为服务提供者，你将注册你的TLS证书，并获得你的服务提供者ID（SPID）和API密钥。SPID和API密钥将被IAS代理使用，以与IAS通信。

> 提示  
建议对SGX远程认证有基本了解。请参阅英特尔的远程认证端到端实例，了解简短的实用介绍。

:::

### **创建工作目录**

我们将使用以下工作目录`/node/ias`（请随意为你的目录命名）。

- 目录的权限应该是`rwx------`。

请使用以下命令创建目录：:

```
mkdir -m700 -p /node/ias

```

## 配置

为了避免在Oasis Node配置中直接指定IAS服务提供商ID（SPID）和API密钥，IAS Proxy支持从环境变量中读取SPID和API密钥。请确保你设置了以下环境变量：

```
OASIS_IAS_SPID="<your-SPID>"
OASIS_IAS_APIKEY="<your-API-key>"

```

为了配置IAS代理，创建`/node/ias/config.yml`文件，内容如下：

```
datadir: /node/ias

log:
  level:
    default: info
  format: JSON

address: unix:{{ oasis_node_socket }}

grpc:
  port: 8650

```

在使用这个配置之前，你应该收集以下信息，以取代配置文件中存在的变量：

- `{{ oasis_node_socket }}`: 运行中的客户端Oasis Node套接字的路径。

## 启动IAS代理

您可以使用以下命令启动 IAS 代理：

```
oasis-node ias proxy --config /node/ias/config.yml

```

## IAS代理公钥

连接到 IAS 代理所需的 TLS 公钥位于已配置`datadir`（例如`/node/ias`）中。

### **共享 IAS 代理地址[](https://docs.oasis.dev/general/run-a-node/set-up-your-node/run-an-ias-proxy#share-ias-proxy-address-)**

[ParaTime 节点](https://docs.oasis.dev/general/run-a-node/set-up-your-node/run-a-paratime-node)现在可以通过在配置中指定它来使用您的 IAS 代理，例如：

```
--ias.proxy.address <IAS_PROXY_PUBLIC_KEY>@<EXTERNAL_IP>:8650

```
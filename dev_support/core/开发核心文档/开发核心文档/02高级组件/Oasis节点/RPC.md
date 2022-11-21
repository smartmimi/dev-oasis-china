# RPC

Oasis Node 公开了一个 RPC 接口，以使外部应用程序能够查询当前的共识和运行时状态、提交事务等。

RPC 接口仅通过位于节点数据目录中的名为 `internal.sock` 的 AF_LOCAL 套接字公开。 这个接口不应该直接暴露在网络上，因为它没有身份验证并且允许完全控制，包括关闭节点。

为了支持远程客户端和不同的协议（例如 REST），应该使用处理身份验证和速率限制等事情的网关。

INFO

此类网关的一个示例是 Oasis Core Rosetta 网关，它通过 Rosetta API 公开共识层的子集。

## 协议

与 Oasis Core 的其他部分一样，Oasis Node 公开的 RPC 接口使用带有 CBOR 编解码器的 gRPC 协议（而不是 Protocol Buffers）。 如果您的应用程序是用 Go 编写的，您可以使用 Oasis Core 提供的便捷 gRPC 包装器来创建客户端。 查看 Oasis SDK 了解更多信息。

例如，要创建一个 gRPC 客户端连接到本地节点在 `/path/to/datadir/internal.sock` 上公开的 Oasis 节点端点，您可以执行以下操作：

```
import (
    // ...
    oasisGrpc "github.com/oasisprotocol/oasis-core/go/common/grpc"
)

// ...

conn, err := oasisGrpc.Dial("unix:/path/to/datadir/internal.sock")

```

这将自动处理设置所需的 gRPC 拨号选项以设置 CBOR 编解码器和错误映射拦截器。 有关 gRPC 帮助程序的更多详细信息，请参阅 API 文档。

## 错误

我们使用特定约定来提供有关处理 gRPC 请求时发生的确切错误的更多信息。 有关详细信息，请参阅 gRPC 详细信息部分。

## 服务

我们使用与 `gRPC over Protocol Buffers` 相同的服务方法命名空间约定。 所有 Oasis Core 服务都有以 oasis-core 开头的唯一标识符。 后跟服务标识符。 单斜杠 (/) 用作方法名称中的分隔符，例如 `/oasis-core.NodeControl/IsSynced`。

公开了以下 gRPC 服务（带有 API 文档的链接）：

- 通用
    - [Node Control](https://pkg.go.dev/github.com/oasisprotocol/oasis-core/go/control/api?tab=doc#NodeController) (`oasis-core.NodeController`)
- 共识层
    - [Consensus (client subset)](https://pkg.go.dev/github.com/oasisprotocol/oasis-core/go/consensus/api?tab=doc#ClientBackend) (`oasis-core.Consensus`)
    - [Consensus (light client subset)](https://pkg.go.dev/github.com/oasisprotocol/oasis-core/go/consensus/api?tab=doc#LightClientBackend) (`oasis-core.ConsensusLight`)
    - [Staking](https://pkg.go.dev/github.com/oasisprotocol/oasis-core/go/staking/api?tab=doc#Backend) (`oasis-core.Staking`)
    - [Registry](https://pkg.go.dev/github.com/oasisprotocol/oasis-core/go/registry/api?tab=doc#Backend) (`oasis-core.Registry`)
    - [Scheduler](https://pkg.go.dev/github.com/oasisprotocol/oasis-core/go/scheduler/api?tab=doc#Backend) (`oasis-core.Scheduler`)
    - [RootHash](https://pkg.go.dev/github.com/oasisprotocol/oasis-core/go/roothash/api?tab=doc#Backend) (`oasis-core.RootHash`)
    - [Governance](https://pkg.go.dev/github.com/oasisprotocol/oasis-core/go/governance/api?tab=doc#Backend) (`oasis-core.Governance`)
    - [Beacon](https://pkg.go.dev/github.com/oasisprotocol/oasis-core/go/beacon/api?tab=doc#Backend) (`oasis-core.Beacon`)
- 运行时层
    - [Storage](https://pkg.go.dev/github.com/oasisprotocol/oasis-core/go/storage/api?tab=doc#Backend) (`oasis-core.Storage`)
    - [Runtime Client](https://pkg.go.dev/github.com/oasisprotocol/oasis-core/go/runtime/client/api?tab=doc#RuntimeClient) (`oasis-core.RuntimeClient`)

有关公开服务的更多详细信息，请参阅相应的文档部分。 Go API 还为所有服务提供了 gRPC 客户端实现，这些服务可以在通过内部套接字建立 gRPC 连接后使用（多个客户端可以共享同一个 gRPC 连接）。 例如，在共识服务使用我们在上一个示例中建立的连接的情况下：

```
import (
    // ...    consensus "github.com/oasisprotocol/oasis-core/go/consensus/api"
    )
    // ...
cc := consensus.NewConsensusClient(conn)
err := cc.SubmitTx(ctx, &tx)
```
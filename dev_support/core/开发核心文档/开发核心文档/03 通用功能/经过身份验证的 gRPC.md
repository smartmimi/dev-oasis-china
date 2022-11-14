# 经过身份验证的 gRPC

Oasis Core 节点之间通过各种协议进行通信。 其中一种协议是 [gRPC](https://grpc.io/)，目前用于以下用途：

- 计算节点与存储节点通信。
- 计算节点与关键管理器节点对话。
- 密钥管理器节点与其他密钥管理器节点对话。
- 客户端与计算节点交谈。
- 客户与关键管理器节点交谈。

所有这些通信都可以附加访问控制策略，指定允许谁在什么时间点执行某些操作。 这首先需要一种身份验证机制。

## TLS[](https://docs.oasis.io/core/authenticated-grpc#tls)

为了对连接的两端进行身份验证，gRPC 总是与 TLS 一起使用。 但是，由于这是一个分散的网络，因此在两个节点之间建立 TLS 会话时，有一些关于如何执行对等验证的细节。

我们不依赖证书颁发机构，而是使用共识层提供的注册服务。 每个节点都会在注册表中发布自己的可信公钥，作为其签名节点描述符的一部分。 TLS 会话使用自己的临时 Ed25519 密钥对，用于（自）签署节点的 X509 证书。 在验证对等身份时，证书上的公钥会与注册表中发布的公钥进行比较。

所有 TLS 密钥都是临时的，并且鼓励节点经常轮换它们（此存储库中的 Oasis Core 实现自动支持这一点）。

有关如何执行证书验证的详细信息，请参阅  `[go/common/crypto/tls](https://github.com/oasisprotocol/oasis-core/tree/master/go/common/crypto/tls)`中的 `VerifyCertificate` 实现。

## gRPC[](https://docs.oasis.io/core/authenticated-grpc#grpc)

Oasis Core 使用了一些与最常见的 gRPC 设置不同的特定约定，这些约定将在以下部分中进行描述。

### CBOR 编解码器

虽然 gRPC 最常与 Protocol Buffers 编解码器一起使用，但 gRPC 协议与实际的底层序列化格式无关。 Oasis Core 使用 [CBOR](https://docs.oasis.io/core/encoding) 对我们 gRPC 服务中使用的所有消息进行编码。

这要求在设置连接时明确配置编解码器。 我们的 [gRPC helpers](https://pkg.go.dev/github.com/oasisprotocol/oasis-core/go/common/grpc?tab=doc) 会自动配置正确的编解码器，因此使用它应该是透明的。 此设置的唯一怪癖是 service codegen 不适用于任意编解码器，因此需要手动生成服务器和客户端的胶水代码（例如，请参阅各种 `api` 包中的`grpc.go`文件） .

### 错误

由于 gRPC 以一些定义的错误代码的形式提供非常有限的错误报告能力，我们扩展了这种机制以支持正确的错误重新映射。

详细错误作为 [gRPC 错误详细信息结构](https://pkg.go.dev/google.golang.org/genproto/googleapis/rpc/status?tab=doc#Status) 的一部分返回。 第一个详细信息元素的“值”字段包含以下指定（命名空间）错误的 CBOR 序列化结构：

```
type grpcError struct {
    Module string `json:"module,omitempty"`
    Code   uint32 `json:"code,omitempty"`
}

```

如果您使用提供的 gRPC 帮助程序，任何错误都会自动映射到已注册的错误类型。

### 服务命名规范

我们使用与 gRPC over Protocol Buffers 相同的服务方法命名空间约定。 所有 Oasis Core 服务都有以 `oasis-core` 开头的唯一标识符。 后跟服务标识符。 单斜杠 (/) 用作方法名称中的分隔符，例如 `/oasis-core.Storage/SyncGet`。
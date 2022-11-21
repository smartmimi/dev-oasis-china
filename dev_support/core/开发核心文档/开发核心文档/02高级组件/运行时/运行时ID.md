# 运行时 ID

运行时的标识符由[common.Namespace](https://pkg.go.dev/github.com/oasisprotocol/oasis-core/go/common?tab=doc#Namespace)类型表示。

前64位保留用于指定表达运行时各种属性的标志，最后192位作为运行时的标识符。

目前定义了以下标志（比特位置假定标志向量被解释为一个无符号的64比特大恩典整数）。

- 第63位：该运行时是一个测试运行时，不用于生产网络。
- 第62位：该运行时是一个密钥管理器运行时。
- 第61-0位。保留给未来的扩展，必须设置为0。

注意：除非注册表的共识参数 "DebugAllowTestRuntimes "被设置，否则试图注册一个测试运行时将被拒绝。
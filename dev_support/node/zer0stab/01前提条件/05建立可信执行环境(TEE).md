# 建立可信执行环境（TEE）

> 提示  如果要运行的ParaTime不需要使用TEE（例如Intel SGX），可以跳过设置TEE。

如果ParaTime被配置为在TEE中运行（目前只有Intel SGX），你必须确保你的系统支持运行SGX enclave。这要求你的硬件有SGX支持，SGX支持被启用，并且额外的驱动和软件组件被正确安装和运行。

## 确保时钟同步

由于运行时 enclave 中的额外健全性检查，您应该确保节点的本地时钟是同步的（例如使用 NTP）。否则，可能会遇到意外的运行时中止。

## 安装SGX Linux驱动程序

> 提示  如果您运行的是 Linux 内核版本 5.11 或更高版本，则已包含所需的 SGX 驱动程序，无需额外安装，可跳过本节。

如在较旧的发行版上安装，请参见下文 [旧版驱动程序](https://github.com/intel/linux-sgx-driver)的安装说明。

### Ubuntu 18.04/16.04

在 Ubuntu 18.04/16.04 系统上安装 SGX Linux 驱动程序的一种便捷方法是使用[Fortanix](https://edp.fortanix.com/docs/installation/guide/)的 APT 存储库及其[DKMS](https://en.wikipedia.org/wiki/Dynamic_Kernel_Module_Support) 包。

首先将 Fortanix 的 APT 存储库添加到您的系统：

```
echo "deb <https://download.fortanix.com/linux/apt> xenial main" | sudo tee /etc/apt/sources.list.d/fortanix.list >/dev/null
curl -sSL "<https://download.fortanix.com/linux/apt/fortanix.gpg>" | sudo -E apt-key add -

```

然后安装`intel-sgx-dkms`包：

```
sudo apt update
sudo apt install intel-sgx-dkms

```

> 警告  某些[Azure 机密计算实例](https://docs.microsoft.com/en-us/azure/confidential-computing/quick-create-portal) 预装 了[Intel SGX DCAP 驱动程序。](https://github.com/intel/SGXDataCenterAttestationPrimitives/tree/master/driver/linux)  
请运行`dmesg | grep -i sgx`确定并观察是否显示，如下所示的行  
[    4.991649] sgx: intel_sgx: Intel SGX DCAP Driver v1.33  
如果是这种情况，您需要通过运行以下命令将英特尔 SGX DCAP 驱动程序模块列入黑名单：  
echo "blacklist intel_sgx" | sudo tee -a /etc/modprobe.d/blacklist-intel_sgx.conf >/dev/null

### Fedora 34/33

在 Fedora 34/33 系统上安装 SGX Linux 驱动程序的一种便捷方法是使用 Oasis 提供[的用于旧版 Intel SGX Linux 驱动程序的 Fedora 软件包](https://github.com/oasisprotocol/sgx-driver-kmod)。

### 其他系统

转到[英特尔 SGX 下载](https://01.org/intel-software-guard-extensions/downloads) 页面并找到最新的“英特尔 SGX Linux 版本”（*不是*“英特尔 SGX DCAP 版本”）并为您的发行版下载“英特尔 (R) SGX 安装程序”。该包将具有`driver`名称（例如，`sgx_linux_x64_driver_2.11.0_2d2b795.bin`）。

### 验证

安装驱动程序并重新启动系统后，确保其中一个 SGX 设备存在（确切的设备名称取决于正在使用的驱动程序）：

- `/dev/sgx_enclave` (自Linux 内核5.11起)
- `/dev/isgx` (旧版本驱动)

## 确保`/dev`没有用`noexec`选项挂载

一些 Linux 发行版`/dev`使用`noexec`mount 选项进行挂载。如果是这种情况，它将阻止 enclave 加载器映射可执行页面。

确保您的`/dev`(ie `devtmpfs`) 未安装该`noexec`选项。要检查这一点，请使用:

```
cat /proc/mounts | grep devtmpfs

```

要暂时删除 的`noexec`挂载选项`/dev`，请运行:

```
sudo mount -o remount,exec /dev

```

要永久删除 的`noexec`挂载选项`/dev`，请将以下内容添加到系统`/etc/fstab`文件中:

```
devtmpfs        /dev        devtmpfs    defaults,exec 0 0

```

> 提示  这是推荐的修改虚拟（即API）文件系统挂载选项的方法，详见[systemd](https://www.freedesktop.org/wiki/Software/systemd/APIFileSystems/)的API文件系统文档。

## 安装AESM服务

为了允许执行SGX enclaves，涉及几个建筑enclaves（AE）（即启动enclave、供应enclave 、供应证书enclave、报价enclave、平台服务enclave）。

应用程序生成的SGX enclave和英特尔提供的建筑enclave之间的通信是通过应用enclave服务管理器（AESM）进行的。AESM作为一个守护程序运行，并提供一个套接字，应用程序可以通过它来促进各种SGX服务，如启动批准、远程证明报价签署等。

### Ubuntu 20.04/18.04/16.04

在 Ubuntu 20.04/18.04/16.04 系统上安装 AESM 服务的一种便捷方法是使用 Intel 的[官方 Intel SGX APT 存储库](https://download.01.org/intel-sgx/sgx_repo/)。

首先将英特尔 SGX APT 存储库添加到您的系统：

```
echo "deb <https://download.01.org/intel-sgx/sgx_repo/ubuntu> $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/intel-sgx.list >/dev/null
curl -sSL "<https://download.01.org/intel-sgx/sgx_repo/ubuntu/intel-sgx-deb.key>" | sudo -E apt-key add -

```

然后安装`sgx-aesm-service`, `libsgx-aesm-launch-plugin`和 `libsgx-aesm-epid-plugin`包：

```
sudo apt update
sudo apt install sgx-aesm-service libsgx-aesm-launch-plugin libsgx-aesm-epid-plugin

```

AESM 服务应该已启动并正在运行。要确认这一点，请使用：

```
sudo systemctl status aesmd.service

```

### **支持 Docker 部署**

[在支持Docker](https://docs.docker.com/engine/)的系统上安装和运行 AESM 服务的一种简单方法是使用[我们的 AESM 容器映像](https://hub.docker.com/r/oasisprotocol/aesmd/)。

执行以下命令应该（总是）拉取最新版本的 AESM Docker 容器，映射 SGX 设备和`/var/run/aesmd`目录并确保 AESM 在后台运行（也在启动时自动启动）：

```
docker run \\
  --pull always \\
  --detach \\
  --restart always \\
  --device /dev/sgx_enclave \\
  --device /dev/sgx_provision \\
  --volume /var/run/aesmd:/var/run/aesmd \\
  --name aesmd \\
  oasisprotocol/aesmd:master

```

> 提示  请确保根据你的内核版本使用正确的设备。上面的例子假设使用了较新的驱动程序，它使用了两个设备。对于旧版本驱动程序，您需要指定`--device /dev/isgx`。

### **支持 Podman 的系统**

[与支持 Docker 的系统类似，在支持Podman](https://podman.io/)的系统上安装和运行 AESM 服务的一种简单方法是使用 [我们的 AESM 容器映像](https://hub.docker.com/r/oasisprotocol/aesmd/)。

首先，使用以下命令创建容器：

```
sudo podman create \\
  --pull always \\
  --device /dev/sgx_enclave \\
  --device /dev/sgx_provision \\
  --volume /var/run/aesmd:/var/run/aesmd:Z \\
  --name aesmd \\
  docker.io/oasisprotocol/aesmd

```

> 提示  请确保根据你的内核版本使用正确的设备。上面的例子假设使用了较新的驱动程序，它使用了两个设备。对于旧版本驱动程序，您需要指定`--device /dev/isgx`。

然后`container-aesmd.service`为它生成systemd单元文件：

```
sudo podman generate systemd --restart-policy=always --time 10 --name aesmd | \\
  sed "/\\[Service\\]/a RuntimeDirectory=aesmd" | \\
  sudo tee /etc/systemd/system/container-aesmd.service

```

最后，启用并启动`container-aesmd.service`：

```
sudo systemctl enable container-aesmd.service
sudo systemctl start container-aesmd.service

```

AESM 服务应该已启动并正在运行。要确认这一点，请使用：

```
sudo systemctl status container-aesmd.service

```

要查看 AESM 服务的日志，请使用：

```
sudo podman logs -t -f aesmd

```

## 检查SGX 安装

为了确保您的 SGX 设置正常工作，您可以使用 [sgxs-tools](https://lib.rs/crates/sgxs-tools) Rust 包中的`sgx-detect`工具

它没有预先构建的软件包，因此您需要自己编译它。

> 提示  sgxs-tools 必须使用 Rust 工具链的夜间版本编译，因为它们使用`#![feature]`宏。

### 安装依赖

确保您的系统上安装了以下内容：

- [GCC](https://gcc.gnu.org/).
- [Protobuf](https://github.com/protocolbuffers/protobuf) compiler.
- [pkg-config](https://www.freedesktop.org/wiki/Software/pkg-config).
- [OpenSSL](https://www.openssl.org/) development package.

在 Fedora 上，您可以使用以下命令安装以上所有内容：:

```
sudo dnf install gcc protobuf-compiler pkg-config openssl-devel

```

在 Ubuntu 上，您可以使用以下命令安装以上所有内容：

```
sudo apt install gcc protobuf-compiler pkg-config libssl-dev

```

### 安装**Nightly**版本[Rust](https://www.rust-lang.org/)

我们遵循Rust给出的建议，使用rustup来安装和管理Rust版本。

> 警告  rustup不能和发行版打包的Rust版本一起安装。在你开始使用rustup之前，你需要删除它（如果它存在的话）。

通过运行以下命令安装 rustup：:

```
curl --proto '=https' --tlsv1.2 -sSf <https://sh.rustup.rs> | sh

```

> 提示  如果你想避免直接执行从互联网上获取的shell脚本，你也可以为你的平台下载rustup-init可执行文件并手动运行它。这将运行rustup-init，它将在你的系统上下载并安装最新的稳定版本的Rust。

安装 Rust nightly :

```
rustup install nightly-2021-11-04

```

### 构建并安装 sgxs-tools

```
cargo +nightly-2021-11-04 install sgxs-tools

```

### 运行`sgx-detect` 工具

安装完成后，运行`sgx-detect`以确保一切设置正确

```
sudo $(which sgx-detect)

```

> 提示  如果您不以 身份运行该`sgx-detect`工具`root`，则它可能没有访问 SGX 内核设备的必要权限

当一切正常时，你应该得到类似以下的输出（有些东西取决于硬件特性，所以你的输出可能不同）。

```
Detecting SGX, this may take a minute...
✔  SGX instruction set
  ✔  CPU support
  ✔  CPU configuration
  ✔  Enclave attributes
  ✔  Enclave Page Cache
  SGX features
    ✔  SGX2  ✔  EXINFO  ✔  ENCLV  ✔  OVERSUB  ✔  KSS
    Total EPC size: 92.8MiB
✘  Flexible launch control
  ✔  CPU support
  ？ CPU configuration
  ✘  Able to launch production mode enclave
✔  SGX system software
  ✔  SGX kernel device (/dev/isgx)
  ✘  libsgx_enclave_common
  ✔  AESM service
  ✔  Able to launch enclaves
    ✔  Debug mode
    ✘  Production mode
    ✔  Production mode (Intel whitelisted)

```

重要的部分是 "能够在调试模式和生产模式（英特尔白名单）下启动enclaves"的复选框。

如果您遇到错误，请参阅[常见 SGX 安装问题列表以](https://edp.fortanix.com/docs/installation/help/) 获取帮助。

## 故障排除

在继续进行 ParaTime 节点特定的故障排除之前，请参阅 [一般故障排除部分。](https://docs.oasis.dev/general/run-a-node/troubleshooting)

### 丢失 `libsgx-aesm-epid-plugin`

如果你在节点的日志中遇到了以下错误信息。

```
failed to initialize TEE: error while getting quote info from AESMD: aesm: error 30

```

确保你已经安装了所有需要的SGX驱动库，如安装SGX Linux驱动一节中所列。本指南的先前版本缺少 `libsgx-aesm-epid-plugin`。

### 访问SGX内核设备时权限被拒绝

如运行`sgx-detect --verbose`报告:

```
🕮  SGX system software > SGX kernel device
Permission denied while opening the SGX device (/dev/sgx/enclave, /dev/sgx or
/dev/isgx). Make sure you have the necessary permissions to create SGX enclaves.
If you are running in a container, make sure the device permissions are
correctly set on the container.

debug: Error opening device: Permission denied (os error 13)
debug: cause: Permission denied (os error 13)

```

确保您通过以下方式运行该`sgx-detect`工具`root`：

```
sudo $(which sgx-detect) --verbose

```

### 打开SGX内核设备出错

如运行 `sgx-detect --verbose` 报告:

```
🕮  SGX system software > SGX kernel device
The SGX device (/dev/sgx/enclave, /dev/sgx or /dev/isgx) could not be opened:
"/dev" mounted with `noexec` option.

debug: Error opening device: "/dev" mounted with `noexec` option
debug: cause: "/dev" mounted with `noexec` option

```

确保你的系统的/dev没有用noexec mount选项挂载

### 无法启动 Enclaves

如运行 `sgx-detect --verbose` 报告:

```
🕮  SGX system software > Able to launch enclaves > Debug mode
The enclave could not be launched.

debug: failed to load report enclave
debug: cause: failed to load report enclave
debug: cause: Failed to map enclave into memory.
debug: cause: Operation not permitted (os error 1)

```

确保你的系统的/dev没有用noexec mount选项挂载。

> update 2022/10/25 - moi
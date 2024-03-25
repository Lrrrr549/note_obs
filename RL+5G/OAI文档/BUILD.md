[doc/BUILD.md · develop · oai / openairinterface5G · GitLab (eurecom.fr)](https://gitlab.eurecom.fr/oai/openairinterface5g/-/blob/develop/doc/BUILD.md?plain=0)
# 概述
[OAI EPC](https://github.com/OPENAIRINTERFACE/openair-epc-fed/blob/master/docs/DEPLOY_HOME_MAGMA_MME.md) 和 [OAI 5GC](https://gitlab.eurecom.fr/oai/cn5g/oai-cn5g-fed/-/blob/master/docs/DEPLOY_HOME.md) 是在不同的项目中开发的，它们有自己的文档，此处不再进一步描述。

OAI 软调制解调器源旨在实现符合 3GPP 标准的 UE、eNodeB 和 gNodeB，可以从 Eurecom [gitlab 存储库](./GET_SOURCES)下载。

源代码附带 build_oai 脚本，位于`openairinterface5g/cmake_targets`目录根目录下。该脚本用于为不同的硬件平台和使用情况构建 oai 二进制文件（可执行文件、共享库）。

通过持续集成流程进行测试的 oai 二进制文件主要有:
- The LTE UE:`lte-uesoftmodem`
- The 5G UE:`nr-uesoftmodem`
- LTE eNodeB：`lte-softmodem`
- 5G gNodeB(gNB)：`nr-softmodem`
- The 5G CU-UP:`nr-cuup`
- LTE PHY simulators：`dlsim`and`ulsim`
- 5G simulators: `nr_dlschsim`，`nr_dlsim`，`nr_pbchsim`，`nr_pucchsim`，`nr_ulschsim`，`nr_ulsim`，`polartest`，`smallblocktest`，`nr _ulsim`，`ldpctest`

运行 [build_oai](https://gitlab.eurecom.fr/oai/openairinterface5g/-/blob/develop/cmake_targets/build_oai) 脚本还会生成生成和/或运行 oai softmodem 二进制文件所需的一些实用程序：
- `conf2uedata`：用于从配置文件构建（4G）UE数据的二进制文件。创建的文件模拟符合 3GPP 标准的手机的 SIM 卡。
- `nvram`：用于构建 （4G） UE （IMEI...） 和 EMM （IMSI， 注册 PLMN） 非易失性数据的二进制文件。
- `rb_tool`：用于 （4G） UE 的无线电承载实用程序
- `genids`T Tracer 实用程序，在构建时用于生成包含文件`T_IDs.h`。此二进制文件位于 [T Tracer 源文件目录中](https://gitlab.eurecom.fr/oai/openairinterface5g/-/tree/develop/common/utils/T)。

OAI 的构建系统使用 [cmake](https://cmake.org/)，这是一个生成 makefile 的工具。该`build_oai`脚本是使用`camke`和`make`/`njnja`简化 oai 构建和使用的包装器。它记录它执行的`camke`和`njnja`/`make`命令。描述如何从源文件构建可执行文件的文件是 [CMakeLists.txt](https://gitlab.eurecom.fr/oai/openairinterface5g/-/blob/develop/CMakeLists.txt)，它用作 cmake 生成 makefile 的输入。

oai 软调制解调器支持许多用例，并定期添加新的用例。大部分功能都可以通过配置文件或命令行选项访问，我们一直在努力避免引入构建选项，因为这样会使测试和使用比运行时选项更复杂。以下功能原本需要特定的构建，现在可以通过配置文件或命令行选项访问：
- s1、noS1
- 除 PHY 模拟器外，所有模拟器都是独立的可执行文件，如 rfsimulator、L2 模拟器。

# 运行`build_oai`
## Options
- `-I`选项用于安装先决条件，只有在首次构建软调制解调器或某些oai依赖项发生变化时才需要它。
- `-w`选项用于选择要包含在构建过程中的射频头支持。构建脚本将 liboai_device.so 与运行时使用的真正设备（此处为 USRP 设备`liboai_usrpdevif.so`）建立软链接。[射频模拟器](https://gitlab.eurecom.fr/oai/openairinterface5g/-/blob/develop/radio/rfsimulator/README.md)是作为替代射频硬件的特定设备实现的，可使用`-w SIMU`选项专门构建，但也可在任何软调制解调器构建过程中构建。
- `--eNB`用于构建`lte-softmodem`可执行文件和所有必需的共享库
- `--gNB`用于构建`nr-softmodem`和`nr-cuup`可执行文件以及所有必需的共享库
- `--UE`用于构建`lte-uesoftmodem`可执行文件和所有必需的共享库
- `--nrUE`用于构建`nr-uesoftmodem`可执行程序和所有必需的共享库
- `--ninja`是指使用`ninja`编译工具，它可以加快编译速度
- `-c`是清理工作空间，强制rebuild
## 安装依赖
使用`-I`选项安装所有依赖库。要为可选库安装更多的库，请使用 `--install-optional-packages` 选项。当 `-I` 选项与`-w` 搭配使用时，也会为 SDR 安装依赖库。例如，要安装所有依赖库和 USRP 的依赖库，运行：
```shell
cd openairinterface5g/cmake_targets/
./build_oai -I --install-optional-packages -w USRP
```
更多信息请参阅下面关于安装 UHD(USRP Hardware Driver) 的章节。
## 从源代码安装(new) asn1c
通过tag 2023.w22，我们从[自己的 asn1c](https://gitlab.eurecom.fr/oai/asn1c.git) 转向了[社区维护的 asn1c](https://github.com/mouse07410/asn1c)。新版本修正了许多错误，但与旧版本不兼容。为了方便过渡，两个版本可以并行安装。假设您使用 `build_oai` 安装了 `asn1c`，2023.w22 之前的tag将使用 `/usr/local/` 下的版本；2023.w22 及更新的标签将使用 `/opt/asn1c/`（如果存在）或任何系统目录（例如，也是 `/usr/local/`）下的版本，此外，还要检查是否支持新 asn1c 的所有命令行选项。
要安装新的 `asn1c`，可以运行 `build_oai -I`。如果不想重新安装所有软件包，也可以像这样直接安装 `asn1c`：
```shell
cd openairinterface5g
sudo ls                               # open sudo session, required by install_asn1c_from_source
. oaienv                              # read of default variables
. cmake_targets/tools/build_helper    # read in function
install_asn1c_from_source             # install under `/opt/asn1c`
```
此外，如果您选择在其他地方安装，也可以使用这两种方法中的一种，指向要使用的特定 `asn1c`：
```shell
./build_oai --ninja <other-options> --cmake-opt -DASN1C_EXEC=/opt/asn1c/bin/asn1c
cmake .. -GNinja -DASN1C_EXEC=/opt/asn1c/bin/asn1c
```
## 从source安装UHD
在安装先决条件（尤其是 UHD 驱动程序）时，现在可以指定是否从源代码安装。
- 对于基于 `fedora` 的操作系统来说，以前一直都是这样。但现在你可以指定要安装的版本。
- 对于 `ubuntu` 操作系统，你仍然可以从 Ettus PPA 安装，或选择从源代码安装的版本。
  * 在 PPA 安装的情况下，无需做任何特别操作，脚本就会安装 PPA 上的最新版本。
    - `./build_oai -I -w USRP`
  - 如果从源代码安装，则按以下步骤操作：
```bash
export BUILD_UHD_FROM_SOURCE=True
export UHD_VERSION=3.15.0.0
./build_oai -I -w USRP
```
`UHD_VERSION` 环境变量必须是`https://github.com/EttusResearch/uhd.git`资源库中的有效标记（减去 `v`）。
## 构建物理层模拟器
```shell
cd openairinterface5g/cmake_targets/
./build_oai --phy_simulators
```

您可以单独构建任何 oai 软调制解调器可执行文件，根据您的 oai 使用情况，可能并不需要所有这些文件。

完成编译后，二进制文件可在 `cmake_targets/ran_build/build` 目录中找到。
## 构建可选二进制文件
有许多可选库可用于支持 RAN，如 telnetsrv、scopes、卸载库等。
使用编译脚本的帮助选项可以获得可用的可选库列表。
```shell
./build_oai --build-lib all # build all
./build_oai --build-lib telnetsrv  # build only telnetsrv
./build_oai --build-lib "telnetsrv enbscope uescope nrscope nrqtscope"
./build_oai --build-lib telnetsrv --build-lib nrqtscope
```
以下库已在 CI 中构建，并应始终有效：`telnetsrv`、`enbscope`、`uescope`、`nrscope`、`nrqtscope`。
某些库有更多依赖关系，可能无法在每个系统上构建：
`enbscope`、`uescope`、`nrscope`: libforms/X
`nrqtscope`：Qt5
`ldpc_cuda`：CUDA
`ldpc_t1`：DPDK 和 VVDN T1
`websrv`: npm 及其他
# 直接运行cmake
`build_oai` 是 `cmake` 的包装器。因此可以直接运行 `cmake`。使用 `ninja` 的示例：构建 5G 的所有 "主要目标"，不包括附加库：
```shell
cd openairinterface5g
mkdir build && cd build
cmake .. -GNinja && ninja nr-softmodem nr-uesoftmodem nr-cuup params_libconfig coding rfsimulator ldpc
```
要构建额外的库，如 `telnetsrv`，请执行以下操作：
```shell
cmake .. -GNinja -DENABLE_TELNETSRV=ON && ninja telnetsrv
```
可使用 `ccmake ...` 或 `cmake-gui ...` 查看所有库的列表。

目前还不能以 `cmake .. -GNinja && ninja` 的形式编译所有目标：目前，即使没有依赖关系，SDR 也总是暴露在外，而且有些目标根本就是坏的。同样，`build_oai` 会列出它编译的所有目标，您可以用 `ninja`

由于历史原因，build_oai 的默认目标目录如下：
```shell
cd openairinterface5g/cmake_targets/ran_build/build
cmake ../../.. -GNinja
ccmake ../../..
cmake-gui ../../..
```
当然，你可以在该目录下使用所有标准的 cmake/ninja/make 命令。
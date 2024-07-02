[doc/NR_SA_Tutorial_OAI_nrUE.md · develop · oai / openairinterface5G · GitLab (eurecom.fr)](https://gitlab.eurecom.fr/oai/openairinterface5g/-/blob/develop/doc/NR_SA_Tutorial_OAI_nrUE.md)

# 场景
本教程介绍如何使用 OAI CN5G、OAI gNB 和 OAI nrUE 配置和运行 5G 端到端设置。

最低硬件要求
- Laptop/Desktop/Server for OAI CN5G and OAI gNB
    - Operating System: [Ubuntu 22.04 LTS](https://releases.ubuntu.com/22.04/ubuntu-22.04.3-desktop-amd64.iso)
    - CPU: 8 cores x86_64 @ 3.5 GHz
    - RAM: 32 GB
- Laptop for UE
    - Operating System: [Ubuntu 22.04 LTS](https://releases.ubuntu.com/22.04/ubuntu-22.04.3-desktop-amd64.iso)
    - CPU: 8 cores x86_64 @ 3.5 GHz
    - RAM: 8 GB
- [USRP B210](https://www.ettus.com/all-products/ub210-kit/), [USRP N300](https://www.ettus.com/all-products/USRP-N300/) or [USRP X300](https://www.ettus.com/all-products/x300-kit/)
    - 请确定连接 USRP 的网络接口并更新 gNB 配置文件
# OAI CN5G
按照[CN5G文件](NR_SA_Tutorial_OAI_CN5G.md)来进行配置。
# OAI gNB and nrUE
## 先决条件
**Build UHD**
```shell
sudo apt install -y libboost-all-dev libusb-1.0-0-dev doxygen python3-docutils python3-mako python3-numpy python3-requests python3-ruamel.yaml python3-setuptools cmake build-essential
git clone https://github.com/EttusResearch/uhd.git ~/uhd
cd ~/uhd
git checkout v4.5.0.0
cd host
mkdir build
cd build
cmake ../
make -j $(nproc)
make test # This step is optional
sudo make install
sudo ldconfig
sudo uhd_images_downloader
```
## Build OAI gNB and OAI nrUE
```shell
# Get openairinterface5g source code
git clone https://gitlab.eurecom.fr/oai/openairinterface5g.git ~/openairinterface5g
cd ~/openairinterface5g
git checkout develop
# Install OAI dependencies
cd ~/openairinterface5g/cmake_targets
./build_oai -I
# nrscope dependencies
sudo apt install -y libforms-dev libforms-bin
# Build OAI gNB
cd ~/openairinterface5g
source oaienv
cd cmake_targets
./build_oai -w USRP --ninja --nrUE --gNB --build-lib "nrscope" -C
```
# Run OAI CN5G and OAI gNB
## Run OAI CN5G
```bash
cd ~/oai-cn5g
docker compose up -d
```
## Run OAI gNB
### USRP B210
```bash
cd ~/openairinterface5g
source oaienv
cd cmake_targets/ran_build/build
sudo ./nr-softmodem -O ../../../targets/PROJECTS/GENERIC-NR-5GC/CONF/gnb.sa.band78.fr1.106PRB.usrpb210.conf --gNBs.[0].min_rxtxtime 6 --sa -E --continuous-tx
```
### RFsimulator
```bash
cd ~/openairinterface5g
source oaienv
cd cmake_targets/ran_build/build
sudo ./nr-softmodem -O ../../../targets/PROJECTS/GENERIC-NR-5GC/CONF/gnb.sa.band78.fr1.106PRB.usrpb210.conf --gNBs.[0].min_rxtxtime 6 --rfsim --sa
```
# OAI UE
## SIM Card
Edit openair3/UICC/usim_interface.c
```bash
#define UICC_PARAMS_DESC {\
    {"imsi",             "USIM IMSI\n",          0,         strptr:&(uicc->imsiStr),              defstrval:"001010000000001",           TYPE_STRING,    0 },\
    {"nmc_size"          "number of digits in NMC", 0,      iptr:&(uicc->nmc_size),               defintval:2,         TYPE_INT,       0 },\
    {"key",              "USIM Ki\n",            0,         strptr:&(uicc->keyStr),               defstrval:"fec86ba6eb707ed08905757b1bb44b8f", TYPE_STRING,    0 },\
    {"opc",              "USIM OPc\n",           0,         strptr:&(uicc->opcStr),               defstrval:"c42449363bbad02b66d16bc975d77cc1", TYPE_STRING,    0 },\
    {"amf",              "USIM amf\n",           0,         strptr:&(uicc->amfStr),               defstrval:"8000",    TYPE_STRING,    0 },\
    {"sqn",              "USIM sqn\n",           0,         strptr:&(uicc->sqnStr),               defstrval:"000000",  TYPE_STRING,    0 },\
    {"dnn",              "UE dnn (apn)\n",       0,         strptr:&(uicc->dnnStr),               defstrval:"oai",     TYPE_STRING,    0 },\
    {"nssai_sst",        "UE nssai\n",           0,         iptr:&(uicc->nssai_sst),              defintval:1,    TYPE_INT,    0 }, \
    {"nssai_sd",         "UE nssai\n",           0,         iptr:&(uicc->nssai_sd),               defintval:0xffffff,    TYPE_INT,    0 }, \
};
```
## 5.2 Testing OAI nrUE with USRP B210
Important notes:
- 应在除 gNB 之外的第二台 Ubuntu 22.04 主机上运行该程序
- 仅适用于使用 USRP B210 运行 OAI gNB 时
Run OAI nrUE
```bash
cd ~/openairinterface5g
source oaienv
cd cmake_targets/ran_build/build
sudo ./nr-uesoftmodem -r 106 --numerology 1 --band 78 -C 3619200000 --ue-fo-compensation --sa -E --uicc0.imsi 001010000000001
```
Ping test
- UE host
```bash
ping 192.168.70.135 -I oaitun_ue1
```

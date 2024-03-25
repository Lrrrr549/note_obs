[doc/NR_SA_Tutorial_COTS_UE.md · develop · oai / openairinterface5G · GitLab (eurecom.fr)](https://gitlab.eurecom.fr/oai/openairinterface5g/-/blob/develop/doc/NR_SA_Tutorial_COTS_UE.md)

# 场景
本教程介绍如何使用 OAI CN5G、OAI gNB 和 COTS UE 配置和运行 5G 端到端设置。

最低硬件要求
- Laptop/Desktop/Server for OAI CN5G and OAI gNB
    - Operating System: [Ubuntu 22.04 LTS](https://releases.ubuntu.com/22.04/ubuntu-22.04.3-desktop-amd64.iso)
    - CPU: 8 cores x86_64 @ 3.5 GHz
    - RAM: 32 GB
- Laptop for UE
    - Operating System: Microsoft Windows 10 x64
    - CPU: 4 cores x86_64
    - RAM: 8 GB
    - Windows driver for Quectel MUST be equal or higher than version **2.4.6**
- [USRP B210](https://www.ettus.com/all-products/ub210-kit/), [USRP N300](https://www.ettus.com/all-products/USRP-N300/) or [USRP X300](https://www.ettus.com/all-products/x300-kit/)
    - 请确定连接 USRP 的网络接口并更新 gNB 配置文件
- Quectel RM500Q
    - Module, M.2 to USB adapter, antennas and SIM card
    - Firmware version of Quectel MUST be equal or higher than **RM500QGLABR11A06M4G**
# OAI CN5G
## 先决条件
按照[CN5G文件](NR_SA_Tutorial_OAI_CN5G.md)来进行配置。
## SIM卡
Program SIM Card with [Open Cells Project](https://open-cells.com/) application [uicc-v3.2](https://open-cells.com/d5138782a8739209ec5760865b1e53b0/uicc-v3.2.tgz).
```bash
sudo ./program_uicc --adm 12345678 --imsi 001010000000001 --isdn 00000001 --acc 0001 --key fec86ba6eb707ed08905757b1bb44b8f --opc C42449363BBAD02B66D16BC975D77CC1 -spn "OpenAirInterface" --authenticate
```
# OAI gNB
## OAI gNB先决条件
### Build UHD
```bash
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
## Build OAI gNB
```bash
# Get openairinterface5g source code
git clone https://gitlab.eurecom.fr/oai/openairinterface5g.git ~/openairinterface5g
cd ~/openairinterface5g
git checkout develop
# Install OAI dependencies
cd ~/openairinterface5g/cmake_targets
./build_oai -I
# Build OAI gNB
cd ~/openairinterface5g
source oaienv
cd cmake_targets
./build_oai -w USRP --ninja --gNB -C
```
# 运行OAI CN5G and gNB
## CN5G
```shell
cd ~/oai-cn5g
docker compose up -d
```
# gNB
**USRP B210**
```bash
cd ~/openairinterface5g
source oaienv
cd cmake_targets/ran_build/build
sudo ./nr-softmodem -O ../../../targets/PROJECTS/GENERIC-NR-5GC/CONF/gnb.sa.band78.fr1.106PRB.usrpb210.conf --sa -E --continuous-tx
```
# 运行UE
## Testing with Quectel RM500Q
### 设置Quectel
使用 [PuTTY](https://the.earth.li/~sgtatham/putty/latest/w64/putty.exe)，以 115200 bps 的速度通过串行接口（例如 COM2）向模块发送以下 AT 命令：
```shell
# MUST be sent at least once everytime there is a firmware upgrade!
AT+CGDCONT=1,"IP","oai"
AT+CGDCONT=2
AT+CGDCONT=3
# (Optional, debug only, AT commands) Activate PDP context, retrieve IP address and test with ping
AT+CGACT=1,1
AT+CGPADDR=1
AT+QPING=1,"openairinterface.org"
```
### Ping测试
- UE
```shell
ping 192.168.70.135 -t -S 12.1.1.2
```
- CN5G
```shell
docker exec -it oai-ext-dn ping 12.1.1.2
```
### 下行iPerf测试
- UE host
    - Download iPerf for Microsoft Windows from [here](https://iperf.fr/download/windows/iperf-2.0.9-win64.zip).
    - Extract to Desktop and run with Command Prompt:
```bash
cd C:\Users\User\Desktop\iPerf\iperf-2.0.9-win64\iperf-2.0.9-win64
iperf -s -u -i 1 -B 12.1.1.2
```
- CN5G host
```bash
docker exec -it oai-ext-dn iperf -u -t 86400 -i 1 -fk -B 192.168.70.135 -b 100M -c 12.1.1.2
```

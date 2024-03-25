CoreNetwork 5G
[doc/NR_SA_Tutorial_OAI_CN5G.md · develop · oai / openairinterface5G · GitLab (eurecom.fr)](https://gitlab.eurecom.fr/oai/openairinterface5g/-/blob/develop/doc/NR_SA_Tutorial_OAI_CN5G.md)

# 场景
在本教程中，我们将介绍如何使用 OAI CN5G、OAI gNB 和 COTS UE 配置和运行 5G 端到端设置。

最低硬件要求：
- Laptop/Desktop/Server for OAI CN5G and OAI gNB
    - Operating System: [Ubuntu 22.04 LTS](https://releases.ubuntu.com/22.04/ubuntu-22.04.3-desktop-amd64.iso)
    - CPU: 8 cores x86_64 @ 3.5 GHz
    - RAM: 32 GB
# OAI CN5G
## OAI CN5G 先决条件
```shell
sudo apt install -y git net-tools putty
# https://docs.docker.com/engine/install/ubuntu/
sudo apt install -y ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
echo "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
# Add your username to the docker group, otherwise you will have to run in sudo mode.
sudo usermod -a -G docker $(whoami)
reboot
```
## OAI CN5G 配置文件
下载并复制配置文件：
```shell
wget -O ~/oai-cn5g.zip https://gitlab.eurecom.fr/oai/openairinterface5g/-/archive/develop/openairinterface5g-develop.zip?path=doc/tutorial_resources/oai-cn5g
unzip ~/oai-cn5g.zip
mv ~/openairinterface5g-develop-doc-tutorial_resources-oai-cn5g/doc/tutorial_resources/oai-cn5g ~/oai-cn5g
rm -r ~/openairinterface5g-develop-doc-tutorial_resources-oai-cn5g ~/oai-cn5g.zip
```
## 拉取 OAI CN5G docker 映像
```bash
docker pull mysql:8.0
docker pull oaisoftwarealliance/oai-amf:develop
docker pull oaisoftwarealliance/oai-nrf:develop
docker pull oaisoftwarealliance/oai-smf:develop
docker pull oaisoftwarealliance/oai-udr:develop
docker pull oaisoftwarealliance/oai-udm:develop
docker pull oaisoftwarealliance/oai-ausf:develop
docker pull oaisoftwarealliance/oai-spgwu-tiny:develop
docker pull oaisoftwarealliance/trf-gen-cn5g:jammy
docker pull oaisoftwarealliance/ims:latest
```
# 运行OAI CN5G
## 启动
```shell
cd ~/oai-cn5g
docker compose up -d
```
## 停止
```shell
cd ~/oai-cn5g
docker compose down
```
# 使用 OAI gNB 运行 5G NR SA 端到端设置
## 使用 COTS UE 进行测试
[参考COTS UE](./NR_SA_Tutorial_COTS_UE)
## 使用OAI nrUE进行测试
[参考nrUE](./NR_SA_Tutorial_OAI_nrUE.md)
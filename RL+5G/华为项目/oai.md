OAI平台搭建
连接bupt_test网络
1. 核心网docker部署
1.  下载源码 
git clone http://git.opensource5g.org/openxg/openxg-5gcs-release.git
2.  安装docker、docker-compose 
cd openxg-5gcs-release/scripts
./install.sh -I
3.  创建docker网桥 
docker network create docker-openxg --subnet=172.11.200.0/24 -o com.docker.network.bridge.name=docker-openxg
4.  启动数据库 
以上命令部署了phpmyadmin，通过访问http://本机ip:8080 可以打开。用户名为：root，密码为：123456；
用户sim卡信息在 Witcomm-DB数据库 users表中。 
5.  在基站主机配置到核心网的静态路由
在基站所在主机中执行以下命令： 
route add -net 172.11.200.0 netmask 255.255.255.0 gw <核心网所在主机的IP>
2. gNB搭建
2.1 环境搭建
Build UHD
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
Build OAI gNB
# Get openairinterface5g source code
git clone https://gitlab.eurecom.fr/oai/openairinterface5g.git ~/openairinterface5g
cd ~/openairinterface5g
git checkout develop

# Install OAI dependencies
cd ~/openairinterface5g/cmake_targets
./build_oai -I
OpenAirInterface自带的一些依赖库可能由于网络问题无法下载，可以使用OpenXG本地托管的第三方库，下载并编译安装：
git clone http://gitlab.openxg.org.cn/openxg/OpenXG-Install.git
cd OpenXG-Install/
sudo ./install.sh
完成第三方依赖安装后，下载并编译基站源代码：

git clone http://gitlab.openxg.org.cn/openxg/ran.git
git checkout dev  #切换到dev分支
cd ran/
sudo ./build_oai --nrUE --gNB -w OXGRF #使用OXGRF编译基站gNB和终端nrUE
2.2 gNB启动
# 检查USRP是否连接
uhd_find_devices

# 启动gNB
cd ran/cmake_targets
sudo ./ran_build/build/nr-softmodem --sa -o ../targets/PROJECTS/GENERIC-NR-5GC/CONF/gnb.sa.band78.fr1.106PRB.usrpb210.conf
3. CN与gNB连接
3.1 核心网
# 输入如下代码，允许路由表规则 ，每次开机都需再次输入
sudo sysctl net.ipv4.conf.all.forwarding=1
sudo iptables -P FORWARD ACCEPT
进入相关yaml文件查看参数：
amf处
- MCC: 466
- MNC: 96
- REGION_ID: 128
文件内还会有一个十六/十进制的TAC参数，也需要修改。
3.2 gNB
修改gNB配置文件
vim gnb.sa.band78.fr1.106PRB.usrpb210.conf
# Tracking area code, 0x0000 and 0xfffe are reserved values
    tracking_area_code  =  100;
    plmn_list = ({ mcc = 466; mnc = 96; mnc_length = 2; snssaiList = ({ sst = 1 }) });

NETWORK_INTERFACES :
    {
        GNB_INTERFACE_NAME_FOR_NG_AMF            = "<gNB网卡>";
        GNB_IPV4_ADDRESS_FOR_NG_AMF              = "<gNB ip>";
        GNB_INTERFACE_NAME_FOR_NGU               = "<gNB网卡>";
        GNB_IPV4_ADDRESS_FOR_NGU                 = "<gNB ip>";
        GNB_PORT_FOR_S1U                         = 2152; # Spec 2152
    };
3.3 输出log文件
docker logs -f amf
docker logs -f smf
只列举出了部分，如果gNB与核心网连接成功，amf.log中会有明显的显示。
3.4 核心网启停
# 数据库启动
docker-compose -f docker-mysql.yml up -d
# 核心网网元启动
docker-compose -f docker-3-network-element.yml up -d
# 数据库关闭
docker-compose -f docker-mysql.yml down
# 核心网网元关闭
docker-compose -f docker-3-network-element.yml down
4. UE与gNB连接
二者连接主要有两个步骤：
● 对SIM卡进行编程
● 修改核心网数据库
4.1 SIM卡编程
4.2 修改核心网数据库
debug
核心网换分支：ubuntu18.04
git checkout ubuntu18.04
amf和smf版本必须是1.0，spgwu无所谓
amf_ip_address用核心网主机的ip
不要运行start_5gc.sh/stop_5gc.sh
遇到基站log出现很多L、usrp分配问题等，拔下usrp，重启系统
！！！UE无法联网的一个debug方法！！！
如果基站报错：
[GTPU] Failed to send data to 172.11.200.25 on port 0 ......
向上拉基站log，如果有提示2152端口被占用则可以采用以下方法：
# 先关闭基站侧运行的程序
# 再查看端口占用情况
netstat -tulpn | grep 2152
# 如果显示有占用，则kill掉
kill -9 <PID>
然后重启核心网与基站
核心网
# 输入如下代码，允许路由表规则，每次开机都需再次输入，一定要输入!!!!!
sudo sysctl net.ipv4.conf.all.forwarding=1
sudo iptables -P FORWARD ACCEPT
# 数据库启动
docker-compose -f docker-mysql.yml up -d
# 核心网网元启动
docker-compose -f docker-3-network-element.yml up -d
基站
# 加静态路由
route add -net 172.11.200.0 netmask 255.255.255.0 gw <核心网所在主机的IP>
加静态路由后，进行ping测试
# ping docker-openxg 网桥
ping 172.11.200.1
# ping amf、smf、upf
ping 172.11.200.14
ping 172.11.200.15
ping 172.11.200.25
# 都能ping通证明连接没问题，可以启动基站
# 启动
cd ran/cmake_targets
sudo ./ran_build/build/nr-softmodem --sa -o ../targets/PROJECTS/GENERIC-NR-5GC/CONF/gnb.sa.band78.fr1.106PRB.usrpb210.conf
原因：推测可能是gNB基站侧重启次数过多，程序不能及时释放端口，导致端口被占用，无法与upf网元连通，就无法上网
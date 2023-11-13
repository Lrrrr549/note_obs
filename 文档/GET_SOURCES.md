从OAI的gitlab上的develop分支获取。
# 先决条件
下载git
```shell
sudo apt-get update
sudo apt-get install git
```
# 使用EURECOM Gitlab
[OAI 5G](https://gitlab.eurecom.fr/oai/openairinterface5g.git)包括RAN（4G和5G）的源码
想对项目做贡献时，用姓名和电子邮件配置git
```shell
git config --global user.name "Your Name"
git config --global user.email "Your email address"
```
# 仅克隆git仓库
RAN仓库：
```shell
git clone https://gitlab.eurecom.fr/oai/openairinterface5g.git
```
# Checkout的分支
`develop`：包含在我们的 CI 测试平台上测试过的最新提交。更新频率约为每周一次。5G 仅在此分支中。
`master`：包含 4G 的稳定版本，将来会更新为 5G。
tag的命名规则：
- 在`develop`分支上**v1**
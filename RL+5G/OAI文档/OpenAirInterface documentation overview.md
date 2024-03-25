[doc/README.md · develop · oai / openairinterface5G · GitLab (eurecom.fr)](https://gitlab.eurecom.fr/oai/openairinterface5g/-/blob/develop/doc/README.md?plain=0)
# General 
- [FEATURE_SET](https://gitlab.eurecom.fr/oai/openairinterface5g/-/blob/develop/doc/FEATURE_SET.md): 支持的功能
- [GET_SOURCES](GET_SOURCES.md): 如何下载资源
- [BUILD](./BUILD.md): build sources
- [clang-format](./clang-format.md): 格式化代码
- [environment-variables.md](https://gitlab.eurecom.fr/oai/openairinterface5g/-/blob/develop/doc/environment-variables.md): 环境变量
There is some general information in the [OpenAirInterface Gitlab Wiki](https://gitlab.eurecom.fr/oai/openairinterface5g/-/wikis/home)
# Tutorials
- Step-by-step tutorials to set up 5G:
  * [OAI 5GC](./NR_SA_Tutorial_OAI_CN5G.md)
  * [OAI gNB with COTS UE](./NR_SA_Tutorial_COTS_UE.md)
  * [OAI NR-UE](./NR_SA_Tutorial_OAI_nrUE.md)
- [RUNMODEM.md](https://gitlab.eurecom.fr/oai/openairinterface5g/-/blob/develop/doc/RUNMODEM.md): 关于如何：
  * 运行模拟器
  * 硬件运行
  * 特定OAI模式 (phy-test, do-ra, noS1)
  * (5G) 使用SDAP和定制DRBs
  * IF（中频）设置和任意频率
  * MIMO
- [How to run a 5G-NSA setup](https://gitlab.eurecom.fr/oai/openairinterface5g/-/blob/develop/doc/TESTING_GNB_W_COTS_UE.md)
- [How to run a 4G setup using L1 simulator](https://gitlab.eurecom.fr/oai/openairinterface5g/-/blob/develop/doc/L1SIM.md) *Note: we recommend the RFsimulator
- [How to use the L2 simulator](https://gitlab.eurecom.fr/oai/openairinterface5g/-/blob/develop/doc/L2NFAPI.md)
- [How to use multiple BWPs](https://gitlab.eurecom.fr/oai/openairinterface5g/-/blob/develop/doc/RUN_NR_multiple_BWPs.md)
- [How to run OAI-VNF and OAI-PNF](https://gitlab.eurecom.fr/oai/openairinterface5g/-/blob/develop/doc/RUN_NR_NFAPI.md) _Note: does not work currently_
- [How to use the positioning reference signal (PRS)](https://gitlab.eurecom.fr/oai/openairinterface5g/-/blob/develop/doc/RUN_NR_PRS.md)
- [How to use device-to-device communication (D2D, 4G)](https://gitlab.eurecom.fr/oai/openairinterface5g/-/blob/develop/doc/d2d_emulator_setup.txt)
- [How to run with E2 agent](https://gitlab.eurecom.fr/oai/openairinterface5g/-/blob/develop/openair2/E2AP/README.md)
未维护的遗留文件:
- `L2NFAPI_NOS1.md`, `L2NFAPI_S1.md`: old L2simulator, not valid anymore
- `SystemX-tutorial-design.md`
- `UL_MIMO.txt`
# Libraries
## SDRs
Some directories under `radio` contain READMEs:
- [RFsimulator](https://gitlab.eurecom.fr/oai/openairinterface5g/-/blob/develop/radio/rfsimulator/README.md)
- [USRP](https://gitlab.eurecom.fr/oai/openairinterface5g/-/blob/develop/radio/USRP/README.md)
The other SDRs (AW2S, LimeSDR, ...) have no READMEs.
# Testing
- [UnitTests.md](https://gitlab.eurecom.fr/oai/openairinterface5g/-/blob/develop/doc/UnitTests.md) 单元测试
- [TESTBenches.md](https://gitlab.eurecom.fr/oai/openairinterface5g/-/blob/develop/doc/TESTBenches.md) 持续集成测试
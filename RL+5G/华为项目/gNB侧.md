[NR_PHY] Number of bad PUCCH received: 62

新空口收到的bad PUCCH数量

[PHY] UL AMP frame 918 8, symbol 12, rbs 5, res 60, pusch amp 112

上行链路放大(UL AMP)信息。frame 918 8:在第918帧的第8个时隙，symbol 12:该时隙的第12个符号，rbs 5:使用了5个资源块，res 60:资源的数量或资源指示符的值，pusch amp 112:PUSCH（物理上行共享信道）的放大增益为112。

[PHY] UL AMP frame 918 18, symbol 12, rbs 5, res 60, pusch amp 115
[PHY] UL AMP frame 964 18, symbol 12, rbs 5, res 60, pusch amp 137
[PHY] UL AMP frame 965 8, symbol 12, rbs 5, res 60, pusch amp 127
[PHY] UL AMP frame 1011 8, symbol 12, rbs 5, res 60, pusch amp 120
[PHY] UL AMP frame 1011 18, symbol 12, rbs 5, res 60, pusch amp 114

[IN_MAC] Frame.Slot 0.0

在媒体访问控制(MAC)层，正在查看第0帧第0时隙的信息。

UE ID 1 RNTI 2728 (1/1) PH 17 dB PCMAX 11 dBm, average RSRP 0 (0 meas)
- 这是关于用户设备(User Equipment, UE) ID为1的终端的信息。RNTI（无线网络临时标识）是2728，这是用于识别UE的临时ID。
- PH 17 dB可能是指功率头部（Power Headroom），是17分贝。
- PCMAX 11 dBm是指UE的最大功率控制值，是11分贝毫瓦。
- average RSRP 0 (0 meas)表示平均参考信号接收功率(Reference Signal Received Power, RSRP)为0，可能是因为没有测量到或者测量次数为0。

UE 1: disch_rounds 16067/1154/784/779, disch_errors 779, pucchO_DTX 89, IBLER 0.00015 MCS 25
- 这些是用户设备进行数据调度的次数统计，包括重传次数、错误次数以及PUCCH的DTX（不传输）次数等。
- IBLER 0.00015是指初始块错误率（Initial Block Error Rate）。
- MCS 25指的是使用的调制和编码方案（Modulation and Coding Scheme）等级。

UE 1: dlsch_total_bytes 63597961
- 这里记录了UE 1在下行共享信道（Downlink Shared Channel, DLSCH）上总共传输的字节数。

UE 1: Ulsch_rounds 22297/72/17/11, ulsch_DTX 17, ulsch_errors 5
UE 1: ulsch_total_bytes_scheduled 13933327, ulsch_total_bytes_received 13930314
- 这里统计了上行共享信道（Uplink Shared Channel, ULSCH）的传输轮次、DTX次数、错误次数以及收到和调度的总字节数。

UE 1: LCID 1: 443 bytes TX
UE 1: LCID 4: 62010950 bytes TX
UE 1: LCID 4: 12157333 bytes RX
- 这是逻辑信道ID（Logical Channel ID, LCID）的数据传输统计，包括传输（TX）和接收（RX）的字节数。LCID是用于标识不同类型的数据流。
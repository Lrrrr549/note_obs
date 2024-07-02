# 发送端

需要通过usb连接上UE，共享网络，网卡是usb0。webrtc传输的可执行文件位于`/home/ep/指标监听/test`，文件名为sender，loss监听代码也在该路径。

电脑密码为123456

# 基站侧

loss监听代码位于`/home/bupt/lr/packet_loss.py`，电脑密码为bupt0924

核心网的电脑密码是123456

# 接收端

连接实验室Wi-Fi，可执行文件位于`/home/boboja/webrtc`，包括receiver和server。运行peerconnection_server后，再运行receiver。电脑密码为3422441

**监听代码要先于webrtc代码运行，记得运行前修改webrtc的输出txt**
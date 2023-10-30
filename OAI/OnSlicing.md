# Introduction
5G场景对服务质量有很高的要求，如延迟、抖动、吞吐量和可靠性。定制通信、网络和计算资源的供应十分紧迫。
网络切片通过虚拟化基站和交换机等物理基础设施，为切片租户提供有专用虚拟资源的逻辑网络（网络切片）来实现这些应用。需要使用端到端切片来创建由各种资源组成的切片。
OnSlicing，端到端网络切片系统，确保了在线跨域资源的调度，同时在在线学习过程中几乎未违反切片的SLA（服务等级协议）。
设计实现：
	几乎零违规的学习。管理端到端网络时，运营商希望最大限度地减少网络资源的使用，同时也要满足切片的性能需求。现有的深度强化学习方法对行动空间进行随机探索，同时在不考虑系统约束的情况下寻找更优策略。
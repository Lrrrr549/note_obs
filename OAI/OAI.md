操作系统：
- Ubuntu 16.04 or 18.04 LTS with the low latency kernel option
- CentOS Linux release 7.4 or higher
- Red Hat Enterprise Linux 7.7 or higher
硬件要求:
	PC:
	1. Intel Core i7 6900K (8 cores), 16GB DDR, 480GB SSD  
	    Allows SW LDPC on 3 cores (1 segment per slot, 3 slots decoded in parallel, up to 30Mb/s)  
	    or  
	    LDPC on FPGA (up to 300Mb/s on 80MHz SISO).
	2. Intel Core i9 7980EX (18 cores) shall support future configurations incl. MIMO  
	    Allows parallel LDPC SW decoder on 9 or 12 cores, up to 3 (tested) or 5 (ongoing) segments per slot or 140Mb/s  
	    or  
	    LDPC on FPGA (same as above)
	3. Intel(R) Xeon(R) Gold 6154 CPU @ 3.00GHz with 18 cores and 2x10Gbit Ethernet
	4. AMD EPYC™ 3rd Gen 7513 32-Core Processor and 2x10Gbit Ethernet
	5. AMD EPYC™ 3rd Gen 7713 64 Cores Processor and 2x10Gbit Ethernet
	SDR/USRP
	
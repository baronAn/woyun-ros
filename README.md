# woyun-ros
# ros机器人开发说明

## 1 TX1嵌入式开发板介绍
### 硬件介绍
    Jetson TX1是NVIDIA第二代嵌入式开发者套件。
    虽然只有信用卡大小，但Jetson TX1 GPU模块的浮点运算能力却达到1 Teraflops，相比Jetson TK1有巨幅提升。
    内建256个CUDA核心的NVIDIA Maxwell GPU，64位ARM A57 CPU，4GB LPDDR4内存，16GB闪存，蓝牙，802.11ac Wi-Fi模块和千兆以太网卡，运 行Linux for Tegra操作系统。
    主板提供了USB 3.0 A型，USB 2.0，HDMI，M.2键E，PCI-E x4，千兆以太网，SD，SATA数据和供电等接口
    针对硬件创客的扩展接口：GPIO，I2C，I2S，SPI，带流控制的TTL UART，显示器扩展头和摄像头扩展头一个不少
    如此强大的性能，Jetson TX1显然是智能无人机，机器人最理想的嵌入式解决方案。
    处理组件
    四核ARM Cortex-A57
    256核Maxwell GPU
    4GB LPDDR4
    16GB eMMC
    H.264 / H.265编码器和解码器
    双ISP（图像服务处理器）
    端口和外围设备
    HDMI 2.0
    802.11ac WiFi，蓝牙4.0
    USB3，USB2
    千兆以太网
    12车道MIPI CSI 2.0
    4车道PCIe gen 2.0
    SATA，2x SD卡
    3x UART，3x SPI，4x I2C
    硬件参数
    400针Samtec板对板连接器
    尺寸：50x87mm（1.96“x 3.42”）
    质量：45克
    热转印板（TTP），-25C至85C的工作温度
    5.5-19.6VDC输入功率（在典型负载下消耗10-15W）
### 检查L4T各种系统参数
    查看Jetson TX1 L4T版本：
    head -n 1 /etc/nv_tegra_release
    效果如下：
    # R24 (release), REVISION: 2.1, GCID: 8028265, BOARD: t210ref, EABI: aarch64, DATE: Thu Nov 10 03:51:59 UTC 2016
    查看系统版本：
    cat /etc/lsb-release
    效果如下：
    DISTRIB_ID=Ubuntu
    DISTRIB_RELEASE=16.04
    DISTRIB_CODENAME=xenial
    DISTRIB_DESCRIPTION="Ubuntu 16.04 LTS"
    查看系统位数（32/64）,显示是64位
    getconf LONG_BIT
    查看kernel内核：
    uname -a
    效果：
    Linux tegra-ubuntu 3.10.96-tegra #1 SMP PREEMPT Wed Nov 9 19:42:57 PST 2016 aarch64 aarch64 aarch64 GNU/Linux
    查看内存：
    free -m
    效果：
                  total        used        free      shared  buff/cache   available
    Mem:           3994         629        2829          11         535        3295
    Swap:             0           0           0
    查看CPU详情：
    lscpu
    效果：
    Architecture:          aarch64
    Byte Order:            Little Endian
    CPU(s):                4
    On-line CPU(s) list:   0-3
    Thread(s) per core:    1
    Core(s) per socket:    1
    Socket(s):             4
    Model name:            ARMv8 Processor rev 1 (v8l)
    CPU max MHz:           1734.0000
    CPU min MHz:           102.0000
    查看硬盘分区：
    sudo parted -l
    如果它要求您修复GPT表中的可用空间，只需点击I即可忽略。
    效果：
    nvidia@tegra-ubuntu:~$ sudo parted -l
    [sudo] nvidia 的密码： 
    错误: /dev/mmcblk0rpmb：未确认磁盘标签
    警告: Error fsyncing/closing /dev/mmcblk0rpmb: 输入/输出错误
    重试/Retry/忽略/Ignore? i                                                 
    Model: 通用 SD/MMC 存储卡 (sd/mmc)   
    磁盘 /dev/mmcblk0rpmb: 4194kB
    Sector size (logical/physical): 512B/512B
    分区表：unknown
    Disk Flags: 

    Model: MMC 016G32 (sd/mmc)
    磁盘 /dev/mmcblk0: 15.8GB
    Sector size (logical/physical): 512B/512B
    分区表：gpt
    Disk Flags: 

    数字  开始：  End     大小    文件系统  Name  标志
     1     17.4kB  15.0GB  15.0GB  ext4      APP   msftdata
     2    15.0GB  15.0GB  2097kB            TBC   msftdata
     3    15.0GB  15.0GB  4194kB            EBT   msftdata
     4    15.0GB  15.0GB  2097kB            BPF   msftdata
     5    15.0GB  15.0GB  6291kB            WB0   msftdata
     6    15.0GB  15.1GB  4194kB            RP1   msftdata
     7    15.1GB  15.1GB  6291kB            TOS   msftdata
     8    15.1GB  15.1GB  2097kB            EKS   msftdata
     9    15.1GB  15.1GB  2097kB            FX    msftdata  
    10    15.1GB  15.2GB  134MB             BMP   msftdata
    11    15.2GB  15.2GB  21.0MB            SOS   msftdata
    12    15.2GB  15.3GB  67.1MB            EXI   msftdata
    13    15.3GB  15.4GB  67.1MB            LNX   msftdata
    14    15.4GB  15.4GB  4194kB            DTB   msftdata
    15    15.4GB  15.4GB  2097kB            NXT   msftdata
    16    15.4GB  15.4GB  6291kB            MXB   msftdata
    17    15.4GB  15.4GB  6291kB            MXP   msftdata
    18    15.4GB  15.4GB  2097kB            USP   msftdata
    19    15.4GB  15.8GB  386MB             UDA   msftdata
    这表明您的存储是总共15.8GB的MMC（eMMC固态芯片）
    查看硬盘空间
    df -h
    效果：
    ubuntu@tegra-ubuntu:~$ df -h
    Filesystem      Size  Used Avail Use% Mounted on
    /dev/mmcblk0p1   14G   13G  565M  96% /
    none            2.0G     0  2.0G   0% /dev
    tmpfs           2.0G  124K  2.0G   1% /dev/shm
    tmpfs           2.0G  9.3M  2.0G   1% /run
    tmpfs           5.0M  4.0K  5.0M   1% /run/lock
    tmpfs           2.0G     0  2.0G   0% /sys/fs/cgroup
    tmpfs           400M   48K  400M   1% /run/user/1000
    这显示你的14G根分区96%用完了，你还剩下565M。
    查看正在运行的进程：
    top
    效果：
    top - 08:45:38 up 24 min,  3 users,  load average: 0.09, 0.34, 0.36
    Tasks: 200 total,   1 running, 199 sleeping,   0 stopped,   0 zombie
    %Cpu(s):  1.6 us,  0.7 sy,  0.0 ni, 97.7 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
    KiB Mem :  4090604 total,  2889820 free,   644244 used,   556540 buff/cache
    KiB Swap:        0 total,        0 free,        0 used.  3375100 avail Mem 

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND    
    1354 ubuntu    20   0  844860  83016  45640 S   5.9  2.0   2:54.46 compiz     
    2086 ubuntu    20   0    8884   1812   1220 R   1.6  0.0   0:00.16 top        
    110 root      rt   0       0      0      0 S   1.0  0.0   0:28.86 cfinteract+
    2078 root      20   0       0      0      0 S   1.0  0.0   0:02.60 kworker/0:0
    819 redis     20   0   31008   1576    776 S   0.7  0.0   0:10.89 redis-serv+
    2052 root      20   0       0      0      0 S   0.7  0.0   0:00.39 kworker/1:0
    3 root      20   0       0      0      0 S   0.3  0.0   0:00.59 ksoftirqd/0
    8 root      20   0       0      0      0 S   0.3  0.0   0:04.13 rcu_preempt
    32 root      20   0       0      0      0 S   0.3  0.0   0:00.78 kworker/3:1
    94 root     -51   0       0      0      0 S   0.3  0.0   0:02.25 irq/189-gk+
    332 root      20   0       0      0      0 S   0.3  0.0   0:00.95 dhd_watchd+
    333 root     -51   0       0      0      0 S   0.3  0.0   0:01.80 dhd_dpc    
    830 root      20   0 3276700  27528  13720 S   0.3  0.7   0:10.44 Xorg
    
    查看PCI设备具体的驱动
    
    lspci -v | grep -e ^0 -e driver
    
    查看USB设备
    
    lsusb
    
    效果：
    
    ubuntu@tegra-ubuntu:~$ lsusb
    Bus 002 Device 003: ID 05e3:0616 Genesys Logic, Inc. hub
    Bus 002 Device 002: ID 0955:09ff NVidia Corp. 
    Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
    Bus 001 Device 008: ID 045e:02c2 Microsoft Corp. Kinect for Windows NUI Motor
    Bus 001 Device 006: ID 25a7:0701  
    Bus 001 Device 005: ID 0403:6001 Future Technology Devices International, Ltd FT232 USB-Serial (UART) IC
    Bus 001 Device 004: ID 10c4:ea60 Cygnal Integrated Products, Inc. CP210x UART Bridge / myAVR mySmartUSB light
    Bus 001 Device 003: ID 05e3:0610 Genesys Logic, Inc. 4-port hub
    Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
    
    Jetson TX1实际上有一个USB3.0集线器，但是USB3.0的驱动程序在这里没有配置，因此它们被检测为USB2.0而不是USB3.0。
### 更新源
    说明：
    介绍如何更新TX1源，方法与TK1类似
    注意：ubuntu14.04是trusty ，ubuntu16.04是xenial
    对于安装软件或升级软件必不可少要更新源：
    sudo apt-add-repository universe
    sudo apt-get update
    国内源：
    对于国外源 http://ports.ubuntu.com/ubuntu-ports/ 更新总是太慢
    ARM的ubuntu源不多，可以参考中国科学技术大学的源:http://mirrors.ustc.edu.cn/help/ubuntu-ports.html
    更换方法：
    sudo vim /etc/apt/sources.list
    先注释掉http://ports.ubuntu.com/ubuntu-ports/，在文件末添加如下内容：
    deb http://mirrors.ustc.edu.cn/ubuntu-ports/  xenial main restricted universe multiverse
    deb-src http://mirrors.ustc.edu.cn/ubuntu-ports/  xenial main restricted universe multiverse
    deb http://mirrors.ustc.edu.cn/ubuntu-ports/  xenial-updates main restricted universe multiverse
    deb-src http://mirrors.ustc.edu.cn/ubuntu-ports/  xenial-updates main restricted universe multiverse
    deb http://mirrors.ustc.edu.cn/ubuntu-ports/  xenial-security main restricted universe multiverse
    deb-src http://mirrors.ustc.edu.cn/ubuntu-ports/  xenial-security main restricted universe multiverse
    保存退出，执行更新
    sudo apt-get update
    之后就可以用新的源了。
    安装GIT：
    git工具是基本在任何场合需要的，优先安装
    安装命令如下：
    sudo apt-get install git
### JETPACK自动刷机
    说明：
    介绍如何在Ubuntu主机上安装JetPack
    也适用于虚拟机，但官方推荐使用独立的Ubuntu主机。
    TX1与TK1安装过程类似
    JetPack
    JetPack 3.0是为Jetson开发套件安装软件工具和操作系统的工具。
    JetPack 3.0可用于将开发工具安装在Jetson开发套件（Jetson TX1或Jetson TK1）上
    列出了所有系统要求以及可安装的不同工具
    只需确保选择“TX1”作为要烧录的设备类型
    
    注意：
    除了Jetson TX1之外，您还需要另一台带有Intel或AMD x86处理器的台式机或笔记本电脑。
    这些类型的机器通常被称为PC的个人电脑。该计算机被称为烧录过程的主机。
    JetPack是一个x86二进制文件，不能在基于ARM的机器上运行。
    参考主机配置：戴尔Inspiron 3000系列i3847-3850BK桌面（3.5 GHz英特尔酷睿i3-4150处理器，8GB DDR3，1TB硬盘，Windows 8.1，      Ubuntu安装双引导）被用作主机。
    注册帐号：
    要官方下载JetPack，需要先注册一个帐号。
    进入https://developer.nvidia.com网站完成帐号注册并登录。
    JetPack安装
    首先在Ubuntu Host上下载最新的JetPack TX1最新的安装文件：JetPack-.run
    其中对应的是相应的版本，目前最新版本：jetpack-l4t-3_
    JetPack介绍页面和下载地址：
    https://developer.nvidia.com/embedded/jetpack
    历史版本：
    https://developer.nvidia.com/embedded/jetpack-archive
    现在到软件包在上传的Ubuntu主机
    更改执行权限：
    $ chmod +x JetPack-L4T-3.0-linux-x64.run
    执行安装
    $ sudo ./JetPack-L4T-3.0-linux-x64.run
    首先是安装包选择界面，一般默认即可。
    检查下是否选择上了CUDA Toolkit和OpenCV for Tegra，这两个包一定要装
    接受协议
## 2 删除Ubuntu的用户及文件






## 3 TXI挂载SD卡

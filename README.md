# woyun-ros
# ros机器人开发说明

### 1 TX1嵌入式开发板介绍
    
  ～Jetson TX1是NVIDIA第二代嵌入式开发者套件。
  ～虽然只有信用卡大小，但Jetson TX1 GPU模块的浮点运算能力却达到1 Teraflops，相比Jetson TK1有巨幅提升。
  ～内建256个CUDA核心的NVIDIA Maxwell GPU，64位ARM A57 CPU，4GB LPDDR4内存，16GB闪存，蓝牙，802.11ac Wi-Fi模块和千兆以太网卡，运     行Linux for Tegra操作系统。
  ～主板提供了USB 3.0 A型，USB 2.0，HDMI，M.2键E，PCI-E x4，千兆以太网，SD，SATA数据和供电等接口
  ～针对硬件创客的扩展接口：GPIO，I2C，I2S，SPI，带流控制的TTL UART，显示器扩展头和摄像头扩展头一个不少
  ～如此强大的性能，Jetson TX1显然是智能无人机，机器人最理想的嵌入式解决方案。
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
# 2 删除Ubuntu的用户及文件






# 3 TXI挂载SD卡

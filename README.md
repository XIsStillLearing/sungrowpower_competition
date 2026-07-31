# 阳光电源杯功率器件波形测试手册

<!-- 本 README 由测试手册.docx 转换并针对 GitHub Markdown 整理。 -->

> 本仓库集中发布上位机、下位机固件与源码、串口 Beta 版本以及波形处理软件。本文档用于说明测试接线、操作流程、判据与数据处理方法。

## 发布包下载

- [查看全部 Release](https://github.com/XIsStillLearing/sungrowpower_competition/releases)
- 正式版：上位机、下位机（含源码）、处理软件
- Beta：上位机（串口）、下位机（串口，含源码）

> [!WARNING]
> 本项目涉及高压功率器件测试。上电前必须确认放电、联锁、接线、探头量程和保护逻辑；短路测试必须采用人工单次触发，并在每次触发后检查波形与器件状态。

---

## 手册说明与注意事项

### 测试注意事项

- MOSFET 安装和焊接时为罗氏线圈及差分探头留出测量空间，提供手册建议预留约 0.7 cm；不得为方便测量而改变主功率回路寄生参数。

- 所有栅极电压均以 Kelvin Source 为参考；高压上电前确认放电、联锁、探头量程和共模范围。

- 每条数据必须保存实际探头型号、传播延迟、示波器 deskew、带宽限制和通道映射。已经在示波器内补偿的数据，软件 deskew 保持 0 ns。

- 程序烧录：
- 使用arm烧录器连接控制板，接线如下图
- [keil芯片pack文件](https://github.com/XIsStillLearing/sungrowpower_competition/releases/tag/JTAG%E7%83%A7%E5%BD%95-v1.0)
- [源码地址](https://github.com/XIsStillLearing/sungrowpower_competition/releases#release-%E4%B8%8B%E4%BD%8D%E6%9C%BA-v1.2.5)
![测试手册插图 1](docs/assets/test-manual/image1.jpeg)
![测试手册插图 2](docs/assets/test-manual/image2.png)

- 烧码器连接：

 JLINK上面四根杜邦线：SWDIO，SWCLK,VCC,GND

 分别连接Mini Board如下：

 VCC->VDDIO(JP3:pin 1,2,3,4皆可)

 GND->VSS(JP4:pin1,2,3,4皆可)

 SWDIO-> SWDIO (JP3:pin5,6皆可)

 SWCLK-> SWCLK (JP3:pin7,8皆可)

MiniBoard板上丝印显示都有VDDIO,VSS,SWDIO, SWCLK,对着丝印名称连接即可

- 注意:

  (1)如果把JLINK上的VCC连接至板上的VDDIO,则板上USB线可不用连接,

  如果连接了板上的USB线供电,则最好不要连接JLINK上的VCC到板子上

  (2)串口线必须连走USB线连接至板子上

- Keil配置

(1)J-LINK Driver需要安装至电脑上,一般WINDOWS会自动安装有,可以在设备管理器确认下

(2)在打开工程后,点击”Option”->”Debug”选择”JLINK/JTRACE”如下图

![测试手册插图 3](docs/assets/test-manual/image3.png)

(3)如果此时已连接JLINK,可以进右侧Setting,进入如果正确识别出JLINK ID号,则调试器识别成功,就可以按照KEIL正常烧码和仿真流程操作了,如下图:

![测试手册插图 4](docs/assets/test-manual/image4.png)
-使用串口烧录

- 通信设置：比赛中使用治具底部连接测试版，各组可自行飞线于can盒连接至电脑进行通信。

需要提前预can驱动，否则识别不到can上位机不运行。

![测试手册插图 5](docs/assets/test-manual/image5.png)

![测试手册插图 6](docs/assets/test-manual/image6.jpeg)

-上位机使用：

1.确保接线正常之后打开上位机，界面如图：

![测试手册插图 7](docs/assets/test-manual/image7.png)

2.点击connect：
![2.点击connect：](docs/assets/test-manual/image8.png)

3.连接正常时，右上角can从离线变为空闲，右下角可以操作，界面：
![3.连接正常时，右上角can从离线变为空闲，右下角可以操作，界面：](docs/assets/test-manual/image9.png)

4.点击start下位机开始执行：

![测试手册插图 10](docs/assets/test-manual/image10.png)

### 示波器与通道配置

| 通道 | 测量 | 探头 | 传播延迟 |
| --- | --- | --- | --- |
| CH1 | 主动管 Vgs |
| CH2 | DUT Vds |
| CH3 | Id／电感电流 |
| CH4 | 被动管 Vgs | 



### 75°工况

使用加热片将散热器加热至75°，保持2min后，使用热成像观察散热器，MOS管温度状态，合格后开始测试，在此工况下将下文测试重复一遍。
![使用加热片将散热器加热至75°，保持2min后，使用热成像观察散热器，MOS管温度状态，合格后开始测试，在此工况下将下文测试重复一遍。](docs/assets/test-manual/image11.jpeg)

- 图 2 75°控温控制盒示意

![测试手册插图 12](docs/assets/test-manual/image12.jpeg)

## 静态工况测试

### 测试目的、条件与判据

静态测试在不加主功率高压的条件下验证两路栅极驱动幅值、互补关系、波形质量和连续运行稳定性。当前台架设置为 30 kHz 、Q1 约 10% 、Q2 互补， 目标记录时长为 1 min，所有栅极电压以 Kelvin Source 为参考。评价内容为：

1. 两路高电平均为 +18.0 ± 1.0 V；

2. 两路低电平均为 -5.0 ± 1.0 V；

3. 无异常振荡、多次触发、毛刺、复位、误保护和故障锁存；

4. 示波器记录或日志覆盖完整 1 min。

5.ch1接于Q1Vgs，ch2接于Q1Vds，ch3罗氏线圈接于MOS管Ids，ch4接于Q2Vgs，测试接线与上位机界面如图

- 图 3上位机界面

![测试手册插图 13](docs/assets/test-manual/image13.png)

### 波形样例
![波形样例](docs/assets/test-manual/image14.jpeg)

- 图 5静态工况测试样例
![图 5静态工况测试样例](docs/assets/test-manual/image15.png)

## 双脉冲开关状态测试

### 工况与测试要求



| 工况 | 母线电压/V | 电流倍数 | 目标电流 |
| --- | --- | --- | --- |
| DPT-250-15 | 250 | 1.0 IN | 15 |
| DPT-250-45 | 250 | 3.0 IN | 45 |
| DPT-500-15 | 500 | 1.0 IN | 15 |
| DPT-500-45 | 500 | 3.0 IN | 45 |

ch1接于Q1Vgs，ch2接于Q1Vds，ch3罗氏线圈接于MOS管Ids，ch4接于Q2Vgs，接法与上文一致。每个工况进行五组测试，保存示波器截图和波形，在软件中对Eon，Eoff进行计算并对其他值进行评估。
![测试手册插图 16](docs/assets/test-manual/image16.png)

图 6上位机界面
<img width="1041" height="645" alt="image" src="https://github.com/user-attachments/assets/dfda86f4-0daf-4ead-8281-18ac6dbd82c6" />

每次同步采集主动管 Vgs 、DUT Vds 、Id 和被动管 Vgs。结果报告原始事件电流，并按当前波形评估工作台的目标电流 ±5% 工程标记评价。软件输出 Eon 、Eoff、Etotal、事件电流、积分窗口、 Vds 过冲、dv/dt 、di/dt、主动管栅极幅值、被动管串扰。
## 单脉冲短路与过流保护测试

### 测试方法与必要信号

在测试治具上将负载和电感短接，在低母线电压下开始。每次同步记录主动管 Vgs、 Vds 、Id 和FAULT/保护输出。保护响应时间必须从明确的电流越阈时刻计算至 FAULT有效沿。Mos管短路耐受时间2us，程序逻辑为从0.1us开始每隔0.2us为一个区间一直到3us，各队手动发送脉冲一直到触发保护。保留波形在软件中进行处理。

注意不要使用单脉冲spt模式。

![测试手册插图 30](docs/assets/test-manual/image30.png)

![测试手册插图 31](docs/assets/test-manual/image31.png)

上位机操作：先手动确认三个量，解锁脉冲，选择脉宽，触发，保护需在2us内触发。

![测试手册插图 32](docs/assets/test-manual/image32.png)

![测试手册插图 33](docs/assets/test-manual/image33.png)

## 双向 Buck 工况测试

其他接线不变，功率板输出由治具上引出，接致电子负载，将罗氏线圈从mos管Ids引脚修改到电感输入引脚，ch1，ch2，ch3保持原先Q1 Vgs，Q1 Vds，Q2 Vgs，在500V，45欧与15欧（三倍负载）工况下进行测试。测试过程中使用电子负载改变负载。

![测试手册插图 34](docs/assets/test-manual/image34.jpeg)

*图 19电子负载接线*

![测试手册插图 35](docs/assets/test-manual/image35.jpeg)

*图 20上位机界面*

![测试手册插图 36](docs/assets/test-manual/image36.png)
![测试手册插图 37](docs/assets/test-manual/image37.png)

### 当前实际配置与数据条件

**表 13 Buck 当前实际配置**

| 指标 | 参数 |
| --- | --- |
| 母线电压 | 500 V |
| 开关频率 | 固定 30 kHz |
| 占空比 | Q1 固定 10%，Q2 互补 |
| 额定负载/时长 | 45 Ω / 60000 ms |
| 三倍过载/时长 | 15 Ω / 20 ms |
| 电流上限 | 100 A |
| 必要信号 | Q1/Q2 Vgs、Vds、电流、Vout、FAULT、负载标记 |

在输出电压近似不变时， 45 Ω 切换至 15 Ω 的理论电流倍数为

![测试手册插图 38](docs/assets/test-manual/image38.png)

正式结果应以负载切换标记前后的实测 RMS电流计算：

![测试手册插图 39](docs/assets/test-manual/image39.png)

### 500 V 功能样例

*图 21500Vbuck波形*

![测试手册插图 40](docs/assets/test-manual/image40.png)

**表 14 tek0029 40 ms Buck 样例全部结果**

| 指标 | 结果 |
| --- | --- |
| 记录时长/有效周期 | 40.000 ms/1208 |
| 频率/周期 | 30.240 kHz/33.069 μs |
| Q1 导通时间/占空比 | 3.329 μs/10.068% |
| Q2 导通时间 | 27.790 μs |
| 两方向死区/重叠 | 0.984/0.965 μs；0 次重叠 |
| Q1 Vgs 高/低中位数 | 17.50/—4.50 V |
| Q2 Vgs 高/低中位数 | 17.60/—7.20 V |
| 周期/Q1/Q2 脉宽 CV | 0.015%/0.052%/0.016% |
| 短毛刺段 | 4 个 |
| Vds 关断/峰值/谷值 | 500/580/—60 V |
| 电流均值/RMS | 0.623/2.653 A |
| Vout/过载/FAULT | 暂无 |

*图 22buck软件波形分析*
![图 22buck软件波形分析](docs/assets/test-manual/image41.png)

![测试手册插图 42](docs/assets/test-manual/image42.jpeg)

**表 15 2 s Buck界面关键结果**

| 指标 | 参数 |
| --- | --- |
| 有效周期/频率 | 30671/30.296 kHz |
| Q1/Q2 导通时间 | 3.488/27.876 μs |
| 两方向死区 | 0.893/0.902 μs |
| Vds 峰值/谷值 | 750/__410 V |
| 静态覆盖/过载 | 2.000 s |

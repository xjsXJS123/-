# STM32

### STM32片上资源/外设

<img src="C:\Users\Administrator\Pictures\Screenshots\屏幕截图 2026-10-24 100141.png"  />https://img.remit.ee/i/1TUZZJGPN86U

### 最小系统电路

##### 复位电路

##### ![](C:\Users\Administrator\Pictures\Screenshots\屏幕截图 2026-10-24 104906.png)https://img.remit.ee/i/NYZxkD1JZd47

上电复位：上电的瞬间，C3电容没有电，电源开始向电容充电，此时电容呈现短路状态，NRST引脚产生低电平，电容逐渐充满电，电容相当断路，此时NRST被R1上拉为高电平。其中低电平可以提供STM32的上电复位信号，但是电容充电是非常快的，所以单片机几乎是在一瞬间就复位了。

手动复位：左边并联的按键提供手动复位的功能，按下按键时，电容被放电，且NRST引脚通过按键直接接地，手动产生低电平信号，松开按键后，NRST又回归高电平，单片机从复位状态转为工作状态。

##### 晶振https://img.remit.ee/i/YcamuV6VRh0f

![](C:\Users\Administrator\Pictures\Screenshots\屏幕截图 2026-10-24 105958.png)

##### 启动配置https://img.remit.ee/i/mBZg5VX2gEJT

![](C:\Users\Administrator\Pictures\Screenshots\屏幕截图 2026-10-24 110044.png)

H1相当于开关，拨动开关，选择让BOOT0接入3V3或GND

##### 下载端口https://img.remit.ee/i/djoAFLVCWKkT

![](C:\Users\Administrator\Pictures\Screenshots\屏幕截图 2026-10-24 110119.png)

##### STM32及供电https://img.remit.ee/i/Bg307Iq7Jd4u

![](C:\Users\Administrator\Pictures\Screenshots\屏幕截图 2026-10-24 110205.png)

### 下载程序步骤

1. BOOT0拉高（3V3），BOOT1拉低(GND)

2. 给板子上电/按复位键——进入ISP bootloader

3. 打开烧录软件——STM32CubeProgrammer/FlyMcu

4. 选择串口COM口、波特率（F4的板子选76800，F1可以任意）https://img.remit.ee/i/rZP4BX7kLaKi

   ![](C:\Users\Administrator\Pictures\Screenshots\屏幕截图 2026-10-27 115109.png)

5. 选中hex/bin固件，开始下载烧录

      点击上图那三个点点的地方（选择文件），选完之后点击上图的开始编程

6. 下载完成，断电，BOOT0改回低电平。再上电，跑刚烧的程序。

### GPIO的八种模式

四种输入：输入浮空、输入上拉、输入下拉、模拟输入

四类输出：开漏输出、开漏复用、推挽输出、推挽复用输出

https://img.remit.ee/i/mXXkoDa41xwR

![](C:\Users\Administrator\Pictures\Screenshots\屏幕截图 2026-10-28 083010.png)

1. 输入浮空：上下拉电路均关闭，输出全由STM32自身控制

2. 输入上拉：IO口内接上拉电阻，如果此时IO口外部没有信号输入或者引脚悬空，IO口默认高电平，如果IO口输入低电平，那么引脚就为低电平，MCU读取到的就是低电平。（STM32的输入上拉时弱上拉，上拉电阻阻值高，若需要大电流须要外部上拉）

3. 输入下拉：IO口内接下拉电阻，如果此时IO口外部没有信号输入或者引脚悬空，IO口默认低电平，如果IO口输入高电平，那么引脚就为高电平，MCU读取到的就时高电平。

4. 模拟输入：信号直接通过最上方的那条线路，不通过施密特触发器。当GPIO用于模拟功能时，引脚的上、下拉电阻是不起作用的。除了 ADC 和 DAC 要将 IO 配置为模拟通道之外其他外设功能一律 要配置为复用功能模式

5. 开漏输出：在开漏输出模式时，只有N-MOS管工作，如果我们控制输出为0，低电平，则P-MOS管关闭，N-MOS管导通，使输出低电平，I/O端口的电平就是低电平，若控制输出为1时，高电平，则P-MOS管和N-MOS管都关闭，输出指令就不会起到作用，此时I/O端口的电平就不会由输出的高电平决定，而是由I/O端口外部的上拉或者下拉决定   如果没有上拉或者下拉 IO口就处于悬空状态，并且此时施密特触发器是打开的，即输入可用，通过输入数据寄存器GPIOx_IDR可读取I/O的实际状态。，I/O口的电平不一定是输出的电平

6. 推挽输出：在推挽输出模式时，N-MOS管和P-MOS管都工作，如果我们控制输出为0，低电平，则P-MOS管关闭，N-MOS管导通，使输出低电平，I/O端口的电平就是低电平，若控制输出为1 高电平，则P-MOS管导通N-MOS管关闭，使输出高电平，I/O端口的电平就是高电平，  外部上拉和下拉的作用是控制在没有输出时IO口电平，此时施密特触发器是打开的

7. 关于P-MOS和N-MOS：P-MOS在左侧连接的VSG（栅极G和源极S之间的电压差）足够低的时候才能导通（G的电压比S的电压低） ；N-MOS在左侧连接的VSG足够高的时候才能连通。

### GPIO相关配置寄存器

1. 端口模式寄存器（GPIOx_MODER）：每两位控制一个IO口，共32位![](C:\Users\Administrator\Pictures\Screenshots\屏幕截图 2026-10-28 085116.png)https://img.remit.ee/i/sl7t0QDDkJUJ

2. 端口输出类型寄存器（GPIOx_OTYPER）：每位控制一个IO口，低16位控制，高16位没用。![](C:\Users\Administrator\Pictures\Screenshots\屏幕截图 2026-10-28 085559.png)https://img.remit.ee/i/JTxUCoTIJ3kO
3. 端口输出速度寄存器（GPIOx_OSPEEDR）:每两位控制一个IO口![](C:\Users\Administrator\Pictures\Screenshots\屏幕截图 2026-10-28 085750.png)https://img.remit.ee/i/tSSRiOxeAtgG

4. 端口上拉/下拉寄存器（GPIOx_PUPDR）:每两位控制一个IO口![](C:\Users\Administrator\Pictures\Screenshots\屏幕截图 2026-10-28 085921.png)https://img.remit.ee/i/ZXfS0fUu8NmN
5. 端口输入数据寄存器（GPIOx_IDR）：每位控制一个IO口
6. 端口置位/复位寄存器（GPIOx_BSRR）:https://img.remit.ee/i/8QY4Dbtjvk8q![](C:\Users\Administrator\Pictures\Screenshots\屏幕截图 2026-10-28 090403.png)

0：原来输出是什么就是什么                 1：位0---15无论原来输出什么，最后一定输出1；位16---31，最后一定输出0

7. 端口位复用功能寄存器

### 时钟树

![download](E:\download.png)https://img.remit.ee/i/yCtwKEUjKm1X

时钟线总线HCLK直接连接处理器、内存、DMA以及各种外设，提供时钟信号。

其中在处理器中存在一个SysTick(系统滴答)，为程序提供时间基准。

分频器·：为频率做除法。

单片机上的各种外设并没有直接连接在HCLK上，而是分别连接在两个APB（先进外设总线，可以通过桥接器连接到AHB总线上，与其他连接在AHB总线上的部分进行通信）总线上。https://img.remit.ee/i/NUGSIVN2sDNY

![](C:\Users\Administrator\Pictures\Screenshots\屏幕截图 2026-10-29 225153.png)

 时钟源：HSI（高速内部时钟）和HSE（高速外部时钟，需要外接晶振）。

SYSCLK:时钟源接入HCLK的数值。

PPL锁相环---倍频器。

FCLK：自由运行时钟，STM32进入停止模式时，AHB停止运行，HCLK停止传输时钟脉冲，FCLK可以中断停止模式。其信号的真正来源是AHB的预分频器。

CSS时钟安全系统。

### Keil5新建工程的步骤

1. 点击project新建工程
2. 左边框点击manage project items加入工程所需要的源文件
3. 点击魔法棒>>C/C++加入源文件所对应的头文件路径，填写好define；魔法棒>>OutPut,勾选Create HEX File.

### MOS管的原理

https://img.remit.ee/i/lUJxLRyGc9dI

![](C:\Users\Administrator\Pictures\Screenshots\屏幕截图 2026-10-30 165314.png)

栅极与源极连通，在绝缘层左侧的金属快上聚集正电粒子，那么P型衬底上的电子就会被正电粒子吸引到左侧，在两个N区域中间组成一条电子通道连接两个N区域，此时金属、绝缘层、P板三个结合起来相当于电容。有了P行衬底 上面的电子之后，外围电路电流可以按如图方式导通。里面的电路电源电压越大，被吸引来的电子就越多，两个N区域之间的通路就越大，外围电路的电流也就越大。上面的寄生二极管阻止电流直接从D极流向S极（如下图所示）。https://img.remit.ee/i/CuZUEbo3MkRH

<img src="C:\Users\Administrator\Pictures\Screenshots\屏幕截图 2026-10-30 170151.png" style="zoom:50%;" />

https://img.remit.ee/i/sQpe0HfIfDM7

<img src="C:\Users\Administrator\Pictures\Screenshots\屏幕截图 2026-10-30 170336.png" alt="屏幕截图 2026-10-30 170336" style="zoom:50%;" />

### GPIO为什么要接口时钟？

时钟相当于外设的“工作电源开关”，GPIO的寄存器、MOS管驱动电路都要跟着时钟信号才能正常工作。没开时钟>>寄存锁死>>写进去的配置都不生效。这样还可以省电，不需要的GPIO端口关掉时钟，外设停止工作，降低功耗。（写程序时，先开时钟，再初始化GPIO）
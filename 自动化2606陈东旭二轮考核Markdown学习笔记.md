# STM32学习笔记

## Part 1 简介

STM32是ST公司基于ARMCortex-M内核开发的32位微控制器

STM32常应用在嵌入式领域，如智能车、无人机、机器人、无线通信、物联网、工业控制、娱乐电子产品等

STM32功能强大、性能优异、片上资源丰富、功耗低，是一款经典的嵌入式微控制器

## Part 2 新建工程

![image-20261031164900955](C:\Users\dong'xu\AppData\Roaming\Typora\typora-user-images\image-20261031164900955.png)

首先新建一个文件夹作为Project存放地，并新建USER等文件夹方便放置后续文件

![image-20261031164733816](C:\Users\dong'xu\AppData\Roaming\Typora\typora-user-images\image-20261031164733816.png)

然后打开keil5如图点击新建工程，选择之前的文件夹中的USER进行存储，再根据自己需要的芯片选择

![image-20261031170042939](C:\Users\dong'xu\AppData\Roaming\Typora\typora-user-images\image-20261031170042939.png)

接下来添加头.C文件等到文件夹

![image-20261031170312685](C:\Users\dong'xu\AppData\Roaming\Typora\typora-user-images\image-20261031170312685.png)

回到keil，添加文件夹，注意文件夹名字要是和之前命名的保持一致

![image-20261031170921936](C:\Users\dong'xu\AppData\Roaming\Typora\typora-user-images\image-20261031170921936.png)

继续把头文件等按照名字加入到对应的keil文件夹内

![image-20261031171249628](C:\Users\dong'xu\AppData\Roaming\Typora\typora-user-images\image-20261031171249628.png)

![](C:\Users\dong'xu\AppData\Roaming\Typora\typora-user-images\image-20261031171523123.png

![image-20261031171644482](C:\Users\dong'xu\AppData\Roaming\Typora\typora-user-images\image-20261031171644482.png)

点击魔术棒—c/c++，将有头文件的文件夹加入到path中,并输入define进行配置

## Part 3 跑马灯实验

![image-20261031205135647](C:\Users\dong'xu\AppData\Roaming\Typora\typora-user-images\image-20261031205135647.png)

GPIO输出方式：推挽输出（可以输出强高低电平，连接数字器件）

如图，如果PF9输出0（即0v低电平），则LED0左右无电压差，灯灭，反之则亮

![image-20261031210653350](C:\Users\dong'xu\AppData\Roaming\Typora\typora-user-images\image-20261031210653350.png)

![image-20261031210819321](C:\Users\dong'xu\AppData\Roaming\Typora\typora-user-images\image-20261031210819321.png)

函数库以及寄存器IO口







### 正式开始编程

首先在Template中新建一个HARDWARE文件夹，再按照新建工程的步骤新建led.c和led.h文件用于初始化led灯

再如图所示，在led.h头文件中进行以下定义，避免头文件被多次引用之后造成定义重复

![image-20261031233524592](C:\Users\dong'xu\AppData\Roaming\Typora\typora-user-images\image-20261031233524592.png)

再进入到led.c文件进行如下编程，将led灯初始化

![image-20261031233816085](C:\Users\dong'xu\AppData\Roaming\Typora\typora-user-images\image-20261031233816085.png)

首先前两行引用两个头文件再创建LED_Init函数

然后调用RCC使能时钟，用的AHB1口（F407的GPIO时钟挂载在AHB1总线上）（函数在这个里面找![image-20261031234707374](C:\Users\dong'xu\AppData\Roaming\Typora\typora-user-images\image-20261031234707374.png))。进入RCC入口参数，找到有效信息判断，找到我们需要初始化的IO口（GPIOF那个)和打开它（ENABLE)

在写IO口初始化函数GPIO_INit（在这里找![image-20261031235536875](C:\Users\dong'xu\AppData\Roaming\Typora\typora-user-images\image-20261031235536875.png))。然后进入它的入口参数，再如图![image-20261031235727255](C:\Users\dong'xu\AppData\Roaming\Typora\typora-user-images\image-20261031235727255.png)先去选好左边的，然后再去看右边的是什么（GPIO_InitTypeDef)，然后再复制到RCC前面，定义结构体名字（这里叫做GPIO_InitTypeDefStructue)，最后把它复制到Init函数右边位置，记得加&定位

接下来在RCC下面对GPIO_InitTypeDefStructue的模式 频率 输出方式 调用引脚这些进行定义

最后写GPIO_SetBits(GPIOF,GPIO_Pin_9);（函数在![image-20261031235536875](C:\Users\dong'xu\AppData\Roaming\Typora\typora-user-images\image-20261031235536875.png)里面)对这一引脚输出高电平

至此对F9这一引脚初始化完成，F10同理，不过将F9换成F10





接下来对主程序main.c进行编程

![image-20261101001219939](C:\Users\dong'xu\AppData\Roaming\Typora\typora-user-images\image-20261101001219939.png)

首先引用三个需要使用的函数头文件以及函数main

接着初始化函数delay（在这里找![image-20261101001406677](C:\Users\dong'xu\AppData\Roaming\Typora\typora-user-images\image-20261101001406677.png))入口参数填成时钟默认的168M，还有LED_init函数

接着是一个while死循环，等下会写灯的延时开关，用while（1）进行循环，不然代码只跑了一遍就没了

然后先是高平电压灭灯GPIO_SetBits(GPIOF,GPIO_Pin_9);   
		                      GPIO_SetBits(GPIOF,GPIO_Pin_10);

延迟500ms

再是低平电压点灯 GPIO_ResetBits(GPIOF,GPIO_Pin_9);
		               GPIO_ResetBits(GPIOF,GPIO_Pin_10);

同样的500ms

最后编译保存



到此编译完成



### 最后写入芯片

![image-20261102163606671](C:\Users\dong'xu\AppData\Roaming\Typora\typora-user-images\image-20261102163606671.png)

![image-20261102163639064](C:\Users\dong'xu\AppData\Roaming\Typora\typora-user-images\image-20261102163639064.png)

选择好芯片以及对应的Debug模式进行烧录













## Part 4 时钟树

### GPIO输出四个模式

1.推挽输出：可以输出高低电平，驱动能力比较强，常用于点灯

2.开漏输出：只能输出低电平，常用于总线通讯

3.复用推挽输出：与推挽输出功能一样，但由内部硬件控制

4.复用开漏输出：与开漏输出功能一样，但由内部硬件控制

### stm32时钟树

#### 是什么

STM32 内部有非常复杂的时钟网络，被称为“时钟树”。时钟就像是单片机的“脉搏”，没有时钟，所有的外设都无法工作。

为什么需要时钟树？

单片机内部的各个外设（如 CPU、GPIO、串口、定时器）对速度的要求不同。

· CPU 需要极高的频率来保证运算速度（F407 可达 168MHz）。
· 串口、I2C 等外设只需要较低的频率。
· 为了兼顾高性能和低功耗，需要一套“时钟树”来对基础频率进行倍频（加速）和分频（减速），然后分发给不同的外设。

#### 工作原理

第一步：时钟源头

· HSI（内部高速时钟）：芯片自带的 RC 振荡器（通常 16MHz），精度差，一般只做备用。
· HSE（外部高速时钟）：外部晶振提供（通常 8MHz），精度极高。这是系统主频的首选源头。

第二步：PLL 锁相环

· 8MHz 的频率不足以让 CPU 跑得快，所以需要送入 PLL（锁相环） 进行倍频。
· 在标准库的配置下，8MHz 的 HSE 经过 PLL 倍频（×21），得到了 168MHz 的频率。这就是单片机运行的最高速度。

第三步：系统时钟

· PLL 输出的 168MHz，经过选择器后，正式成为 SYSCLK（系统时钟）。
· 它是整个单片机内部最高级别的时钟节拍。

第四步：总线分频

SYSCLK（168MHz）并不是直接给所有外设用，而是分给了几条不同的总线（相当于高速公路的不同车道）：

· AHB 总线：通常不分频，直接跑 168MHz。（重点：我们点灯用的 GPIO 就挂载在 AHB1 总线上！）
· APB2 总线：通常 2 分频，频率为 84MHz。挂载了串口 1、SPI1 等高速外设。
· APB1 总线：通常 4 分频，频率为 42MHz。挂载了串口 2、定时器 2 等低速外设。




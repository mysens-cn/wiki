title: NRF24M模块
date: 2019-04-18T2:54:30.859Z
tags: [mysensor-中文站]
categories: [mysensor]
---


&nbsp;&nbsp;&nbsp;&nbsp;NRF24M模块是一款半SMT产品，采用atmega328p-mmhr系列。尺寸仅19.5*14.6mm，输出anlnog接口A2-A5,数字接口D2-D7，全部io均可以被使用，A4,A5为I2C接口，D2，D3为物理中继接口。搭配国产中科微SI24R1芯片乐鑫模块。该模块经测试稳定性不错，丢包率较其它国产打磨模块低，并且兼容性比较好。

##PCB示图

###PCB实物图

>目前最新版本为1.1版本，更新了刷机脚，全部在正面即可完成刷机

#### v1.1版

![PCB TOP](/images/nrf24msw11.jpg)

#### v1.0版

![PCB TOP](/images/nrf24msw.jpg)

###LAYOUT图

>PCB脚位1.1版本刷机将更容易，1.0版本需要从背面取脚，正面取RST 


#### v1.1版

![PCB TOP](/images/nrf24mtti11.jpg)


#### v1.0版

![PCB TOP](/images/nrf24mtti.jpg)


!!! note "说明"

    1.模块SMD和接口均为1.27间距。
    
    2.模块自带LDO-662K压降芯片【1.1版本默认取消】，功耗实测大概在7ua左右，电压范围最大可达6.2V左右，如果项目中你不需要，那么清你拆下这个SOT23-3的黑色3脚芯片，并短接上图中VCC3V蓝色区域。
    
    3.刷机时，刷机夹请取电正方向V-G-RST-背面夹住SI24R1模块，取其SCK-MOSI-MISO即可。
    
##模块参数
&nbsp;&nbsp;&nbsp;&nbsp;模块植入性强，尺寸小，未来众多Mysensor项目将会采用此模块，目前已经完成门磁，花草，温湿度气压光线，人体感应等具体请参看本节项目。元件LED为台产亿光，容阻为三星及国巨。

###功耗实测
  &nbsp;&nbsp;&nbsp;&nbsp;模块集合662K，关闭WDT休眠 状态下消耗12ua-14ua左右，无休眠状态下功耗14ma左右。远远低于esp系列。
  
  &nbsp;&nbsp;&nbsp;&nbsp;针对电池系列，去掉662K，由于atmega芯片时间久(开源支持也是相对完善的)，WDT目前不是需要消耗4UA左右，针对非定时唤醒的项目 ，待机仅消耗4-5ua，年待机消耗不足50mah，针对定时唤醒设备，待机7-9ua，年消耗不足80mah。
  
   &nbsp;&nbsp;&nbsp;&nbsp;发送数据时，模块将会启动SI24R1，瞬间电流高在14ma，按官方宣称，节点单次数据发送消耗大概150ms左右，加上程序，大概消耗300ms，该部分没有示波器无法实测。
   
&nbsp;&nbsp;&nbsp;&nbsp;经过简单测试，对比某米zigbee产品，其待机低于1ua，发射时功耗相当，但由于zigbee是新产品，运行频率和速度远远超过atmega328p 8Mhz，故其无论待机还是发射消耗都是有优势的，特别是待机上，发射消耗不会差出很多水平。不过我们可以使用电池容量来补短板。
  
###整合特征



- &nbsp;&nbsp;&nbsp;&nbsp;模块D2脚集成10M拉高电阻，合适二进制传感器，是制作门磁，触发，雨滴，无线按键的快捷选择，直接外接设备和电池，即可快速完成。

- &nbsp;&nbsp;&nbsp;&nbsp;模块D3、D5、D6为物理PWM脚，非esp系列的模拟。合适制作调光电路。

- &nbsp;&nbsp;&nbsp;&nbsp; A5脚为SCL,A4为sda，直接可接入I2C设备使用。

- &nbsp;&nbsp;&nbsp;&nbsp; A2-A5均可以当ADC脚使用，支持5V电压测量。
title: NRF24-Relays
date: 2019-08-07T2:54:30.859Z
tags: [mysensor-中文站]
categories: [mysensor]
---

 &nbsp;&nbsp;&nbsp;&nbsp;开关模块使用328P 芯片处理器、无线使用SI24R1、具有自动组网中继、弹性设计、“离线可控”等优点，是一款非常适合改装的开关模块
  
  - 继电器品牌为HF49FD-005-1H11T，特性材质，防粘连效果好，应用负载150W-LED不粘
  - 继电器支持模块在线切换，点控或者翻转只需要指令操作即可完成
  - 内置P2P程序，支持遥控直接点到点操作，无需经过外部自动化平台
  - 支持状态灯输出，自动翻转，适合物理开关

  
##PCB制造


!!! Warning "版权提示"

       &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;继电器制造文件仅供个人极客使用，严禁个人或者公司商业运作!

###PCB设计

![PCB TOP](/images/relays-5a-3.jpg)

![PCB TOP](/images/relays-5a-2.jpg)

![PCB TOP](/images/relays-5a-1.jpg)

> {==继电器支持5V 3V外接输出，使用跳焊并线，S1 S2 S3分别接开关1 开关2 开关3 ，L1 L2 L3分别接对应的LED灯，状态反转==}

###PCB文件

&nbsp;&nbsp;&nbsp;&nbsp;<a href="https://github.com/huexpub/nrf24-relays" target="_blank">Gerber下载</a>

###BOM表

&nbsp;&nbsp;&nbsp;&nbsp;BOM表对应上传到GITHUB制造文件里，请直接参考购买

##固件下载

&nbsp;&nbsp;&nbsp;&nbsp;固件默认单双三开统一固件，采用属性定义的办法定义继电器数量，并存入EEPROM，仅设置一次即可。

&nbsp;&nbsp;&nbsp;&nbsp;<a href="https://fimware.mysensor.pub/?dir=NRF24M/NRF24-RELAYS" target="_blank">Hex下载</a>

 {++下载后请参考NRF24M-固件写入，写入带BOOT 文件名的固件++}
 

!!! note "提示"

       &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;继电器完成固件后，请定义自定义属性，默认为单开，翻转模式
       
       

##属性定义

&nbsp;&nbsp;&nbsp;&nbsp;固件自定义项引用VAR1，数据格式"Relay:Mode" 

- Relay:继电器数量，默认为1，按实际数量填写，超过3无效
- Mode:继电器切换模式，支持点动值1和翻转值0，默认0 

&nbsp;&nbsp;&nbsp;&nbsp;{++ 举例:  3开点动模式  3:1 ++}

![Alt text](/images/varchange.gif)

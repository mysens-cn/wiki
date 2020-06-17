title: 硬件工具
date: 2019-04-18T2:54:30.859Z
tags: [mysensor-中文站]
categories: [mysensor]
---


&nbsp;&nbsp;&nbsp;&nbsp;节点设备的固件编译和代码修改是一项烦锁的工作，本文介绍了解其过程,有针对性的修改部分源码固件等。
>针对NRF24M模块以及后续其它模块均会事先写入bootloader和对应的测试程序，到手只需要简单的刷写bin文件即可,这里更合适折腾帝！

!!! warning "警告"
    务必要清醒的是，MCU熔丝设置是不可逆的，轻则需要靠其它设备引线救砖，重则直接报废。通常来说，所有传感器节点均无需修改bootloader操作，仅上传固件，针对其它支持OTA节点的，bootloader刷写完成后，建议使用控制器进行固件变换操作！
    
## 硬件设备
&nbsp;&nbsp;&nbsp;&nbsp;硬件上，你需要准备USB-TTL,USB-ASP,SOP16刷机夹具,杜邦线

###USB-ASP

![Alt text](/images/usbasp.jpg)
>USBASP设备芯片多样，建议购买atmega系列usbasp,注意USBASP默认可能是5V供电，务必条线到3.3v，否则烧坏无线模块，导致功耗奇高，无法休眠 

!!! note "提醒"
    淘宝系USB设备多为特殊产品。默认win10驱动导致arduino无法识别，部分需要安装libusbK驱动。链接https://pan.baidu.com/s/1dENivhJ?qq-pf-to=pcqq.c2c

###SOP16夹具
&nbsp;&nbsp;&nbsp;&nbsp; 此刷机夹由于并非设计给夹板，夹具张口偏大，针对PCB可能出现夹不紧的状况， 这里提供一个改装方法。

>拆出栓

![Alt text](/images/SOP1601.jpg)

>将红红部分使用刀具削掉一部分，削完后如下图

![Alt text](/images/SOP1602.jpg)

>将弹簧使用工具拉一下，然后装回去，如图，完成组装，此时便可以夹1.0mm PCB板了

![Alt text](/images/SOP16.jpg)

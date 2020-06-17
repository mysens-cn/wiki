title: 固件写入
date: 2019-04-18T2:54:30.859Z
tags: [mysensor-中文站]
categories: [mysensor]
---

!!! warning "警告"
    务必要清醒的是，MCU熔丝设置是不可逆的，轻则需要靠其它设备引线救砖，重则直接报废。从2019年8月后，所有正常信息节点均可被OTA升级操作，OTA部分请参考OTA章节单元，针对自己制作PCB的，请按下图使用工具刷机，



&nbsp;&nbsp;&nbsp;&nbsp;硬件上，你需要准备USB-TTL,USB-ASP,SOP16刷机夹具,杜邦线

##USB-ASP

![Alt text](/images/usbasp.jpg)
>USBASP设备芯片多样，建议购买atmega系列usbasp,注意USBASP默认可能是5V供电，务必条线到3.3v，否则烧坏无线模块，导致功耗奇高，无法休眠 

!!! note "提醒"
    淘宝系USB设备多为特殊产品。默认win10驱动导致arduino无法识别，部分需要安装libusbK驱动。链接https://pan.baidu.com/s/1dENivhJ?qq-pf-to=pcqq.c2c

##SOP16夹具
&nbsp;&nbsp;&nbsp;&nbsp; 此刷机夹由于并非设计给夹板，夹具张口偏大，针对PCB可能出现夹不紧的状况， 这里提供一个改装方法。

>拆出栓

![Alt text](/images/SOP1601.jpg)

>将红红部分使用刀具削掉一部分，削完后如下图

![Alt text](/images/SOP1602.jpg)

>将弹簧使用工具拉一下，然后装回去，如图，完成组装，此时便可以夹1.0mm PCB板了

![Alt text](/images/SOP16.jpg)

## avrdudess

&nbsp;&nbsp;&nbsp;&nbsp;avrdudess刷机软件打开后，请参考下图设置完成，确保第一项都严格核对后再操作。

![Alt text](/images/avrdudess-flash.jpg)

  - Fuses_Lock bits如果读出来与图中不一致，务必更正与图中一致
  - 使用前，夹具夹好后，软件Detect两次，确保读出328P信息后刷入
  - 刷机过程中，如果遇到校验ERROR，则说明你的线接触不良或者没夹好
  - 不可强制中断操作，任何时间也不可以。
  - 如果使用标准文件网关无法发现节点，请刷入({++with-boot++})后缀名文件
  
>请参考下方GIF图片

![Alt text](/images/avrdudess-flash.gif)
title: ota
date: 2019-04-18T2:54:30.859Z
tags: [mysensor-中文站]
categories: [mysensor]
---


&nbsp;&nbsp;&nbsp;&nbsp;Mysensor OTA 依赖网关控制器，并且节点必须支持对应的BOOT升级操作，328P目前均可以支持并且对应升级。在升级之前我们必须先了解一下数据传输的结构和OTA原理以及相关可能带来的问题。

![PCB TOP](/images/mysensor-ls.jpg)

- 从上图了解到，数据最终汇集到控制器，控制器形式是可以多样的，按照官方API即可完成匹配，目前HA并不支持完整控制器功能，主推使用控制器为MYCONTROLLER.ORG 开放源码控制器,支持所有mysensor的全部功能

##BOOT


&nbsp;&nbsp;&nbsp;&nbsp;Bootloader是个绕不开的话题，要想可以OTA，硬件上必须支持MYSX的核心bootloader及API操作，虽然mysx支持了类似STM32F103,SAND21 ATMEGA328P，大体上兼容了支持arduino的兼容atmega硬件，但实质上，Bootlaoder ota这一部分目前仅仅atmega328P系列是完美的.

- {==这就决定了，如果你想要取得OTA，必须针对你的MCU重新刷写bootloader支持，针对328PU-TH，你需要用工具写FUSE，写入with_boot名称文件，并且设置正确的保险丝==} 


##MCTLL
　　　&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;网关控制器是针对网关消息处理的核心，节点设备的OTA数据分发,控制器理论上并非是必须设备，但是由于固件非硬性ID固化，目前所有节点初使化依赖控制器分配管理ID并形成网络，分配完成后节点将信息写入EEPROM不再重复请求。
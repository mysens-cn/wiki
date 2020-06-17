title: ESP32GW
date: 2019-04-18T2:54:30.859Z
tags: [mysensor-中文站]
categories: [mysensor]
---



&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;本项目采用乐鑫ESP32-S/ESP32模块，采用 AS01-ML01DP5 NRF24L01+PA无线收发模块，网关是核心数据节点，请确保元件的正品性，采用非nordic产品的无线模块，会选成高丢包率，组网中，无法分配ID！国产SI24R1不建议作网关使用，上下兼容性较差。

##PCB视图

----------
&nbsp;&nbsp;&nbsp;&nbsp;PCB正面视图
![PCB TOP](/images/ESP32GWTOP.jpg)

&nbsp;&nbsp;&nbsp;&nbsp;PCB背面视图
![PCB TOP](/images/ESP32GWBTOM.jpg)

> PCB 电源芯片为UZ1085L-3.3V 最大3A输出，刷机和桥接SERIAL-TTL采用拨码式

----------


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;PCB ESP32启动电流超过500mah，大部分电脑USB设备无法提供足够的电池完成配网操作，设备会重复重启，LED不断闪烁，请连接5V 1A以上电源

----------

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;对于ESP32-S模块，可以使用外接天线，但可能对nrf24造成干扰，通常来说建议放置无线信号较好的地区使用！


----------

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;PCB背面URX,UTX，为telnet程序IO对接，由于IO直对tx rx,测试中发现出现部分无法刷机的现况，故改为短接点，刷机前请确保两点未短接。


##刷机-OTA

----------
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;刷机请从固件区fimware.mysensor.pub下载对应ESP32GW版本的固件，固件区分MQTT和TCP。升级完成后网关WEB有显示

### TTL刷机

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;请使用ESPHOMEFLASHER刷机，https://github.com/esphome/esphome-flasher/releases

>由于固件不包含分区和BOOT APP等信息，其它刷机软件刷完之后无法启动

![PCB TOP](/images/esp32-ttl-in.jpg)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;按图中拨码，将1-2码拨OFF，3码拨ON 即为短接0进入刷机模式

![PCB TOP](/images/esp32-ttl-flash.gif)
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;接入TTL，选择对应文件，点击FLASH-ESP，软件检测到ESP32板后自动进入清空和刷机，出现Done! Flashing is complete！完成刷机。将拨码还原到【 上-上-下 】

### OTA升级

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;请使用Chrome、firefox浏览器 打开http://网关IP 进入后出现界面,使用admin/admin登入，选择上传完成即可！

![web-ota](/images/esp32-web-ota.gif)

##配网操作

###TCP节点

&nbsp;&nbsp;TCP节点由于库的原因，无法采用WIFIMANANGER配网，目前使用官方SDK中的SMARTCONFIG
需要手机下载ESPTOUCH完成配网操作，IOS在苹果APPSTORE搜ESPTOUCH，安卓请在群里下载文件..,

![ios-esptouch](/images/esptouch-ios.jpg)

&nbsp;&nbsp;&nbsp;&nbsp;当前手机接入WIFI将会被ESP32作为SSID连接，输入WIFI密码后，选择Multicast模式，完成配网后，弹出界面，网关3个灯将常亮，表示正常完成网关的配网并开始接收2.4G数据


!!! warning "注意"

    1.WIFI接入点请尽量使用单频2.4G，不要使用混合5G类型
    
    2.部分由于网络环境AP软路由固件因素，导致无法返回配网IP，请视察网关LED灯是否全部亮起

----------

###MQTT节点


&nbsp;&nbsp;&nbsp;&nbsp;升级操作或者TTL刷机操作后，网关启动后自动生成ESP32GW 热点，请使用手机等设备连接，自动弹出配网界面，输入WIFI信息和MQTT信息，保存后生效，针对不能自动弹出的，请打开192.168.4.1完成以上操作，完成后，网关WEB有对应的蓝色界面


!!! warning "注意"

    当前MQTT版本为mysensor-2.3.2-beta内核，可能存在无法与HA正常通信，但不会影响MQTT协议消息。
    

----------

## 日志调试

&nbsp;&nbsp;&nbsp;&nbsp;由于一些特殊环境，可能你会需要调试网关，分为TTL网关调试、NC日志、Telnet日志


###TTL调试

  - USB口旁边4P-2.54依次分别为VCC-GND-RXD-TXD，VCC需要5V 1A以上电流
  - 将拨码1 2调回OFF 即三个拨码为下下下，接入电脑TTL，打开 串口工具，上电后将输出信息

###Telnet
&nbsp;&nbsp;&nbsp;&nbsp;标准Telnet服务，支持输入输出，使用PUTTY工具或者其它工具均可，这里建议使用webzsh
#### Putty连接
&nbsp;&nbsp;&nbsp;&nbsp;使用PUTTY工具请参考下方设置，在终端选选项勾上增加LR和增加CR，以取得换行效果，否则显示将乱行
![telnet-putty](/images/telnet-putty.gif)

#### WEBZSH
&nbsp;&nbsp;&nbsp;&nbsp;WEBZSH安装请参考下节./gateway/webzsh/，它支持后台数据运行记录， 有利于Debug节点相关信息:

![telnet-webzsh](/images/telnet-webzsh.gif)


##3D外壳

![telnet-webzsh](/images/3dstl.gif)

&nbsp;&nbsp;&nbsp;&nbsp;3D打印文件：  


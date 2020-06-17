title: OTA-MSG
date: 2018-09-01T12:54:30.859Z
tags: [mysensor-中文站]
categories: [mysensor]
---


　　　&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;支持OTA升级的设备，目前分为电池传感器-NRF24M固件和长在线节点

- 电池传感器由于采用外部中断或者定时狗，设备进入休眠后，直接关闭接收数

- 在线式节点支持中继，并且可以完美随时变量操作

>下文中均将电池传感器视为离线节点，另外由于BOOTLOADER原因，部分固件需要重新USBASP刷机后方可离线OTA

![Alt text](/images/otamsg0.jpg)

##上传固件
　　　&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;进入控制器-实用程序-固件 依次添加固件类型，版本 并上传固件

![Alt text](/images/otaupload.gif)

## 编辑节点
　　　&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;节点启动后，会对应在资源，节点中生成节点通告的内容。编辑节点，选择对应的节点固件版本

![Alt text](/images/nodechar.gif)

## OTA上传
　　　&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;完成上面步骤后，由于目标分在线节点和离线节点，针对在线节点直接我们可以直接操作上传固件，然后重启节点，节点就自动完成上传发起更新任务。

### 在线节点
　　　&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;发起上传操作后，网关发送固件值版本，节点接收并对比，重启后开始上传，如下图操作：

```python

 GWT:RFC:C=1,MSG=27;255;4;0;1;11000D0000052D2A
 TSF:MSG:SEND,0-0-27-27,s=255,c=4,t=1,pt=6,l=8,sg=0,ft=0,st=0:11000D0000052D2A

```


![Alt text](/images/upnode-ota.gif)

### 离线节点

　　　&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;跟在线节点一样发起上传操作后，网关发送固件值版本，但节点离线无法实时接收，反馈如下

```python

 GWT:RFC:C=1,MSG=27;255;4;0;1;11000D0000052D2A
 !TSF:MSG:SEND,0-0-27-27,s=255,c=4,t=1,pt=6,l=8,sg=0,ft=0,st=NACK:11000D0000052D2A

```

　　　&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;我们可以拨下电池，等待10-30秒的时间【部分节点有220UF电容，需要等待或者短路一下正负极】，重新上电后，自动进入OTA流程


![Alt text](/images/offnode-ota.gif)


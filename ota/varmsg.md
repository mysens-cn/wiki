title: VAR-MSG
date: 2018-09-01T12:54:30.859Z
tags: [mysensor-中文站]
categories: [mysensor]
---


　　　&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;属性变量在官方中应用的地方并不多，作为开源软件，官方更多考虑的是直译，但是由于适用性，大部分固件均采用了属性定义来取得一些个性化的变量

##工作原理

![Alt text](/images/varmsgt.jpg)

　　　&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在线节点无线一直是工作状态，可以实时接收到任何来着外部的数据指令，针对离线节点，则需要由MCU内部在初使化的时候与控制器请求匹配数据完成变量操作，并完成一致性对比提交

- 针对继电器节点，分发后，数据由节点MCU完成保存变量，但由于部分变量是在循环主程序之外的定义，比如继电器数量，这个是初使化中引用，那么则需要重启生效，针对模式的切换其变量是在循环主程序，故无需重启直接可生效


- 针对传感器节点，重启后由MCU请求数据，完成变量的保存工作，在进入主程序循环之前则完成变量的匹配工作。比如人体传感器的定义

----------

##属性定义

　　　&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;由于节点版本的差异，文中无法涉及到全部的定义节点历史版本，所有定义的属性均由节点自身presentation通告内容如下：

```python

 TSF:MSG:READ,27-27-0,s=255,c=3,t=11,pt=0,l=14,sg=0:Relay Actuator
 TSF:MSG:READ,27-27-0,s=255,c=3,t=12,pt=0,l=3,sg=0:1
 TSF:MSG:READ,27-27-0,s=81,c=0,t=3,pt=0,l=6,sg=0:Relay1
 TSF:MSG:READ,27-27-0,s=23,c=0,t=24,pt=0,l=10,sg=0:Relay:Mode
 TSF:MSG:READ,27-27-0,s=255,c=3,t=26,pt=1,l=1,sg=0:2
 TSF:MSG:SEND,0-0-27-27,s=255,c=3,t=27,pt=1,l=1,sg=0,ft=0,st=OK:1
 GWT:RFC:C=1,MSG=27;255;3;0;6;M
 TSF:MSG:SEND,0-0-27-27,s=255,c=3,t=6,pt=0,l=1,sg=0,ft=0,st=OK:M
 TSF:MSG:READ,27-27-0,s=23,c=1,t=24,pt=0,l=3,sg=0:1:0
 TSF:MSG:READ,27-27-0,s=81,c=1,t=2,pt=2,l=2,sg=0:1
 TSF:MSG:READ,27-27-0,s=255,c=3,t=1,pt=0,l=0,sg=0:
 GWT:RFC:C=0,MSG=27;255;3;0;1;1565282381
 TSF:MSG:SEND,0-0-27-27,s=255,c=3,t=1,pt=0,l=10,sg=0,ft=0,st=OK:1565282381
 GWT:RFC:C=1,MSG=27;255;3;0;1;1565311181
 TSF:MSG:SEND,0-0-27-27,s=255,c=3,t=1,pt=0,l=10,sg=0,ft=0,st=OK:1565311181
 
```

> TSF:MSG:READ,27-27-0,s=23,c=0,t=24,pt=0,l=10,sg=0:Relay:Mode  
该条信息明确了自定义项目的内容即Relay:Mode

----

>  TSF:MSG:READ,27-27-0,s=23,c=1,t=24,pt=0,l=3,sg=0:1:0   
该条信息明确了自定义项目的默认值即1:0

　　　&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;明白该节点通告内容后，我们对应生成我们自己想要的数据

- {== Presentation 通告内容仅在节点初使化过程中出现，启动之后无外部指令无法重新通告，针对在线设备， 我们可以使用控制器刷新节点让其重新通告，针对离线设备则只能重启 ==}

##变更属性

　　　&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;属性的变量需要用控制器完成操作，进入资源-传感器，查找对应的节点EUI ID值，找到对应的变量值：

![Alt text](/images/varchange.gif)


>{>>操作时鼠标指向数量点击，请依据节点对应固件的自定义项填好数据，并回车键保存<<}



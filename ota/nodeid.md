title: node-id
date: 2018-09-01T12:54:30.859Z
tags: [mysensor-中文站]
categories: [mysensor]
---


　　　&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;该功能目前已经包含在节点属性定义中，输出为EMPYT属性定义。现由于bootloader已经实现离线设备的更新及控制器分发数据，后期将取消定义EMPYT更换ID的功能，转移到本节中的使用控制器分发。


##在线设备

  　　　&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;进入设备节点列表，勾选对应的EUI ID，选择删除设备，设备将自动重启并请求网关控制器分配新的ID。
  
 > {>>请注意，分配新的ID，会导致原来设置的属性定义失效，需要重新设置<<}


![Alt text](/images/upnodeid.gif)

##离线设备

  　　　&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;进入设备节点列表，勾选对应的EUI ID，选择删除设备，进入Telnet界面后，我们可以看到如下：
  
  
```python 

309843421 GWT:RFC:C=1,MSG=30;255;3;0;18;
309843460 !TSF:MSG:SEND,0-0-30-30,s=255,c=3,t=18,pt=0,l=0,sg=0,ft=0,st=NACK:

```

  　　　&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;对离线节点重启后，与控制器连接，即收到控制器要请求清空的消息，节点按流程处理更新ID
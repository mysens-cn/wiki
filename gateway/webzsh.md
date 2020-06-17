title: WEBZSH
date: 2018-09-01T12:54:30.859Z
tags: [mysensor-中文站]
categories: [mysensor]
---


　　　&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;本文推荐一款调试集成web工具，拥有ssh telnet netcat dig mtr mqtt-client。项目整合打包后在docker.hub已发布，源自ttyd项目，

----------

　　　&nbsp;&nbsp;基于apline，集成一部分工具，特别是调试ESP32-GW telnet不会出来类似putty的乱屏问题。并且支持多屏分享，没错，仅发送一个web链接，便可以操作或者观看，多端操作和数据是同步的。

　　　&nbsp;&nbsp;N1 arm架构的请使用以下命令启动：

```
docker pull huex/webzsh:armbian

docker run --network=host -d  \
--restart=always \
--name webzsh \
huex/webzsh:armbian
```

　　　&nbsp;&nbsp;x64架构的请使用以下命令启动：

```
docker pull huex/webzsh

docker run --network=host -d  \
--restart=always \
--name webzsh \
huex/webzsh
```
>web端口为7681,可nginx转发至域名子目录使用。

![Alt text](/images/webzsh.jpg)

>这里推荐所有Mysensor用户使用，可以方便的查看esp32-gw，并且不受关机的影响，只要不断开不重启，信息会一直显示下去

title: Node-red
date: 2019-05-08T2:54:30.859Z
tags: [mysensor-中文站]
categories: [mysensor]
---


&nbsp;&nbsp;&nbsp;&nbsp;Node-red是理想的自动化处理节点，由于网关采用TCP或者MQTT协议数据，故无需添加任何专用插件，特别是使用TCP节点网关，仅需要对数据简单处理即可完成自动化。

##简单示例

```yaml
[{"id":"2c277daf.ef48d2","type":"function","z":"73da2156.fb1da","name":"tcp-set","func":"var splitTopic= msg.topic.split(\"\\/\");\n\n// splitTopic[0] is the MySensors/ preamble. #1 is NODE_ID and on up. \nmsg.payload = splitTopic[1] + \";\" + splitTopic[2] + \";\" +  splitTopic[3] + \";\" +  splitTopic[4] + \";\" +  splitTopic[5] + \";\" +  msg.payload ;\nreturn msg;","outputs":1,"noerr":0,"x":764,"y":538,"wires":[["dad3c4c0.ead3a8","400b9254.fa90bc"]]},{"id":"dad3c4c0.ead3a8","type":"function","z":"73da2156.fb1da","name":"","func":"msg._session = context.global.tcpsess;\nreturn msg;","outputs":1,"noerr":0,"x":931,"y":536,"wires":[["e368a715.2472e8","af803fdd.1e5ab"]],"outputLabels":["msg.nodeId"]},{"id":"400b9254.fa90bc","type":"debug","z":"73da2156.fb1da","name":"","active":true,"tosidebar":true,"console":true,"tostatus":true,"complete":"payload","x":819,"y":442,"wires":[]},{"id":"e368a715.2472e8","type":"debug","z":"73da2156.fb1da","name":"","active":true,"tosidebar":true,"console":true,"tostatus":true,"complete":"payload","x":1156,"y":597,"wires":[]},{"id":"af803fdd.1e5ab","type":"tcp out","z":"73da2156.fb1da","host":"","port":"","beserver":"reply","base64":false,"end":false,"name":"","x":1178,"y":528,"wires":[]}]
  
```


&nbsp;&nbsp;&nbsp;&nbsp;由于目前DIY仅限制于非可控设备，可控设备后续添加后补充。

##ALL IN ONE

&nbsp;&nbsp;&nbsp;&nbsp;Node-red是理想的all in one自动化处理中心，目前理想是使用派设备，运行Node-red，使用串行数据中心或者rj45,整合到一个模块中，即可完成家庭智能网关集合，无视外网和WIFI，所有操作系统内部完成，并且不受HA或者homekit升级BUG影响。
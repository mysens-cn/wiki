title: Homeassistant
date: 2019-05-07T2:54:30.859Z
tags: [mysensor-中文站]
categories: [mysensor]
---


&nbsp;&nbsp;&nbsp;&nbsp;Homeassistant插件由Mysensor官方维护，插件已经收录到homeassistant插件库.只需要添配置网关文件即可，项目目前支持以下类型：

## 节点支持

![Alt text](/images/mysensoriot.jpg)

!!! note "说明"

    1.设备由节点presentation信息提供，HA检测到presentation信息后，自动添加生成
    
    2.Ha节点采用TCP，会间隔对网关请求，以判断节点的可操作性
    
    3.由于无线设备的非双向性，目前ha针对设备无状态可查询，主观依靠网关提供。

##配置文件

```yaml
mysensors:
  gateways:
#    - device: '/dev/ttyUSB0' #串行节点(TCP/MQTT/串行 三选一)
#      persistence_file: 'path/mysensors.json'
#      baud_rate: 38400
#      nodes:
#        201:
#          name: 'lux'
#        200:
#          name: 'node'
#    - device: '/dev/ttyACM0' #串行节点(TCP/MQTT/串行 三选一)
#      persistence_file: 'path/mysensors2.json'
#      baud_rate: 115200
    - device: '10.0.0.244' #TCP节点(TCP/MQTT/串行 三选一)
      persistence_file: '/config/mysensors.json'
      tcp_port: 5003
      nodes:     #节点名称(非必须）
        21:
          name: '儿童房'
        18:
          name: '书房'
#    - device: mqtt  #MQTT节点(TCP/MQTT/串行 三选一)
#      persistence_file: '/config/mysensors.json'
#      topic_in_prefix: 'mygateway-out'
#      topic_out_prefix: 'mygateway-in'
  optimistic: false
  persistence: true
  retain: true
  version: '2.0'
  
```

>配置后,HA会在配置根目录生成mysensor.json，此文件是homeassistant保存的节点信息文件

##节点编辑
　　　子设备的添加是需要由节点提供presentation后才会添加的，所以当配置完后，设备并不会自动由网关添加，你需要重置子节点后，ha才会去收集presentation信息并生成对应的json格式

```json

{
    "201": {
        "sensor_id": 201,
        "children": {
            "2": {
                "id": 2,
                "type": 16,
                "description": "Lux",
                "values": {
                    "37": "6056"
                }
            },
            "254": {
                "id": 254,
                "type": 30,
                "description": "Vcc",
                "values": {
                    "38": "3.44"
                }
            }
        },
        "type": 17,
        "sketch_name": "South Lux Sensor",
        "sketch_version": "1.0",
        "battery_level": 99,
        "protocol_version": "2.3.1",
        "heartbeat": 0
    },
    "0": {
        "sensor_id": 0,
        "children": {},
        "type": 18,
        "sketch_name": "Mysensors Gateway",
        "sketch_version": "1.0",
        "battery_level": 0,
        "protocol_version": "2.3.1",
        "heartbeat": 104039792
    }
}

```

!!! note "注意"

    1.如果HA已经存在节点，节点硬件重启后仍然会提交presentation，默认HA会update节点信息，但HA可能会在log里报错,并不会影响设备。
    
    2.请保留0节点，此为网关设备的配置信息
    
!!! warning "警告"
    修改此文件时，先停用homeassistant,修改完成后，请务必检查json格式，如果格式错误保存后，ha无法读取会造成清空所有的配置文件重新生成，，切记修改删除部分节点后，一要备份并且复制格式到json在线格式化工具中检查一次确保没有问题后再启动ha
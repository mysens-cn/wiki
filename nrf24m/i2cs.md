title: NRF24-SENSORS
date: 2019-05-09T13:54:30.859Z
tags: [mysensor-中文站]
categories: [mysensor]
---

- 平均待机功耗7-8ua左右，发射瞬间功耗14ma左右，年待机80mah左右。

- CR2450(550mah)/CR2477(950mah)电池。电池使用时长与上报频率和上报差值有关。
> CR2450电池550mah，以一年计算，合400mah可用于发射检测，400mha/14ma=28.5H/365=0.078277(H)*3600s=281s/300ms(单次发送预计最大时长,参考国外)=936次，即1天发射936次。假如每分钟检测一次，上报一个值，那么天天需要消耗1440次。即CR2450电池仅可使用0.6年。CR2477电池(1.7倍CR2450)预计时长增加1.5倍以上。

![Alt text](/images/nrf24-sensors.jpg)

!!! Note "建议"

       &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;考虑到传感器的实际情况，一般建议温度差值0.1，湿度0.3(即芯片的误差范围)。单一光线传感器，由于工作只有白天，故CR2450足够，采用BME280,气压值需要精准度高的情况下，电池续航会相对更低.
       

##设计示图
&nbsp;&nbsp;&nbsp;&nbsp;模块采用挖空方式下沉贴NRF24M模块。由于NRF24M模块下沉后在1.68左右。考虑到部分情况，电池采用固定式接触片和非固定式，目前采用双PCB加厚到2.0mm。
###PCB示图

![Alt text](/images/i2cs.jpg)

>设计直径32MM。双侧减0.8mm. 6个0603贴片，黑色为电阻，棕色为电容。

###实物图
  - 正面图片
![Alt text](/images/i2cssw.jpg)

  - 正面图片
  ![Alt text](/images/i2csswt.jpg)
##BOM制造
&nbsp;&nbsp;&nbsp;&nbsp;模块PCB基于NRF24M模块，PCB必须做1.0 叠双层使用。

### Gerber

&nbsp;&nbsp;&nbsp;&nbsp;仅供极客使用，不得商业使用 <a href="https://github.com/huexpub/NRF24M-SENSORS" target="_blank">Gerber</a>

-----
### BOM表

| 序列 | 标记 | 元件名 | 封装 | 数量 | 备注 |
| - | :-: | -: | -:| -: | -: |   
| 1 | NRF24M | nrf24m | nrf24m | 1 |  
| 2 | R1、R2、R3 | 10KΩ (10K) ±5% | 0603 | 3 | 
| 3 | C2、C3、C1| 100nf-6.3v | 0603 |  3 | x7r最佳
| 4 | Si7020-A20 | Si7020-A20 | 6-DFN（3x3）|  1 |
| 5 | OPT3001 | OPT3001 | USON-6 |  1 |
| 6 | BME280 | BME280 | LGA-8 |  1 | 

&nbsp;&nbsp;&nbsp;&nbsp;BME280/si7021(htu21)/opt3001;按需使用。

##代码固件

>0.1温度差异、0.3湿度差异化上报，检测频率 每分钟

&nbsp;&nbsp;&nbsp;&nbsp;<a href="http://fimware.mysensor.pub/?dir=NRF24M/NRF-SENSOR" target="_blank">NRF24-SENSOR.HEX</a>


##属性定义

&nbsp;&nbsp;&nbsp;&nbsp;通过对网关的消息日志，


```python

236343905 TSF:MSG:PINGED,ID=18,HP=2
236343915 TSF:MSG:SEND,0-0-7-18,s=255,c=3,t=25,pt=1,l=1,sg=0,ft=0,st=OK:1
236345906 TSF:MSG:READ,70-7-0,s=255,c=3,t=15,pt=6,l=2,sg=0:0100
236345914 TSF:MSG:SEND,0-0-7-70,s=255,c=3,t=15,pt=6,l=2,sg=0,ft=0,st=OK:0100
236345971 TSF:MSG:READ,70-7-0,s=255,c=0,t=17,pt=0,l=5,sg=0:2.3.1
236345991 TSF:MSG:READ,70-7-0,s=255,c=3,t=6,pt=1,l=1,sg=0:18
236346212 GWT:RFC:C=1,MSG=70;255;3;0;6;M
236346217 TSF:MSG:SEND,0-0-7-70,s=255,c=3,t=6,pt=0,l=1,sg=0,ft=0,st=OK:M
236346274 TSF:MSG:READ,70-7-0,s=255,c=3,t=11,pt=0,l=13,sg=0:NRF24 Sensors
236346295 TSF:MSG:READ,70-7-0,s=255,c=3,t=12,pt=0,l=3,sg=0:1.4
236346419 TSF:MSG:READ,70-7-0,s=2,c=0,t=16,pt=0,l=3,sg=0:Lux
236346470 TSF:MSG:READ,70-7-0,s=254,c=0,t=30,pt=0,l=3,sg=0:Vcc
236346521 TSF:MSG:READ,70-7-0,s=23,c=0,t=24,pt=0,l=5,sg=0:Empyt
236346541 TSF:MSG:READ,70-7-0,s=255,c=3,t=26,pt=1,l=1,sg=0:2
236346553 TSF:MSG:SEND,0-0-7-70,s=255,c=3,t=27,pt=1,l=1,sg=0,ft=0,st=OK:1
236347964 TSF:MSG:READ,70-7-0,s=2,c=1,t=37,pt=4,l=4,sg=0:497
236347984 TSF:MSG:READ,70-7-0,s=255,c=3,t=0,pt=1,l=1,sg=0:100
236348005 TSF:MSG:READ,70-7-0,s=254,c=1,t=38,pt=7,l=5,sg=0:3.44

```

我们可以得知，固件支持Empyt定义，该定义引用VAR1值，方便重新分发节点ID，具体使用请参<a href="ota/varmsg/">OTA</a>  章节

##3D-STL

![Alt text](/images/nrf24-sensor-stl.jpg)


> 打印无需支撑，精度不够的请对应调节

&nbsp;&nbsp;&nbsp;&nbsp;<a href="http://fimware.mysensor.pub/?dir=3D-STL/NRF24-SENSOR" target="_blank">STL下载</a>

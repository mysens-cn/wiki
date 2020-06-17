title: NRF24-MOTION
date: 2019-04-18T2:54:30.859Z
tags: [mysensor-中文站]
categories: [mysensor]
---


&nbsp;&nbsp;&nbsp;&nbsp;pir模块采用NRF24M核心，电池为CR2477(标称950mah),森霸am312大窗口,由于diy市场难以找到符合低电压,外围电路简单的PIR芯片，故使用TI升压芯片，am312最低要求电压为2.7, 本次PCB ti升压芯片(0.7-5.5)稳定输出2.8V，功耗仅4ua,最优转换效率高达95%。模块全年功耗预计250mah左右,剩余电量可计算出触发次数。

##模块图示

&nbsp;&nbsp;&nbsp;&nbsp;模块顶部挖空立式安装nrf24m核心板,正面PIR 透镜正中安装，光线传感器在透镜内部，作参考是完全足够的。电池座为塑座。


###实物图

![PCB TOP](/images/nrf24-pir.jpg)


###PCB电路图

>PCB视图

![PCB TOP](/images/pirpcb.jpg)

>0603共计16个贴片元件、一个sot23-6元件。


!!! note "说明"

    1.透镜为大镜，角度120，标称距离8-10米，另外经测试曲面镜效果也不错。
    
    2.PIR上检测上报时差硬件为3s，时长调节需要控制器设置后，硬件重启生效。
    
    2.温度模块为SI7021，默认固件不会定时发送，仅PIR触发后上报数据。即代码结构WDT为关闭状态。
    
##BOM制造

  &nbsp;&nbsp;&nbsp;&nbsp;无件各类较多，容阻请购买大品牌。电感必须为绕线内阻200mΩ上下.
  
| 序列 | 标记 | 元件名 | 封装 | 数量 | 备注 |
| - | :- | :-: | :-| -: | -: |   
| 1 | NRF24M | nrf24m | nrf24m | 1 |  
| 2 | R3、R1 | 1KΩ (1K) ±5% | 0603 | 2 | 
| 3 | C5、C6、C2| 10uf-16v | 0603 |  3 | x5r以上
| 5 | C7、C1、C4、C3| 100nf-16v | 0603 |  4 |
| 6 | D1 | 0603-LED | 0603 |  1 |
| 7 | L1 | 10uh-10% |  SMD3*3*1.2 |  1 |
| 8 | R2| 20KΩ (20K) ±5% | 0603 | 1 | 
| 9 | R5| 200KΩ (200K) ±5% | 0603 | 1 | 
| 10 | R6、R4 | 1MΩ (1M) ±5% | 0603 | 2 | 
| 11 | R7 | 470KΩ (470K) ±5% | 0603 | 1 | 
| 12 | Si7020-A20 | Si7020-A20 | 6-DFN（3x3）|  1 |
| 13 | BH1750 | BH1750 | WSOF-6I |  1 |
| 14 | tlv | TLV61220 | sot23-6 |  1 |


##固件下载

&nbsp;&nbsp;&nbsp;&nbsp;PIR芯片3S触发机制,针对3S触发、频率较高，耗电相对较多。故代码部分添加了软时间，触发后，MCU会sleep指定时间，无论中途是重新触发还是由WDT唤醒，均不会提交两次ON，无触发后，会进入OFF,现默认30S


!!! Note "建议"

       &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;超时设置请依据自身的需求，针对高频率触发区域，建议设置30秒-2分钟，以减少电耗和无效数据量，针对低频率你可以设置更低，但必须知道的是，当你设置10S超时，意味着对比小米等人体 不仅数据量大，而且消耗较高，综合将会是10倍以上的消耗，电池续航时间将大大缩短到1/10 以下的使用时间.


&nbsp;&nbsp;&nbsp;&nbsp;<a href="http://fimware.mysensor.pub/?dir=NRF24M/NRF-MOTION" target="_blank">NRF24-MOTION.HEX</a>

##属性定义

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;PIR模块目前最新版本引用了{++Empyt:Time:Blink:Lux++}4个自定义值，数据格式A:B:C:D ，请依照自身的定义值修改：

- Empyt：清除当前节点的配置ID及其它信息，用作更换节点ID，值为1或者0，默认为0
- Time：定义PIR检测超时时间，设置30即触发后，30秒以后才会重复检测状态，默认为30
- Blink：PIR-led选项，当值为1时，触发人体，PIR会闪绿色灯，值默认为0禁用此功能
- Lux：生成自定义值VAR2，输出布尔值1或者0，默认为0禁用此功能。参考下节说明


{==&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;定义60S，启用LED 那么则数据应该为0:60:1:0 ,依次类推，关于设置请参<a href="ota/varmsg/">OTA</a>  ==}

##3D-STL

![PCB TOP](/images/PIR-3DSTL.png)

>  此处感谢小F、三文大神付出，文件均需要支撑打印

&nbsp;&nbsp;&nbsp;&nbsp;<a href="http://fimware.mysensor.pub/?dir=3D-STL/NRF24-MOTION" target="_blank">STL下载</a>
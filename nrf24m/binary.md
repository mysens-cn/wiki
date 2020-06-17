title: NRF24-DOOR
date: 2019-04-18T2:54:30.859Z
tags: [mysensor-中文站]
categories: [mysensor]
---

- 平均待机功耗4-5ua左右，发射瞬间功耗14ma左右，年待机40mah左右。

- CR2032(210mah)电池，理论待机3年以上。理论正常频率(每天开启6-10次左右）可使用超过1年以上。



![Alt text](/images/doorstl.jpg)

!!! warning "BUG"

       &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;长加安装方式，由于PCB生产厚度行业难达标称1.6(实际1.51更低),导致拆电池会导致SI24R1部分元件摩擦，替换2.0PCB成本过高，考虑到门磁耗电实际情况，故暂时无更改计划，建议做完板后，使用胶带贴一下SI24R1芯片元件，参考下图。
       

##模块示图
&nbsp;&nbsp;&nbsp;&nbsp;模块DIY难度非常低，到手焊接即可，电池座为铁扣形式
###PCB示图

![Alt text](/images/nrf24door.jpg)

>19.6*21MM净板尺寸

###实物图
  - 正面
![Alt text](/images/nrf24doorti.jpg)

  - 背面
![Alt text](/images/nrf24doortb.jpg)


##模块BOM

&nbsp;&nbsp;&nbsp;&nbsp;仅需干簧管({++最大14mm++})、NRF24M模块。

!!! Note "关于干簧管"

       &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;干簧管长度选择上，越短触发磁力越小，越灵敏，越长灵敏越差，但并非越灵敏越好，一般来说，门磁建议使用14mm干簧管(间隙较小才触发)，针对特殊环境，你可以使用更小的尺寸，比如7MM，灵敏和触发距离范围均大一倍以上。

##代码固件
&nbsp;&nbsp;&nbsp;&nbsp;本PCB项目提供开源代码和固件，使用者可以快速使用arduino编译上传或者使用progisp上传hex固件。

###源代码

```python

/**
   The MySensors Arduino library handles the wireless radio link and protocol
   between your home built sensors/actuators and HA controller of choice.
   The sensors forms a self healing radio network with optional repeaters. Each
   repeater and gateway builds a routing tables in EEPROM which keeps track of the
   network topology allowing messages to be routed to nodes.

   Created by Henrik Ekblad <henrik.ekblad@mysensors.org>
   Copyright (C) 2013-2015 Sensnology AB
   Full contributor list: https://github.com/mysensors/Arduino/graphs/contributors

   Documentation: http://www.mysensors.org
   Support Forum: http://forum.mysensors.org

   This program is free software; you can redistribute it and/or
   modify it under the terms of the GNU General Public License
   version 2 as published by the Free Software Foundation.

 *******************************

   DESCRIPTION

   Simple binary switch example
   Connect button or door/window reed switch between
   digitial I/O pin 2 (BUTTON_PIN below) and GND.
   http://www.mysensors.org/build/binary
   code reedit by huex //
*/


// Enable debug prints to serial monitor
#define MY_DEBUG

// Enable and select radio type attached
#define MY_RADIO_RF24
//#define MY_DEBUG_VERBOSE_RF24
#define MY_RF24_PA_LEVEL RF24_PA_LOW
#define MY_RF24_DATARATE RF24_250KBPS //RF24_1MBPS  RF24_2MBPS
//#define MY_RF24_CHANNEL 105
//#define MY_BAUD_RATE 115200
#define MY_BAUD_RATE 9600
//#define MY_SIGNING_ATSHA204
//#define MY_SIGNING_ATSHA204_PIN 17
//#define MY_SIGNING_REQUEST_SIGNATURES


/*Node ID & NAME*/
#define MY_TRANSPORT_UPLINK_CHECK_DISABLED
#define MY_PARENT_NODE_IS_STATIC
#define MY_PARENT_NODE_ID 0
char SKETCH_NAME[] = "Door Sensor";
char SKETCH_VERSION[] = "1.0";


/*Time Period */
//#define SLEEP_TIME 21600*1000   // 12h
#define SLEEP_TIME 0   // no wdt
#define BATT_REPORT_CYCLE 60   // 1 hour
float lastVcc = 3.30 ; //Batteryv
int lastdoor = -1;
int batteryPcnt ;

/*Sensor & Battery Pin */
#define REED_PIN 2 // sensor pin
#define LED_PIN 8
#define BATTERY_SENSE_PIN  A1  // battery
#define VMIN 1.8
#define VMAX 3.3

/*Child ID*/

#define CHILD_ID_DOOR 1
#define CHILD_ID_VBAT 254 //BATTERY

//#include <Wire.h>
#include <MySensors.h>

/*Message Instance */
MyMessage msgdoor(CHILD_ID_DOOR, V_TRIPPED);
MyMessage msgVbat(CHILD_ID_VBAT, V_VOLTAGE); 

void blinkity(uint8_t pulses, uint8_t repetitions) {
  for (int x = 0; x < repetitions; x++) {
    for (int i = 0; i < pulses; i++) {
      digitalWrite(LED_PIN, HIGH);
      delay(20);
      digitalWrite(LED_PIN, LOW);
      delay(100);
    }
    delay(100);
  }
}

void setup()
{
  pinMode(LED_PIN, OUTPUT);
  pinMode(REED_PIN, INPUT);
  analogReference(INTERNAL);
  delay(100);
  if (transportCheckUplink() == false) {
    blinkity(4, 2);
  }
  if (isTransportReady() == true)  {
    blinkity(2, 1);
  }
}

void presentation() {
  // Send the sketch info to the gateway
  sendSketchInfo(SKETCH_NAME, SKETCH_VERSION);
  present(CHILD_ID_DOOR, S_DOOR, "DOOR");  //door sensor
  present(CHILD_ID_VBAT, S_MULTIMETER, "VCC"); //Battery
}

void loop() {
  int reed = digitalRead(REED_PIN);
  if (reed != lastdoor) {
    lastdoor = reed;
    if  (isTransportReady() != true){
      wait(50);
      while (isTransportReady() == true) {}
    }
    send(msgdoor.set(reed == HIGH ? 1 : 0));
  }
  batterylevel();
  sleep(600);
  while (digitalRead(REED_PIN) == lastdoor ) {
    sleep(digitalPinToInterrupt(REED_PIN), CHANGE, SLEEP_TIME);
  }
}

void batterylevel() {
  int sensorValue = analogRead(BATTERY_SENSE_PIN);
  float batteryV = sensorValue * 0.003363075;
  float v = (float) (batteryV - lastVcc);
  if ((batteryV > VMIN ) && ( batteryV < VMAX)) {
    batteryPcnt = int(((batteryV - VMIN) / (VMAX - VMIN)) * 100.);
  }
  else {
    if (batteryV < VMIN ) {
      batteryPcnt = 0 ;
    }
    else {
      batteryPcnt = 100;
    }
  }
  if ((lastVcc != batteryV) && (abs(v) >= 0.1)) {
    sendBatteryLevel(batteryPcnt);
    send(msgVbat.set(batteryV, 2));
    lastVcc = batteryV;
  }
}

```

###HEX固件
<a href="http://fimware.mysensor.pub/?dir=NRF24M/NRF-DOOR" target="_blank">{==NRF24DOOR.HEX==}</a>

##3D外壳

  - 长48 宽23.实物打印效果：


  - stl文件下载：
  
  <a href="http://fimware.mysensor.pub/?dir=3D-STL/NRF24-DOOR" target="_blank">{==NRF24DOOR-STL.RAR==}</a>
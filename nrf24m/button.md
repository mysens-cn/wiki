title: 遥控按键
date: 2019-08-01T13:54:30.859Z
tags: [mysensor-中文站]
categories: [mysensor]
---

- 平均待机功耗7-8ua左右，发射瞬间功耗14ma左右，年待机80mah左右。

- CR2032(210mah)电池，平均每天检测1-2次，理论寿命1年左右



###源代码

```python


//#define MY_DEBUG

/*Raido*/
#define MY_RADIO_RF24 // Enable and select radio type attached 
#define MY_RF24_PA_LEVEL RF24_PA_LOW //MINI LOW HIGH MAX
//#define MY_RF24_DATARATE RF24_250KBPS

//#define MY_REPEATER_FEATURE

#define MY_BAUD_RATE 9600 //115200 19200 9600 =8MHZ  4800 =1MHZ

/*Node ID & NAME*/
//#define MY_NODE_ID 9  //Fallback NodeId
//#define MY_TRANSPORT_UPLINK_CHECK_DISABLED
//#define MY_PARENT_NODE_IS_STATIC //only for clone si24r1
//#define MY_PARENT_NODE_ID 0 //only for clone si24r1
char SKETCH_NAME[] = "Button Sensor";
char SKETCH_VERSION[] = "1.0";

#include <Wire.h>
#include <MySensors.h>

/*Authentication*/

/*Child ID*/
#define CHILD_ID_BINARY 1

#define CHILD_ID_VBAT 254 //BATTERY

/*BATTERY*/
#define  BATTERY_SENSE_PIN  A1 // battery sensor
#define LED_PIN 8
#define  BUTTON_PIN 2 //button pin
#define VMIN 2.2
#define VMAX 3.44

/*Time Period */
#define SLEEP_TIME 60000   // 1 min
static int16_t lastVcc = 3.44; //Batteryv
int lastvalue = -1;
int batteryPcnt ;
int mode;
int node;
int child;
int empyt;

/* IMport Modlues */

/*Message Instance */

MyMessage msgsw(CHILD_ID_BINARY, V_SCENE_ON); //对标RELAYS继电器属性，不可更改
MyMessage msgvcc(CHILD_ID_VBAT, V_VOLTAGE);
MyMessage msgvar1(S_CUSTOM, V_VAR1); // Empty ID

void(* resetFunc) (void) = 0;

void before() {
  pinMode(BUTTON_PIN, INPUT);
  analogReference(INTERNAL);
  mode = loadState(2);
  node = loadState(6);
  child = loadState(9);
}
void setup() {
  if (transportCheckUplink() == false) {
    blinkity(4, 2);
  }
  if (isTransportReady() == true)  {
    blinkity(2, 1);
  }
  /*每次重启后请求值*/
  request(S_CUSTOM, V_VAR1);
  wait(1000);

  /* 检测值的准确性 */
  if ( mode > 1 ) {
    empyt = 0;
    mode = 30;
    node = 0;
    child = 0;
    String str = String(empyt) + ":" + String(mode) + ":" + String(node) + ":" + String(child);
    const char* value = str.c_str();
    send(msgvar1.set(value));
  }
  if (empyt == 1) {
    for (uint16_t i = 0; i < EEPROM_LOCAL_CONFIG_ADDRESS; i++) {
      hwWriteConfig(i, 0xFF);
    }
    empyt = 0;
    String str = String(empyt) + ":" + String(mode) + ":" + String(node) + ":" + String(child);
    const char* value = str.c_str();
    send(msgvar1.set(value));
    resetFunc();
  }
}

void presentation()  {
  // Send the sketch version information to the gateway and Controller
  sendSketchInfo(SKETCH_NAME, SKETCH_VERSION);
  wait(50);
  present(CHILD_ID_BINARY, S_SCENE_CONTROLLER, "Button");
  wait(50);
  present(CHILD_ID_VBAT, S_MULTIMETER, "Vcc"); //Battery
  wait(50);
  present(S_CUSTOM, V_VAR1, "Empyt:Mode:Node:Child");
}

void loop() {
  int value = digitalRead(BUTTON_PIN);
  //SWitch模式操作
  if (mode == 0) {
    if (value != lastvalue) {
      if (node != 0) {
        send(msgsw.set("1").setSensor(node).setDestination(child));
      }
      else {
        send(msgsw.set(value == HIGH ? 1 : 0));
      }
    }
    lastvalue = value;
    batterylevel();
  }

  //button模式操作
  if (mode == 1 ) {
    if (value != lastvalue && value == 0) {
      if (node != 0) {
        send(msgsw.set("1").setSensor(node).setDestination(child));
      }
      else {
        send(msgsw.set("1"));
      }
    }
    lastvalue = value;
  }
  sleep(600);//过滤无效或者连续操作
  while ((digitalRead(BUTTON_PIN) == HIGH) == lastvalue ) {
    sleep(digitalPinToInterrupt(BUTTON_PIN), CHANGE, 0);
  }
}


void receive(const MyMessage & message) {
  if (message.type == V_VAR1  && message.sensor == 23) {
    char* buf = new char[strlen((message.data)) + 1];
    strcpy(buf, (message.data));
    sscanf(buf, "%d:%d:%d:%d:%d", &empyt, &mode , &node, &child);
    if ( empyt > 1 || mode > 1 ) {
      int empyt = 0;
      int mode = 0 ;
      int node = 0;
      int child = 0;

    }
    else {
      saveState(2, mode);
      saveState(6, node);
      saveState(9, child);
    }
    String str = String(empyt) + ":" + String(mode) + ":" + String(node) + ":" + String(child);
    const char* value = str.c_str();
    send(msgvar1.set(value));
  }
}

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
  if ((lastVcc != batteryV) && (abs(v) >= 0.2)) {
    sendBatteryLevel(batteryPcnt);
    send(msgvcc.set(batteryV, 2));
    lastVcc = batteryV;
  }
}

```

###HEX固件
&nbsp;&nbsp;&nbsp;&nbsp;暂不提供，项目为测试

##3D外壳

&nbsp;&nbsp;&nbsp;&nbsp;暂未完成，等待大神留名中.....
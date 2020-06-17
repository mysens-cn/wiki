title: 花花草草
date: 2019-05-09T13:54:30.859Z
tags: [mysensor-中文站]
categories: [mysensor]
---

- 平均待机功耗7-8ua左右，发射瞬间功耗14ma左右，年待机80mah左右。

- CR2032(210mah)电池，平均每天检测1-2次，理论寿命1年左右

&nbsp;&nbsp;&nbsp;&nbsp;PCB门磁板预留A2-A3脚，交替使用检测量


!!! warning "BUG"

       &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;长加安装方式，由于PCB生产厚度行业难达标称1.6(实际1.51更低),导致拆电池会导致SI24R1部分元件摩擦，替换2.0PCB成本过高，考虑到门磁耗电实际情况，故暂时无更改计划，建议做完板后，使用胶带贴一下SI24R1芯片元件，参考下图。
       

##模块示图
&nbsp;&nbsp;&nbsp;&nbsp;模块DIY难度非常低，到手焊接即可，电池座为铁扣形式，建议使用304材质，1mm，太粗会损坏PCB，消耗电量也会更多。
###PCB示图

![Alt text](/images/nrf24door.jpg)

>19.6*21MM净板尺寸

###实物图
  - 加长安装方式
![Alt text](/images/nrf24doorti.jpg)

  - 加高安装方式

>此加高安装方式建议购买1.27错位贴母针即可。

&nbsp;&nbsp;&nbsp;&nbsp;等待补图中.....

##模块BOM

&nbsp;&nbsp;&nbsp;&nbsp;仅需1MM-304圆棒、NRF24M模块。

##代码固件
&nbsp;&nbsp;&nbsp;&nbsp;本PCB项目提供开源代码和固件，使用者可以快速使用arduino编译上传或者使用progisp上传hex固件。

###源代码

```python

#define MY_DEBUG

// Mode Radio / Enable and select radio type attached
#define MY_RADIO_RF24
#define MY_RF24_PA_LEVEL RF24_PA_LOW //MINI LOW HIGH MAX
//#define MY_RF24_DATARATE RF24_250KBPS
#define MY_BAUD_RATE 9600 //115200 19200 9600 =8MHZ  4800 =1MHZ
//#define MY_TRANSPORT_WAIT_READY_MS (30000)

/*Node ID & NAME*/
//#define MY_NODE_ID 61 //Fallback NodeId
#define MY_TRANSPORT_UPLINK_CHECK_DISABLED
#define MY_PARENT_NODE_IS_STATIC
#define MY_PARENT_NODE_ID 0
char SKETCH_NAME[] = "Flower Sensor";
char SKETCH_VERSION[] = "1.0";

/*OTA Featuer*/
#define MY_OTA_FIRMWARE_FEATURE

/*Child ID*/
#define CHILD_ID_HUM 4 //Humidity Sensor
#define CHILD_ID_MOISTURE 5 //Humidity Sensor
#define CHILD_ID_VBAT 254 //BATTERY

/*Time Period */
unsigned long SLEEP_TIME = 12 * 3600000; //检测时间间隔
unsigned long SHORT_TIME = 2000;
static int32_t battreportCount = -3;
static int32_t voltage;
static int32_t batteryPcnt;
static int32_t lastVcc;
const int SENSOR_ANALOG_PINS[] = {A2, A3};
#define  BATTERY_SENSE_PIN A6// battery sensor
#define THRESHOLD             1.1     // Only make a new reading with reverse polarity if the change is larger than 10% (default 1.1)
#define MOISTURE_THRESHOLD    1.01    // Delta needing for sending moisture to the controler (default 1.01)
#define BATTERY_FULL          3300    // when full AAA (default 3300)
#define BATTERY_ZERO          2340
#define LED_PIN  8


int oldbatteryPcnt = -1;
byte direction = 0;
int oldMoistureLevel = -1;

//Define functions
#define round(x) ((x)>=0?(long)((x)+0.5):(long)((x)-0.5))
#define N_ELEMENTS(array) (sizeof(array)/sizeof((array)[0]))

#include <MySensors.h>
#include <SPI.h>

MyMessage msgm(CHILD_ID_MOISTURE, V_HUM);
MyMessage msgv(CHILD_ID_VBAT, V_VOLTAGE);

void presentation()  {
  wait(50);
  sendSketchInfo(SKETCH_NAME, SKETCH_VERSION);
  wait(50);
  present(CHILD_ID_MOISTURE, S_HUM, "Moistrue"); //BH1750
  wait(50);
  present(CHILD_ID_VBAT, S_MULTIMETER, "Vcc"); //Battery
}

void setup() {
  pinMode(LED_PIN, OUTPUT);
  analogReference(INTERNAL);
  for (int i = 0; i < N_ELEMENTS(SENSOR_ANALOG_PINS); i++) {
    pinMode(SENSOR_ANALOG_PINS[i], OUTPUT);
    digitalWrite(SENSOR_ANALOG_PINS[i], LOW);
  }
  //  wait(3000);  //wait for ota
}

void loop() {
  long voltage = softvcc();
  //Get moisture level
  int moistureLevel = readMoisture();
  if (moistureLevel > (oldMoistureLevel * THRESHOLD) || moistureLevel < (oldMoistureLevel / THRESHOLD))
  {
    moistureLevel = readMoisture();
  }

  if (moistureLevel > (oldMoistureLevel * MOISTURE_THRESHOLD) || moistureLevel < (oldMoistureLevel / MOISTURE_THRESHOLD)) //Send moisture only if moisture changed more than MOISTURE_THRESHOLD
  {
    if (oldMoistureLevel == -1) {
      send(msgm.set((moistureLevel) / 10.23, 0));
    } else {
      send(msgm.set((moistureLevel + oldMoistureLevel) / 2.0 / 10.23, 0));
    }
    oldMoistureLevel = moistureLevel;
  }
  blinkity(1,1);
  sleep(SLEEP_TIME);
}

int readMoisture()
{
  //Power on the sensor and read once to let the ADC capacitor start charging
  pinMode(SENSOR_ANALOG_PINS[direction], INPUT_PULLUP);
  analogRead(SENSOR_ANALOG_PINS[direction]);

  //Stabilize and read the value
  sleep(SHORT_TIME);
  int moistureLevel = (1023 - analogRead(SENSOR_ANALOG_PINS[direction]));

  //Turn off the sensor to conserve battery and minimize corrosion
  pinMode(SENSOR_ANALOG_PINS[direction], OUTPUT);
  digitalWrite(SENSOR_ANALOG_PINS[direction], LOW);

  //Make direction alternate between 0 and 1 to reverse polarity which reduces corrosion
  direction = (direction + 1) % 2;
  return moistureLevel;
}

long softvcc()
{
  ADMUX = (0 << REFS1) | (1 << REFS0) | (0 << ADLAR) | (1 << MUX3) | (1 << MUX2) | (1 << MUX1) | (0 << MUX0);
  delay(50);  // Let mux settle a little to get a more stable A/D conversion
  //Start a conversion
  ADCSRA |= _BV( ADSC );
  //Wait for it to complete
  while (bit_is_set(ADCSRA, ADSC));

  //Compute and return the value
  uint8_t low  = ADCL;                  // must read ADCL first - it then locks ADCH
  uint8_t high = ADCH;                  // unlocks both
  long result = (high << 8) | low;
  result = 1125300L / result;           // Calculate Vcc (in mV); 1125300 = 1.1*1023*1000
  return result;                        // Vcc in millivolts
}

void batterylevel() {
  // Get the battery Voltage
  int sensorValue = analogRead(BATTERY_SENSE_PIN);
  float batteryV = sensorValue * 0.3363075;
  long v = (long)(batteryV - lastVcc);
  if (batteryV > BATTERY_FULL) {
    batteryPcnt = 100;
  }
  else if (batteryV < BATTERY_ZERO) {
    batteryPcnt = 0;
  }
  else {
    batteryPcnt = int(((batteryV - BATTERY_ZERO) / (BATTERY_FULL - BATTERY_ZERO)) * 100.);
  }
  int batteryMv  = round(batteryV * 1000);
  if (lastVcc != batteryMv ) {
    sendBatteryLevel(batteryPcnt);
    send(msgv.set(batteryMv, 2));
    lastVcc = batteryMv;
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


```

###HEX固件
&nbsp;&nbsp;&nbsp;&nbsp;暂不提供，项目为测试

##3D外壳

&nbsp;&nbsp;&nbsp;&nbsp;暂未完成，等待大神留名中.....
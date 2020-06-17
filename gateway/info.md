
　　　&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Mysensor网关充当控制器和无线电网络之间的传话器。它将无线电消息转换为可由控制器理解的协议。您可以使用与传感器相同的组件自己构建网关。我们提供了这里描述的各种选择。

在构建网关之前，您应该检查控制器支持哪个选项。

*  Sensebender网关这是无麻烦(几乎)即插即用选项，以使网关运行。
>该项目由samd芯片完成，焊接件较多，目前未进行制作

*  SerialGateway-网关使用一个可用的USB端口直接连接到您的控制器。由于SerialGateway依赖于可用的USB端口，因此需要将网关定位到控制器附近。
>该项目依赖设备比如各类派有SPI接口即可

*  EthernetGateway-该网关连接到以太网网络，您的控制器也使用该网络提供了比SerialGateway更灵活的放置位置，允许您将网关安装在无线电网络的中心位置。
>以太网设备有更高的可靠性，支持TCP和UDP

*  MQTTGateway-该网关还连接到以太网网络，并公开一个MQTT代理，该代理可以用于提供诸如OpenHAB这样的MQTT支持的控制器。
>MQTT协议，简易通俗

@(重点讲解以wifi接入的网关设备，目前较为容易获得)

## 接入类型

*  以目前的官方资料，主要支持RJ45,RS485,WIFI,USB,GPIO

#### RJ45以太网接入
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;主要设备W5100,W5500以太网SPI接口，需要配合MCU，官方推荐配置使用samd21 频率48MHZ，也可以使用atmega328P 16MHZ,但处理速度均远远低于esp8266 70MHZ, ESP32 80-120MHZ,最大的优势即无视WIFI干扰，稳定性五星

#### RS485接入
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;目前使用并不多，主要为联动设备，多数是从mysensor协议转换到485，少数转到mysensor，软件和支持开源自动化方面比较弱。

####WIFI接入
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;主要设备ESP8266,ESP32，两款均可以被良好支持，频率高，反应快，目前也是推荐使用之一，高达120MHZ，足够使用多点TCP接入，同时接入NODE-RED，Mysensor控制器，Homeassistant仍然可以很快速的处理数据!稳定性一般，受wifi影响，可能会导致网关与自动化系统传输问题

####USB
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;主要来自官方Sensebender，受限于设备，不作过多介绍，类似于GPIO类型，需要安装驱动，未有测试，受制于网关设备端。

####GPIO
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;主要设备树梅派，橙子派等，直接使用linux驱动，理论上性能好，内部控制器直接使用TTL完成，也可使用TCP中转，是理想的all in one解决方案，即NODE-RED MYSENSOR控制器 网关一体，无视外界，内部自成一体。

## 协议类型

*   支持MQTT,TCP/UDP,串行，接入HA和node均无任何压力，其它平台domoticz openhba等均可以直接使用。

####MQTT
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;协议适合度广，目前测试HA偶会无响应，需要重启HA，可能是HA插件造成!

####TCP/UDP
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;推荐使用协议，节点通信好，支持限制接入数，

####SERIAL
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;由于串口受主机影响，无法直接多点输出，合适于内部一体完成，集成网关和Node-red，是比较理想的内部一体设备，暂时不谈论，有兴趣的可以一试！

## 自动化平台
　　 Mysensor网关目前已经支持大部分主流平台，包括HA，Domoticz,HomeGenie,Pidome,Vera,OPENHAB，Node-Red等，部分支持控制器功能！设备添加仅需要添加主机配置，设备自动由节点提交presentation自动添加。

　　
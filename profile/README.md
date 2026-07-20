

# OhioIoT

Firmware for connecting ESP32 devices to the OhioIoT MQTT broker.  

*** THESE CODE BASES ARE STILL UNDER DEVELOPMENT *** 
If you have any questions, please send a note from the web page at [ohioiot.com/contact](https://ohioiot.com/contact).


```cpp

// define your variables

void setup() {
    controller.setup(WIFI_SSID, WIFI_PASS, MQTT_USER, MQTT_PASS);
    // add your own code
}

void loop()  {
    controller.loop();
    // add your own code
}
```


## What's here

The SDK ships as **three tiers**, each in a **PlatformIO** and an **Arduino IDE**
flavor. Clone one and start writing your own code — every repo is complete and
self-contained.

| | PlatformIO | Arduino IDE |
|---|---|---|
| **Minimalist** — DeviceID, WiFi, and MQTT | [`Minimalist-PlatformIO`](https://github.com/OhioIoT-Firmware/Minimalist-PlatformIO) | [`Minimalist-ArduinoIDE`](https://github.com/OhioIoT-Firmware/Minimalist-ArduinoIDE) |
| **Scaler** — adds Monitoring and Messages | [`Scaler-PlatformIO`](https://github.com/OhioIoT-Firmware/Scaler-PlatformIO) | [`Scaler-ArduinoIDE`](https://github.com/OhioIoT-Firmware/Scaler-ArduinoIDE) |
| **Champion** — adds Provisioning, OTA, and Settings | [`Champion-PlatformIO`](https://github.com/OhioIoT-Firmware/Minimalist-PlatformIO) | [`Champion-ArduinoIDE`](https://github.com/OhioIoT-Firmware/Champion-PlatformIO) |

Start with the **Minimalist** to establish basic MQTT connectivity.  Then move to the downstream libraries to add in more features.



## Links

[Documentation](https://ohioiot.com/docs)


*Find video tutoriols on making IoT systems at the YouTube channel (https://youtube.com/@ohioiot).*

*OhioIoT is an IoT platform designed for small-scale IoT projects (https://ohioiot.com).*

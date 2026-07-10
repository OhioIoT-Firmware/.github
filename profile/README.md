

# OhioIoT

Firmware for connecting ESP32 devices to the OhioIoT MQTT broker.  

*** THESE CODE BASES ARE STILL UNDER DEVELOPMENT *** 
If you have any questions, please send a note from the web page at [OhioIoT.com(https://ohioiot.com)]


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
| **Minimalist** — DeviceID, WiFi, and MQTT | [`Minimalist-PlatformIO`](../../../Minimalist-PlatformIO) | [`Minimalist-ArduinoIDE`](../../../[Tier1]-ArduinoIDE) |
| **Scaler** — adds Monitoring and Messages | [`Scaler-PlatformIO`](../../../Scaler-PlatformIO) | [`Scaler-ArduinoIDE`](../../../Scaler-ArduinoIDE) |
| **Champion** — adds Provisioning, OTA, and Settings | [`Champion-PlatformIO`](../../../Champion-PlatformIO) | [`Champion-ArduinoIDE`](../../../Champion-ArduinoIDE) |

Start with the **Minimalist** to establish basic MQTT connectivity.  Then move to the downstream libraries to add in more features.



## Links

[OhioIoT.com](https://ohioiot.com) · [Documentation](https://ohioiot.com/docs)

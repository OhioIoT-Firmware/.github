

# OhioIoT

Firmware for connecting ESP32 devices to the OhioIoT MQTT broker.  

*** THESE CODE BASES ARE STILL UNDER DEVELOPMENT *** 
If you have any questions, please send a note from the web page at OhioIoT.com


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
| **Minimalist** — WiFi, MQTT, metrics | [`[Tier1]-PlatformIO`](../../../Minimalist-PlatformIO) | [`Minimalist-ArduinoIDE`](../../../[Tier1]-ArduinoIDE) |
| **Scaler** — adds structured commands | [`Scaler-PlatformIO`](../../../Scaler-PlatformIO) | [`Scaler-ArduinoIDE`](../../../Scaler-ArduinoIDE) |
| **Champion** — adds provisioning, OTA, settings, sleep | [`Champion-PlatformIO`](../../../Champion-PlatformIO) | [`Champion-ArduinoIDE`](../../../Champion-ArduinoIDE) |

Start with **[Tier1]**. Moving up a tier is copy-paste — the controller API is
identical across all six.

## Also here

- [`Modules`](../../../Modules) — the building blocks the tiers are made of. Drop a
  folder into any ESP32 project; most have no dependencies.

## Links

[ohioiot.com](https://ohioiot.com) · [Docs](https://ohioiot.com/docs)

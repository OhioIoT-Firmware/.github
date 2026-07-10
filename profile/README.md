

# OhioIoT Firmware

Connect an ESP32 to the internet in two lines.

This repo holds the **OhioIoT SDK** — a controller that handles the tedious parts of a connected device (WiFi, MQTT over TLS, reconnects, OTA updates, remote settings) and a set of **modules** you can use with it or lift on their own.

```cpp
void setup() {
    controller.setup(WIFI_SSID, WIFI_PASS, MQTT_USER, MQTT_PASS);
}

void loop() {
    controller.loop();
}
```

That device is now online, subscribed, publishing metrics, and able to take an over-the-air update. Everything below is optional.

---

## Quickstart

**Requirements:** an ESP32 dev board, [PlatformIO](https://platformio.org/) (or the Arduino IDE), and a WiFi network.

1. Download the tier you want from [`build/`](build/) — start with [TIER_NAME] if you're not sure.
2. Open the folder in PlatformIO.
3. Create `credentials.h` in `src/`:

   ```cpp
   #pragma once

   #define WIFI_SSID  "your-network"
   #define WIFI_PASS  "your-password"
   #define MQTT_USER  "your-mqtt-user"
   #define MQTT_PASS  "your-mqtt-password"
   ```

4. Flash it. Open the serial monitor at `115200`.

By default the device connects to `ota.ohioiot.com` on port `8883` over TLS. To point it at your own broker, see [Configuration](#configuration).

> **Video walkthrough:** [coming soon]

---

## Tiers

The SDK ships in six builds. Each is a complete, self-contained project — no submodules, no external paths. Pick the smallest one that does what you need; you can always move up.

| Tier | What you get | Flash |
|---|---|---|
| [TIER_1] | [description] | [~XXX KB] |
| [TIER_2] | [description] | [~XXX KB] |
| [TIER_3] | [description] | [~XXX KB] |
| Scaler | Structured command namespaces on top of plain MQTT messaging | [~XXX KB] |
| [TIER_5] | [description] | [~XXX KB] |
| Champion | Everything: WiFi provisioning portal, OTA with rollback, remote settings, deep sleep | [~XXX KB] |

Moving up a tier is a copy-paste, not a rewrite — the controller API is identical across all six.

---

## What the controller does

You call `controller.setup()` once and `controller.loop()` every pass. In exchange it:

- brings up WiFi and reconnects when it drops
- connects to MQTT over TLS, with a CA certificate baked in
- publishes device metrics (uptime, RSSI, free memory, firmware version)
- routes incoming framework messages (restart, status requests, OTA commands)
- on the Champion tier: applies OTA updates, reports progress, and rolls back a bad image automatically

You never subclass it, register it, or initialize it. There is one global, `controller`, and it already exists.

### Adding your own behavior

Subscribe to topics and handle messages:

```cpp
static const char * subscription_list[] = {
    "~/~/whatever",
    nullptr                     // keep this
};

void messageHandler(char * topic, char * payload) {
    // your code
}

void setup() {
    controller.setup(WIFI_SSID, WIFI_PASS, MQTT_USER, MQTT_PASS);
    mqtt.set_subscriptions(subscription_list);
    mqtt.set_callback(messageHandler);
}
```

Your handler and the framework's coexist — the controller claims what belongs to it and passes the rest to you.

### Provisioning instead of hardcoded WiFi (Champion)

Drop the WiFi arguments and the device launches a captive portal on first boot:

```cpp
controller.setup(MQTT_USER, MQTT_PASS);
```

### Remote settings (Champion)

Declare a table and a callback. The values survive reboots and can be changed from the dashboard.

```cpp
char _threshold[SETTINGS_BUF_SIZE] = "2.5";

SettingRow settings_table[] = {
    { "threshold", _threshold },
    { nullptr,     nullptr    },
};

void settingsChanged() {
    threshold = settings.convert_to_float(_threshold, 2.5);
}

void setup() {
    controller.setup(MQTT_USER, MQTT_PASS);
    settings.begin(settings_table, settingsChanged);
}
```

---

## Modules

Everything in [`modules/`](modules/) is a plain PlatformIO/Arduino library folder. Copy one into your project's `lib/` and `#include` it. No build system, no package manager, no registry.

These are the same modules the SDK is built from — nothing is held back — but they were written for the SDK, so a few expect a sibling to be present. That's the whole table:

| Module | What it does | Needs |
|---|---|---|
| `counters` | Persistent counters in NVS | — |
| `device_id` | Stable per-device identifier | — |
| `events` | Event log with persistence | — |
| `flags` | Persistent boolean flags | — |
| `json` | Minimal JSON building and parsing | — |
| `mqtt` | MQTT client with TLS, subscriptions, callbacks | — |
| `ota` | HTTPS OTA with rollback and partition logging | — |
| `power` | Deep sleep, light sleep, CPU frequency scaling | — |
| `settings` | Registered settings table, persisted to NVS | — |
| `wifi_tools` | Connect, reconnect, scan, signal strength | — |
| `metrics` | Uptime, RSSI, memory, firmware, brownout detection | `flags` |
| `messages` | Command routing and namespaces | `mqtt` |
| `provisioner` | Captive-portal WiFi setup with a web UI | `wifi_tools` |
| `monitor` | Periodic metric publishing | `events`, `json`, `metrics`, `mqtt` |
| `_certificates` | The CA certificate the SDK trusts by default | — |

Two honest notes:

- **Some modules compile alone but do nothing alone.** `messages` and `monitor` are framework parts — they wait to be wired up by a controller. They'll build in any project; they won't act in one.
- **The rest are ordinary libraries.** `json`, `flags`, `counters`, `power`, `device_id`, `wifi_tools` don't know the SDK exists. Take them anywhere.

No module includes `controller.h`. The dependency runs one way.

---

## Configuration

Everything is a build flag. Uncomment in `platformio.ini`, or `#define` before including in the Arduino IDE.

### Connection and security — all tiers

| Flag | Default | Purpose |
|---|---|---|
| `MQTT_HOST` | `"ota.ohioiot.com"` | Broker hostname |
| `MQTT_PORT` | `8883` | Broker port |
| `ALLOW_INSECURE_MQTT` | off | Bypass TLS — local brokers only |
| `MQTT_USER` | from `credentials.h` | Broker username |
| `MQTT_PASS` | from `credentials.h` | Broker password |
| `CA_CERT` | OhioIoT (Let's Encrypt) | CA cert for MQTT and OTA |

### Provisioning — Champion

| Flag | Default | Purpose |
|---|---|---|
| `AP_SSID` | `"DEVICE PROVISIONING"` | Name of the setup access point |

### OTA — Champion

| Flag | Default | Purpose |
|---|---|---|
| `OTA_HOST` | `https://ota.ohioiot.com` | Update endpoint |
| `OTA_SOCKET_TIMEOUT` | `10000` | ms to wait on a single response |
| `OTA_MAX_DURATION` | `300000` | ms before the download aborts |
| `ROLLBACK_THRESHOLD` | `600000` | ms after which firmware can no longer roll back |
| `OTA_PROGRESS_INCREMENT` | `1` | Percent between progress reports |
| `REQUIRE_MANUAL_FIRMWARE_ACCEPT` | off | Roll back unless the update is confirmed |

**Example — a local Mosquitto broker with no TLS:**

```ini
build_flags =
    -D MQTT_HOST=\"192.168.1.160\"
    -D MQTT_PORT=1883
    -D ALLOW_INSECURE_MQTT
```

---

## Repo layout

```
firmware/
  build/          the six shipped tiers — self-contained, ready to flash
  modules/        the modules on their own, for use in any project
```

Both are generated from a private development repo. Don't send pull requests against `build/` — see below.

---

## Requirements

- ESP32 (tested on `esp32dev`)
- Arduino framework 3.x via [pioarduino](https://github.com/pioarduino/platform-espressif32)
- [`knolleary/PubSubClient@^2.8`](https://github.com/knolleary/pubsubclient)

---

## Contributing

Issues and discussion are welcome here. Because `build/` and `modules/` are both generated, code changes need to happen upstream — open an issue describing what you'd change and we'll take it from there.

## License

[LICENSE] — see [LICENSE](LICENSE).

## Links

- [ohioiot.com](https://ohioiot.com)
- [Documentation](https://ohioiot.com/docs)
- [`ohioiot` on npm](https://www.npmjs.com/package/ohioiot)
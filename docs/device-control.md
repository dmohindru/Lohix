# Lohix CBOR Device Control Specification

This document outlines the CBOR-encoded commands and responses for communicating with Lohix-enabled devices over various transport layers (UART, USB, MQTT, radio, etc.).

Each command is a CBOR map with the following base structure:

```cbor
{
  "cmd": "<command_name>",
  "args": { ... },    // optional
  "id": <number>      // optional request identifier for response matching
}
```

---

## 🔍 Command Overview

| Command          | Description                          | Args                                              | Response                        |
| ---------------- | ------------------------------------ | ------------------------------------------------- | ------------------------------- |
| `whoami`         | Get board identity and firmware info | _none_                                            | board/chip/firmware string map  |
| `baud_get`       | Get current baud rate                | _none_                                            | numeric baud rate               |
| `baud_supported` | Get list of supported baud rates     | _none_                                            | array of numbers                |
| `baud_set`       | Set UART baud rate                   | `baud` (u32)                                      | acknowledgment                  |
| `resources`      | Get available hardware resources     | _none_                                            | map of features (leds, gyro...) |
| `led_status`     | Get LED state                        | `index` (u8)                                      | `index`, `state` ("on"/"off")   |
| `led_set`        | Set LED state                        | `index` (u8), `state`                             | acknowledgment                  |
| `led_mode`       | Set LED to display sensor status     | `mode`: `"compass"` / `"gyro"`                    | acknowledgment                  |
| `log_start`      | Start logging sensor data            | `sensor`, `duration_ms` or `interval_ms`, `count` | array of samples                |
| `btn_config`     | Configure button action bindings     | see below                                         | acknowledgment                  |

---

## 📘 Command Details

### 🆔 `whoami`

- **Purpose:** Identify the board and firmware version.
- **Response:**

```cbor
{
  "resp": "whoami",
  "data": {
    "board": "STM32F3DISCOVERY",
    "chip": "STM32F303VCT6",
    "firmware": "v0.1.0"
  }
}
```

---

### 🔧 `baud_get`

- **Purpose:** Retrieve current baud rate.

```cbor
{ "resp": "baud_get", "data": 115200 }
```

---

### 📶 `baud_supported`

- **Purpose:** Retrieve supported baud rates.

```cbor
{ "resp": "baud_supported", "data": [9600, 19200, 57600, 115200] }
```

---

### 📡 `baud_set`

```cbor
{
  "cmd": "baud_set",
  "args": { "baud": 57600 }
}
```

---

### 📎 `resources`

- **Response:**

```cbor
{
  "resp": "resources",
  "data": {
    "leds": 4,
    "compass": true,
    "gyro": true,
    "temp_sensor": true,
    "buttons": 2
  }
}
```

---

### 💡 `led_status`

```cbor
{
  "cmd": "led_status",
  "args": { "index": 0 }
}
```

- **Response:**

```cbor
{
  "resp": "led_status",
  "data": { "index": 0, "state": "on" }
}
```

---

### 🔘 `led_set`

```cbor
{
  "cmd": "led_set",
  "args": { "index": 0, "state": "off" }
}
```

---

### 🧭 `led_mode`

```cbor
{
  "cmd": "led_mode",
  "args": { "mode": "compass" }
}
```

```cbor
{
  "cmd": "led_mode",
  "args": { "mode": "gyro" }
}
```

---

### 📊 `log_start`

#### Logging Gyroscope Data:

```cbor
{
  "cmd": "log_start",
  "args": { "sensor": "gyro", "duration_ms": 5000 }
}
```

#### Logging Temperature Data (10 samples, 1s interval):

```cbor
{
  "cmd": "log_start",
  "args": { "sensor": "temp", "interval_ms": 1000, "count": 10 }
}
```

- **Response:**

```cbor
{
  "resp": "log_data",
  "data": {
    "sensor": "gyro",
    "samples": [[x, y, z], [x, y, z], ...]
  }
}
```

---

### ⌨️ `btn_config`

- **Purpose:** Configure actions on button press types.

```cbor
{
  "cmd": "btn_config",
  "args": {
    "button": 0,
    "bindings": [
      { "type": "short", "action": "led_toggle", "args": { "index": 0 } },
      { "type": "long", "duration_ms": 2000, "action": "log_start", "args": { "sensor": "temp", "duration_ms": 5000 } }
    ]
  }
}
```

---

## ✅ Notes

- All responses include the `"resp"` field that mirrors the `"cmd"` field.
- Every command may include an optional `"id"` field to correlate requests and responses.
- `args` is omitted when not needed.

---

## 🧪 Future Extensions

- Sensor calibration
- Device sleep mode
- Persistent configuration
- Event-driven streaming support

---

# Lohix Web API Specification

## 🌐 Introduction

**Lohix Web API** exposes the capabilities of the Lohix embedded system to network-connected clients (e.g., browsers, mobile apps, or other services). It acts as a bridge between the `lohix-cli` and modern web interfaces, allowing remote device control, monitoring, and configuration.

The API provides RESTful endpoints for interacting with hardware components like LEDs, sensors, buttons, and system-level configurations (e.g., baud rate, transport type). Future support for WebSocket streams is envisioned for real-time telemetry.

Base URL for all endpoints:

```
/api/v1
```

---

## 📋 Board Information

| Method | Endpoint                      | Description                     |
| ------ | ----------------------------- | ------------------------------- |
| GET    | `/board/info`                 | Get device board info (whoami)  |
| GET    | `/board/baud-rate`            | Get current baud rate           |
| GET    | `/board/supported-baud-rates` | List supported baud rates       |
| POST   | `/board/baud-rate`            | Set a new baud rate             |
| GET    | `/board/resources`            | Advertise available peripherals |

---

## 💡 LED Controls

| Method | Endpoint             | Description              |
| ------ | -------------------- | ------------------------ |
| GET    | `/leds/status`       | Get status of all LEDs   |
| POST   | `/leds/{id}/on`      | Turn a specific LED on   |
| POST   | `/leds/{id}/off`     | Turn a specific LED off  |
| POST   | `/leds/mode/compass` | Set LEDs to compass mode |
| POST   | `/leds/mode/gyro`    | Set LEDs to gyro mode    |

---

## 📊 Sensor Logging

| Method | Endpoint       | Description                       |
| ------ | -------------- | --------------------------------- |
| POST   | `/log/compass` | Start logging compass data        |
| POST   | `/log/gyro`    | Start logging gyro data           |
| POST   | `/log/temp`    | Start logging temperature data    |
| GET    | `/log/data`    | Get recent log results            |
| POST   | `/log/stop`    | Stop all active logging processes |

---

## 🎛️ Push-Button Configuration

| Method | Endpoint          | Description                                     |
| ------ | ----------------- | ----------------------------------------------- |
| GET    | `/buttons/config` | Get current button configuration                |
| POST   | `/buttons/config` | Set custom actions for different button presses |

---

## ⚙️ System & Transport

| Method | Endpoint            | Description                                   |
| ------ | ------------------- | --------------------------------------------- |
| GET    | `/system/transport` | Get current transport configuration           |
| POST   | `/system/transport` | Set transport mode and details (e.g., MQTT)   |
| GET    | `/system/config`    | Get default CLI configuration from `.rc` file |
| POST   | `/system/config`    | Update or reset CLI configuration             |

---

## 🔌 WebSocket Endpoints (Future)

| Path             | Description                  |
| ---------------- | ---------------------------- |
| `/ws/log-stream` | Live sensor data stream      |
| `/ws/status`     | Real-time device status feed |

---

## 📁 Example Payloads

### Set Baud Rate

```json
POST /api/v1/board/baud-rate
{
  "baud_rate": 115200
}
```

### Configure Transport

```json
POST /api/v1/system/transport
{
  "mode": "mqtt",
  "broker": "mqtt://192.168.1.100:1883",
  "topic": "lohix/dev"
}
```

### Configure Button

```json
POST /api/v1/buttons/config
{
  "button": "user",
  "single_press": "toggle_led",
  "double_press": "start_logging_gyro",
  "long_press": "reset_board"
}
```

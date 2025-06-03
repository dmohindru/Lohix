# 🧰 Lohix CLI — Command-Line Interface for Device Control

`lohix-cli` is a versatile command-line interface for communicating with embedded devices in the **Lohix ecosystem**. Built using **Rust**, it enables developers, testers, and learners to interact with STM32- and RP2040-based boards via structured, CBOR-encoded control commands.

The CLI acts as a universal **control terminal** for issuing commands, retrieving data, configuring peripherals, and debugging communication across various transport layers like **serial (UART)** and **network (MQTT)**. Future extensibility includes support for USB HID, LoRa/radio mesh, and WebUSB.

---

## ✨ Features

- 🧪 Structured communication via CBOR
- 🚀 Cross-platform support via Rust
- 🛠️ Interact with hardware peripherals: LEDs, sensors, buttons, etc.
- 🌉 Supports multiple transport layers (Serial, MQTT, etc.)
- 🧩 Easily extensible via modular command dispatch
- 🗂 `.rc` config file support for defaults and profiles

---

## 📦 Installation

```bash
cargo install lohix-cli
```

Or clone and build locally:

```bash
git clone https://github.com/your-org/lohix.git
cd lohix/cli
cargo build --release
```

---

## 🔧 Global CLI Usage

```bash
lohix-cli [GLOBAL OPTIONS] <COMMAND> [ARGS...]
```

### 🌐 Global Options

| Option            | Description                                           |
| ----------------- | ----------------------------------------------------- |
| `--serial <path>` | Use serial transport (e.g., `/dev/ttyUSB0`)           |
| `--baud <rate>`   | Set baud rate (default: `115200`)                     |
| `--network <url>` | Use network transport (e.g., `mqtt://localhost:1883`) |
| `--node-id <id>`  | (For radio/future use) Target node/device ID          |
| `--config <file>` | Path to config file (default: `~/.lohixrc`)           |
| `--timeout <ms>`  | Command response timeout in milliseconds              |
| `--help`, `-h`    | Show help                                             |
| `--version`, `-V` | Show version                                          |

> 💡 Provide either `--serial` or `--network` depending on transport.

---

## 🗃️ `.lohixrc` Example

```toml
transport = "serial"
serial_path = "/dev/ttyUSB0"
baud = 115200
timeout = 1000
```

---

## 🧭 Command Reference

### 🔹 Identity and Baud Rate

```bash
lohix-cli whoami                      # Show board identity/version
lohix-cli baud-info                   # Show current and supported baud rates
lohix-cli set-baud 921600             # Set baud rate
```

---

### 🔹 Peripheral Discovery

```bash
lohix-cli advertise                   # List available resources
```

---

### 🔹 LED Controls

```bash
lohix-cli led status                  # Query LED states
lohix-cli led on <index>             # Turn ON LED
lohix-cli led off <index>            # Turn OFF LED
lohix-cli led compass-mode           # Map compass heading to LED
lohix-cli led gyro-mode              # Map gyro state to LED
```

---

### 🔹 Sensor Logging

```bash
lohix-cli log compass <duration-ms>  # Log compass data
lohix-cli log gyro <duration-ms>     # Log gyro data
lohix-cli log temp <duration-ms>     # Log temperature
```

---

### 🔹 Button Configuration

```bash
lohix-cli button-config set <index> <pattern> <action>
lohix-cli button-config show [index]
```

Patterns can be:

- `single`
- `double`
- `long`
- `triple` (optional extension)

Example:

```bash
lohix-cli button-config set 1 double "log gyro 1000"
```

---

### 🔹 Node Discovery (Radio/MQTT)

```bash
lohix-cli discover-nodes             # Discover reachable nodes (radio/MQTT)
```

---

## 🔮 Planned/Future Commands

- `flash <firmware.bin>` — Flash new firmware
- `rtc get/set` — Real-time clock management
- `scan-i2c` — Scan I2C devices

---

## 📚 Examples

```bash
# Interact via serial
lohix-cli --serial /dev/ttyUSB0 whoami

# Log temp sensor for 3 seconds
lohix-cli --serial /dev/ttyUSB0 log temp 3000

# Use MQTT transport
lohix-cli --network mqtt://broker.local advertise

# With config file
lohix-cli led compass-mode
```

---

## 🧩 Development Tips

- Use [`clap`](https://docs.rs/clap/latest/clap/) for CLI parsing
- Build CBOR messages using [`serde_cbor`](https://docs.rs/serde_cbor/)
- Use `mock_serial` or `tokio-test` for transport testing
- Support dry-run and verbose modes (`--dry-run`, `--verbose`)

---

## 🧠 Philosophy

> A CLI is not just a tool — it's your first debugger, your testing rig, and your remote control. `lohix-cli` helps bridge the gap between embedded reality and software control, one CBOR packet at a time.

---

## 🚀 See Also

- [Firmware Spec](../docs/device-control.md)
- [Web Interface](../docs/web-ui.md)
- [Lohix Project Overview](../README.md)

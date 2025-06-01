# Project: **Lohix** — An End-to-End Rust Systems Playground

## 📌 Project Description

**Lohix** is an educational, modular, and professional-grade project designed to explore the **Rust ecosystem** across a full stack — from embedded firmware to user-facing desktop, web, mobile apps, and even a Linux device driver.

At the core of Lohix is the **STM32F3 Discovery Board**, which exposes its onboard peripherals (LEDs, compass, accelerometer, etc.) over a structured **AT command interface** using **UART** or **USB**. This command interface becomes the foundation for building multiple user-facing control and monitoring interfaces.

The project is an exercise in:

- Embedded Rust development
- Command-line application design
- Web servers in Rust
- React and GTK-based UI development
- TDD and modular architecture
- Potential Linux kernel driver integration

---

## 🌱 Name Genesis — Why _Lohix_?

The name **Lohix** is derived from:

- **"Loha"** (लोह), the Sanskrit word for **iron** — symbolizing strength, hardware, and the foundational element behind Rust’s name.
- The suffix **`-ix`**, evoking modern system-level technology and tying in with naming patterns like Unix, Ferrix, and others.
- A concise, powerful name that bridges ancient strength and modern systems thinking.

Lohix represents a unified control platform — one where you command real hardware, visualize state across devices, and do it all with Rust.

---

## 🤩 Project Layers

Lohix is divided into modular components, each a standalone project with its own testing and abstraction layers.

| Layer                                                      | Description                                                                                                                 |
| ---------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------- |
| [**Firmware**](./docs/firmware.md)                         | Embedded Rust firmware running on STM32F3, handling AT commands over UART/USB to control peripherals                        |
| [**AT Command Specification**](./docs/device-control.md)   | Defined, extensible command format for communicating with the firmware                                                      |
| [**CLI Application**](./docs/cli-tool.md)                  | A Rust-based terminal app to convert user-friendly commands to AT commands                                                  |
| [**Backend Web Server**](./docs/web-server.md)             | Web API written in Rust that wraps the CLI, enabling browser-based interaction                                              |
| [**React Web UI**](./docs/web-ui.md)                       | Modern single-page application (SPA) to control and monitor board peripherals                                               |
| [**Desktop App (rs-gtk)**](./docs/gtk-app.md)              | A native GUI written using GTK in Rust, reusing the command abstraction                                                     |
| [**Mobile App (Android, optional)**](./docs/mobile.md)     | A future extension using Kotlin or Flutter that communicates with the web backend or directly via USB OTG                   |
| [**Linux Device Driver (future)**](./docs/linux-driver.md) | A learning exploration into Linux kernel development, replacing the CLI/server and allowing direct device file I/O from UIs |

---

## 💠 Goals

- ✅ Hands-on embedded Rust development using real hardware
- ✅ Practice modular architecture, where components are independently testable
- ✅ Use TDD where possible: e.g., the CLI app can be developed and tested using mocked responses before firmware exists
- ✅ Develop portable and reusable logic (e.g., shared command parser library)
- ✅ Build confidence in building Linux tools, drivers, and UIs
- ✅ Serve as a **learning playground** and potentially a teaching/demo tool

---

## 🔧 Project Structure

This `README.md` serves as the **root** of the repository. Submodules will be documented in individual markdown files:

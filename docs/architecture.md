# Custom USB Device Stack -- Architecture Overview

## 1. Project Goal

Design and implement a full-stack custom USB device system consisting
of:

- Custom firmware (USB vendor class, bulk endpoints)
- Linux kernel driver (C)
- Userland device daemon (Rust)
- CLI + Web control interface (Spring Boot)
- Optional streaming data logger

The goal is clean separation of concerns across all layers.

---

# 2. High-Level Architecture

    ┌──────────────────────────────┐
    │         Web Frontend         │
    │   (Browser via WebSocket)    │
    └──────────────┬───────────────┘
                   │
                   ▼
    ┌──────────────────────────────┐
    │        Spring Boot API       │
    │   (HTTP / REST / WebSocket)  │
    └──────────────┬───────────────┘
                   │ Unix Domain Socket
                   ▼
    ┌──────────────────────────────┐
    │     Rust Device Daemon       │
    │  - Device state manager      │
    │  - ioctl wrapper             │
    │  - Stream multiplexer        │
    │  - Reconnection logic        │
    └──────────────┬───────────────┘
                   │ ioctl (C ABI)
                   ▼
    ┌──────────────────────────────┐
    │     Linux Kernel Driver      │
    │  - Character device          │
    │  - ioctl interface           │
    │  - USB bulk transport        │
    │  - Protocol translation      │
    └──────────────┬───────────────┘
                   │ USB Bulk Endpoints
                   ▼
    ┌──────────────────────────────┐
    │      Device Firmware         │
    │  - USB vendor class          │
    │  - Packet protocol           │
    │  - Hardware control          │
    └──────────────────────────────┘

---

# 3. Layer Responsibilities

## 3.1 Firmware

- Implements custom USB vendor class
- Uses bulk IN/OUT endpoints
- Implements packet-based protocol
- Handles:
  - Command execution
  - Event generation
  - Streaming data (e.g., data logger)
- No awareness of Linux, web, or userland APIs

---

## 3.2 Kernel Driver (C)

### Responsibilities

- Registers as USB driver
- Exposes character device (`/dev/mydevice`)
- Implements `ioctl` interface
- Translates ioctl calls → device protocol packets
- Sends/receives via USB bulk endpoints

### Design Principles

- No string parsing
- No business logic
- No web awareness
- Stable binary ABI
- Thin transport + validation layer

### ABI Contract

- Shared C header (`mydevice_ioctl.h`)
- All structs follow C ABI layout
- Rust side uses `#[repr(C)]` to match layout
- `_IO`, `_IOW`, `_IOR`, `_IOWR` macros used properly

---

## 3.3 Rust Device Daemon

Runs as system service (systemd unit).

### Responsibilities

- Opens `/dev/mydevice`
- Performs ioctl calls
- Manages device state
- Handles reconnection if device resets
- Handles streaming read loop
- Exposes Unix Domain Socket API
- Serializes concurrent access
- Provides safe abstraction layer

### Why a Daemon?

- Decouples hardware from web layer
- Prevents multiple processes from racing device access
- Centralizes retry and error handling
- Enables streaming fan-out to multiple clients
- Avoids JNI/JNA complexity in Java

---

## 3.4 IPC: Unix Domain Socket

Communication between:

- Rust daemon
- Spring Boot
- CLI tools

### Socket Type

- `SOCK_STREAM`
- Full duplex
- Long-lived connections
- Low latency

### Benefits

- Faster than TCP
- File-permission based security
- Suitable for continuous streaming
- Multiple client support

---

# 4. Userland Interface Strategy

## 4.1 CLI Tool

CLI parses human-readable commands:

    mydevctl set-led 3 on

CLI: - Parses strings - Converts to structured request - Sends to Rust
daemon over Unix socket

Driver never parses strings.

---

## 4.2 Spring Boot Integration

Spring Boot:

- Connects to Unix socket (Java 16+ native support)
- Sends structured requests
- Receives responses or streaming data
- Exposes:
  - REST endpoints
  - WebSocket endpoints

Example:

    POST /device/led
    {
      "id": 3,
      "state": true
    }

---

# 5. Data Flow Types

## 5.1 Control Flow

    Web → Spring → Unix Socket → Rust → ioctl → Driver → USB → Firmware

## 5.2 Streaming Flow (Data Logger)

    Firmware → USB → Driver → Rust daemon → Unix socket → Spring → WebSocket → Browser

Rust daemon may: - Buffer - Apply backpressure - Broadcast to multiple
clients

---

# 6. Design Principles

### 6.1 Separation of Concerns

- Firmware = hardware logic
- Driver = transport + ABI
- Rust = device service layer
- Spring = business + web

---

### 6.2 ABI Stability

- C header is source of truth
- Rust bindings generated via `bindgen`
- Avoid breaking struct layouts
- Version field included in protocol packets

---

### 6.3 No String Parsing in Kernel

- Kernel uses structured binary contract
- All parsing happens in userland
- Driver only validates structured input

---

### 6.4 Layered Protocol Design

Firmware Protocol: - Versioned - Length-prefixed packets - Sequence
numbers - CRC - Command / Response / Event types

Unix Socket Protocol: - Length-prefixed messages - Binary or structured
format - Separate control + streaming types

---

# 7. Future Extensions

- gRPC over Unix socket
- WebSocket passthrough streaming
- Authentication at daemon layer
- Multi-device support
- Hot-plug detection
- Metrics and observability

---

# 8. Why This Architecture?

This design provides:

- Clean layering
- Cross-language compatibility
- High performance
- Stream support
- Safe concurrency
- Hardware abstraction
- Production-grade extensibility

---

# 9. Next Steps

1.  Finalize USB device descriptor design
2.  Define firmware packet structure
3.  Define ioctl ABI
4.  Implement minimal driver skeleton
5.  Implement minimal Rust daemon
6.  Add Unix socket control path
7.  Add streaming pipeline

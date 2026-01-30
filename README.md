<p align="center">
  <img src="https://raw.githubusercontent.com/STMicroelectronics/STM32CubeF4/master/Drivers/CMSIS/Device/ST/STM32F4xx/Include/stm32f407xx.h" width="120"/>
</p>

<h1 align="center">🚀 STM32F407_FOTA</h1>

<p align="center">
  <b>Robust Dual-Slot Firmware-Over-The-Air (FOTA) Manager</b><br/>
  <i>Safety-first | Zero-brick | SPI-accelerated updates</i>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/MCU-STM32F407-blue"/>
  <img src="https://img.shields.io/badge/Host-ESP32-orange"/>
  <img src="https://img.shields.io/badge/Protocol-SPI-green"/>
  <img src="https://img.shields.io/badge/FOTA-Dual%20Slot-success"/>
  <img src="https://img.shields.io/badge/Cloud-AWS%20S3-yellow"/>
</p>

---

## 🌟 Overview

**STM32F407_FOTA** is a production-grade **Firmware Over-The-Air update system** designed for embedded devices that **must never brick**.

It combines:
- A **custom STM32 flash bootloader**
- A **dual-slot (A/B) firmware architecture**
- An **ESP32 Wi-Fi host**
- A **high-speed SPI update pipeline**
- **Cloud-driven versioning via AWS S3**

> ⚠️ Firmware updates are streamed into an *inactive slot* while the active application continues running — ensuring **zero-risk deployments**.

---

## 🧠 Design Philosophy

✅ Safety over speed  
✅ Atomic updates  
✅ Deterministic communication  
✅ Autonomous operation  
✅ Production-ready fault tolerance  

This system was built to **survive power loss, network dropouts, and partial writes** without ever leaving the device unbootable.

---

## 🏗️ System Architecture

```text
          ┌────────────────────┐
          │      AWS S3        │
          │ Firmware + Metadata│
          └─────────┬──────────┘
                    │ Wi-Fi
             ┌──────▼───────┐
             │    ESP32     │
             │ SPI Master   │
             │ Update Logic │
             └──────┬───────┘
                    │ SPI (MOSI/MISO/SCK/CS)
        ┌───────────▼──────────┐
        │       STM32F407      │
        │  Custom Flash Loader │
        │ ┌────────┬─────────┐ │
        │ │ Slot A │ Slot B  │ │
        │ └────────┴─────────┘ │
        └──────────────────────┘
```
## 🔩 Hardware Components

### 🎯 Target MCU
- **STM32F407 Discovery**
  - ARM Cortex-M4
  - Custom flash bootloader (Sector 0)
  - Dual firmware slots (A / B)

### 🌐 Host Controller
- **ESP32 (NodeMCU / DevKit)**
  - Wi-Fi connectivity
  - SPI Master
  - Boot control via **BOOT0 / NRST**

### 🔗 Physical Interconnect
- **SPI1** – High-speed synchronous programming interface  
- GPIO-controlled reset line  
- Mandatory common ground reference  

---

## 🔌 Hardware Wiring (SPI)

> ⚠️ **Important:** SPI requires short wiring, clean grounding, and impedance awareness due to the clock signal (SCK).

| ESP32 Pin | STM32F407 Pin | Signal |
|----------|---------------|--------|
| GPIO 23  | PA7 | MOSI (Host → Target) |
| GPIO 19  | PA6 | MISO (Target → Host) |
| GPIO 18  | PA5 | SCK (Clock) |
| GPIO 5   | PA4 | NSS / CS |
| GND      | GND | Common Ground |

---

## 🧪 Software Stack

### STM32
- **STM32CubeIDE**
- HAL-based custom flash bootloader
- Slot-aware application logic

### ESP32
- **Arduino IDE**
- Wi-Fi + SPI Master controller
- Firmware update orchestrator

### ☁️ Cloud
- **AWS S3**
  - Firmware binaries
  - Version metadata
  - ETag-based update detection

---

## 📡 Communication Protocol

- **ST Serial Bootloader Protocol**
- **ST SPI Bootloader Protocol** (AN4286 compliant)

**Key Characteristics**
- 🔁 ACK / NACK handling  
- ⏱ Dummy-byte clocking for response reads  
- 🔒 Deterministic, state-driven execution  

---

## 🧩 Core Components

### ☁️ Cloud Gateway (AWS S3)
- Stores firmware images
- Provides version metadata
- Enables autonomous update checks

### 🧠 ESP32 Host
- Polls cloud for firmware updates
- Controls **BOOT0** and **NRST**
- Drives SPI clock and framing
- Streams firmware to STM32

### 🛠 STM32 Flash Bootloader
- Resides in Flash **Sector 0**
- Selects Slot A or Slot B
- Verifies update integrity
- Performs safe application jump

### 🧾 Metadata Sector
- Reserved Flash **Sector 11**
- Stores:
  - Magic number
  - Active slot flag
  - Update validity marker

> 🔐 Enables **atomic commit and rollback protection**

---

## 🔁 Firmware Update Flow

1. ESP32 polls AWS S3 for updates  
2. New firmware version detected (ETag)  
3. STM32 forced into bootloader mode  
4. SPI session initialized  
5. Firmware streamed into inactive slot  
6. Metadata updated atomically  
7. System reboot  
8. New firmware validated  
9. Automatic rollback on validation failure  

---

## ⚠️ Important Notes

- ESP32 **fully controls the SPI clock**
- STM32 responds strictly on clock edges
- Dummy bytes are required to read ACK responses
- Wiring quality directly impacts system stability

---

## 📜 License

This project is intended for **educational and experimental use**.  
Evaluate and harden further before deploying in safety-critical systems.

---

## ⭐ If You Like This Project

Give it a ⭐ and feel free to:
- Fork it
- Extend it
- Adapt it for production
- Use it as a reference architecture

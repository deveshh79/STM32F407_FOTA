# STM32F407_FOTA
Firmware Over the Air Update for STM32. Having a custom bootloader and ESP32 as the network interface.
Robust Dual-Slot FOTA Manager for STM32F407

Project Overview
This project implements an autonomous, cloud-connected Firmware Over-The-Air (FOTA) update system. It utilizes an ESP32 as a host controller to bridge AWS S3 cloud storage with an STM32F407 target. The system is designed with a "safety-first" philosophy, employing a dual-bank memory strategy that allows firmware updates to be streamed into a secondary slot while maintaining a stable primary application, ensuring zero-risk deployments.
Moving beyond traditional asynchronous methods, this system leverages SPI (Serial Peripheral Interface) to create a high-bandwidth data pipeline. The architecture retains a "safety-first" philosophy, employing a dual-bank memory strategy that streams firmware into a secondary slot while maintaining a stable primary application, ensuring zero-risk deployments with superior transmission speeds.

Hardware Used
STM32F407 Discovery Board: The target microcontroller (Cortex-M4) featuring the dual-slot application logic.
ESP32 (NodeMCU/DevKit): The host controller acting as the SPI Master, managing Wi-Fi connectivity and the update clock.
Physical Interconnects:  SPI1 (PA4/PA5/PA6/PA7): High-speed synchronous programming interface.
GPIO Control: Reset (NRST)
Common Ground: Absolute reference required for signal integrity and noise immunity in high-frequency clocking.

Hardware Configuration
The system requires a common ground between the ESP32 and STM32. Wiring is critical for the synchronization phase.The shift to SPI requires a 4-wire bus topology. Unlike UART, wiring length and impedance matching become more critical due to the clock signal (SCK).
ESP32 Pin	STM32 Pin	Function
GPIO 23 (MOSI)	PA7 (MOSI)	Data: Host to Target
GPIO 19 (MISO)	PA6 (MISO)	Data: Target to Host
GPIO 18 (SCK)	PA5 (SCK)	Clock Source
GPIO 5 (CS/SS)	PA4 (NSS)	Chip Select / Slave Select
GND	GND	Common Ground Reference

Software Used
STM32CubeIDE: Used for developing the Custom Flash Bootloader and the main application code (C/HAL).
Arduino IDE: Used for developing the ESP32 Host logic (C++/Arduino).
AWS S3: Cloud infrastructure for binary storage and version metadata.
ST Serial Bootloader Protocol: The low-level communication standard for flash memory access.
ST SPI Bootloader Protocol: The specific command set (documented in ST AN4286) used for communicating with the STM32 system memory via SPI.

Key Components & Process Highlights
Key Components
Cloud Gateway (AWS S3): Acts as the remote repository for firmware binaries, utilizing ETags for efficient version tracking.
Host Controller (ESP32): Orchestrates the update lifecycle, including cloud polling, physical pin manipulation (BOOT0/NRST), and protocol execution.
Custom Flash Bootloader (STM32): A dedicated first-stage bootloader residing in Sector 0 that manages the logic-based jump between Slot A and Slot B.
SPI Master (ESP32): Orchestrates the update lifecycle. It controls the bus clock, manages the Chip Select (NSS) line to frame transactions, and handles physical pin manipulation.
SPI Slave (STM32 Bootloader): Listens on the SPI bus. It receives commands and data strictly on the clock edges provided by the ESP32.
Metadata Sector: A reserved Flash region (Sector 11) used to store the "Magic Number" and boot flags required for atomic commitment.
Protocol: ST SPI Bootloader (Ack/Nack handling).Synchronization: ESP32 sends "Dummy Bytes" to clock out the Acknowledgement (ACK) byte from the STM32.

Achievements
Fail-Safe Redundancy: Successfully implemented a dual-slot layout that prevents system bricking during interrupted updates.

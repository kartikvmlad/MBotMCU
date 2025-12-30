# Astra Robotics Controller (ARC-2040)

![Hardware Version](https://img.shields.io/badge/Hardware_Version-v1.2-blue) ![Status](https://img.shields.io/badge/Status-Verified-success) ![MCU](https://img.shields.io/badge/MCU-RP2040-red)

**A high-performance embedded control board designed for autonomous mobile robotics.**

The **Astra Robotics Controller** bridges the gap between prototyping boards and industrial PLCs. Built around the Raspberry Pi **RP2040** microcontroller, it integrates high-current motor drivers, redundant sensing, and non-volatile "Black Box" data logging into a unified, vibration-resistant PCB.

-> To view the schematic, download the repository and open the file **MBot.kicad_sch** in KiCad 

---

## 📋 Table of Contents
- [System Architecture](#-system-architecture)
- [Technical Specifications](#-technical-specifications)
- [Pin Configuration](#-pin-configuration)
- [Power System & Safety](#-power-system--safety)
- [Fabrication Notes](#-fabrication-notes-bom-criticals)
- [Getting Started](#-getting-started)

---

## 🏗 System Architecture

The board is divided into four isolated subsystems to minimize noise and maximize reliability:

1.  **Core Logic:** RP2040 Dual-Core Cortex-M0+ running at 133MHz with 2MB QSPI Flash.
2.  **Actuation:** Dual **DRV8876** H-Bridge drivers capable of 5A peak current, featuring hardware current regulation and fault feedback.
3.  **Sensing:**
    * **Motion:** InvenSense **MPU-9150** (9-DoF IMU) for heading and stabilization.
    * **Environment:** Bosch **BMP280** for barometric altitude and temperature.
    * **Data Logging:** Cypress **FM24W256 F-RAM** for high-speed, infinite-endurance telemetry logging.
4.  **Power:** A hybrid 12V $\to$ 6.5V $\to$ 5V/3.3V power tree using a high-efficiency **XL4015** Buck Converter.

---

## ⚙ Technical Specifications

| Feature | Specification |
| :--- | :--- |
| **Microcontroller** | Raspberry Pi RP2040 (Dual Core ARM M0+ @ 133MHz) |
| **Input Voltage** | 10V - 14V DC (Optimized for 3S LiPo or 12V Lead Acid) |
| **Motor Drive Output** | 2x Channels, 6.5V Regulated @ 3A Continuous / 5A Peak |
| **Logic Voltage** | 3.3V (System) / 5.0V (Auxiliary) |
| **IMU** | MPU-9150 (Gyro + Accel + Mag) via I2C `0x68` |
| **Altimeter** | BMP280 via I2C `0x76` |
| **Non-Volatile Memory** | 256 Kbit (32KB) Ferroelectric RAM via I2C `0x50` |
| **Expansion** | 4x ADC, 2x UART, 1x I2C, 1x SPI |

---

## 📍 Pin Configuration

### External Interfaces
| Port Label | Function | Pinout | Voltage Level |
| :--- | :--- | :--- | :--- |
| **J1 (SWD)** | Firmware Debugging | SWDIO, SWCLK, GND | 3.3V |
| **J3 (ADC)** | Analog Sensors | ADC0-2, 3V3, AGND | 3.3V |
| **J7 (UART0)** | GPS / Lidar | TX, RX, 3V3, GND | 3.3V |
| **J8 (I2C)** | Expansion / OLED | SDA, SCL, 3V3, GND | 3.3V |
| **J14 (PWM)** | Servo / GPIO | PWM_A, PWM_B, 3V3, GND | 3.3V |

### Onboard Switch Multiplexer
The board features hardware toggle switches to route the RP2040 ADC lines:
* **Position A (Left):** ADC monitors **Motor Current** (via DRV8876 IPROPI).
* **Position B (Right):** ADC monitors **External Sensors** (via Port J3).

---

## ⚡ Power System & Safety

This board implements a **Split-Rail Architecture**:
* **VMOT (6.5V Rail):** Powered by an XL4015 Buck Converter. Dedicated to Motors and High-Torque Servos.
* **VSYS (5.0V Rail):** Powered by an AMS1117-5.0. Dedicated to logic-level conversion.
* **3V3 (Logic Rail):** Powered by onboard LDO for MCU and Sensors.

**Safety Features:**
* **Reverse Polarity Protection:** 5A Schottky Diodes on input.
* **Over-Current Protection:** On-board Fuse (F1) and software-readable Current Sense.
* **Thermal Protection:** DRV8876 drivers have automatic thermal shutdown with LED indication (`nFAULT`).

> **⚠️ Usage Warning:** Port `J8`, `J9`, and `J10` (Motor/Aux) supply **6.5V** on Pin 3. Do not connect standard 5V-intolerant logic devices to this pin without checking datasheets.

---

## 🛠 Fabrication Notes (BOM Criticals)

If fabricating this board from the provided Gerbers, strictly adhere to the following component requirements to ensure power stability:

1.  **Diodes (D4, D5, D6):** MUST be **SS54** (5 Amp) or **MBRD1045** (10 Amp). Using SS34 (3A) may result in thermal failure under load.
2.  **Inductor (L1):** Footprint requires a **Shielded Power Inductor** (e.g., CDRH127) rated for **Isat > 5A**.
3.  **Capacitors:**
    * **Cout1:** 330µF/25V Electrolytic Low-ESR is mandatory for 6.5V rail stability.
    * **CC1:** Ensure 1µF ceramic is placed between XL4015 Pin 5 (VIN) and Pin 4 (VC).

---

## 🚀 Getting Started

1.  **Hardware Setup:**
    * Connect a 12V battery to the Screw Terminal (J3) or Barrel Jack (J2).
    * Connect motors to the JST outputs `M0` and `M1`.
2.  **Firmware Upload:**
    * Hold the **BOOT** button (or short `BOOTSEL` jumper).
    * Connect USB-C to a PC.
    * Copy your `.uf2` file to the `RPI-RP2` drive.
3.  **Software Addresses:**
    * `BMP280`: **0x76**
    * `MPU9150`: **0x68**
    * `FRAM`: **0x50**

---

**Project Lead:** Kartik Lad  
**Organization:** Astra Robotics  
**License:** Open Source Hardware

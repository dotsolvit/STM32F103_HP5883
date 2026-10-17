# HP5883 / QMC5883P Magnetometer Driver for STM32F103

**Read me in English** | [Читати мене Українською](README_uk.md)

A lightweight, efficient, and **non-blocking C driver** for the modern **HP5883 (QMC5883P)** 3-axis digital compass, built upon the **STM32CubeHAL** library for the STM32F103C8T6 (Blue Pill) microcontroller.

Unlike legacy QMC5883L/HMC5883L sensors, this modern Chinese silicon revision features a completely redesigned register map, a different I2C address, and customized over-sampling registers. This repository provides a hardware-verified, production-ready implementation tailored to these specific hardware characteristics.

---

## 🎯 Key Features

- **Hardware-Specific Tuning:** Out-of-the-box support for the unique `0x2C` 7-bit I2C address and internal register layout of the HP5883.
- **Zero-Blocking Architecture:** Fully non-blocking sensor polling utilizing `HAL_GetTick()`, preventing main loop execution stalls.
- **Embedded Auto-Calibration:** Built-in runtime Hard-Iron offset calibration algorithm that tracks \(X/Y\) min/max values dynamically to eliminate ambient magnetic distortion.
- **Hardware Filtering:** Configured for maximum over-sampling ratio (`OSR2 = 8, OSR1 = 8`) to achieve the cleanest azimuth (heading) angle stability.

---

## 🛠️ Hardware & Toolchain Profile

- **Target MCU:** STM32F103C8T6 (ARM Cortex-M3 @ 72 MHz)
- **Sensor:** HP5883 / QMC5883P (Marked as *HP5883 5702*)
- **IDE:** STM32CubeIDE (GCC Compiler toolchain)
- **Bus Profile:** I2C1 Standard Mode (100 kHz)
- **Verification Hardware:** OWON SDS210S Digital Oscilloscope

---

## 🔌 Hardware Connection (Pinout)

Connect the sensor directly to the STM32F103 I2C1 peripheral pins:

| Sensor Pin | STM32F103 Pin | Description |
| :---: | :---: | :--- |
| **VCC** | **3.3V** | Main Power Supply |
| **GND** | **GND** | Common Ground |
| **SCL** | **PB6** | I2C1 Clock (5V Tolerant) |
| **SDA** | **PB7** | I2C1 Data (5V Tolerant) |

---

## ⚙️ Driver Architecture Details

### 1. Register Relocation Fix
The driver explicitly maps the relocated register topology of the **QMC5883P**:
- Data registers start at `0x01` (instead of `0x00`).
- Status register moved to `0x09` (instead of `0x06`).
- Control registers shifted to `0x0A` and `0x0B`.

### 2. High-Fidelity Signal Filtering
The initialization configuration writes `0xC3` into `REG_CTRL1 (0x0A)`. This maps to maximum hardware over-sampling (`OSR1=8, OSR2=8`) at a stable `10 Hz` Output Data Rate (ODR) in Continuous Mode, minimizing digit jitter on the azimuth calculations.

---


## 🤝 Credits & Acknowledgments

Developed as an educational and portfolio milestone for autonomous unmanned ground vehicle (UGV) navigation algorithms. Special thanks to the **Nanit Robot Academy** engineering community for support and toolchain orchestration.

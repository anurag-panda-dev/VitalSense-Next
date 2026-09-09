# VitalSense-Next: Hardware Design Specification

## 1. Purpose

This document defines the hardware design logic for the VitalSense-Next wearable node. The selected implementation uses the Arduino Uno R4 WiFi as the control and communication platform, with sensors for PPG, temperature, and movement detection.

---

## 2. Hardware Overview

### 2.1 Core Platform
- Arduino Uno R4 WiFi
- MCU: Renesas RA4M1, Cortex-M4, 48 MHz
- SRAM: 256 KB
- Flash: 1 MB
- Wi-Fi connectivity: onboard

### 2.2 Sensor Stack
- MAX30102: heart rate and pulse oximetry
- DS18B20: temperature measurement
- MPU-6050: acceleration and gyroscope

### 2.3 Power Architecture
- power source: 3.7V LiPo battery
- regulator: 3.3V low-dropout regulator
- logic rail: 3.3V for sensors and digital logic
- optional battery monitor or ADC-based voltage sensing for low-battery warnings

---

## 3. Electrical Design Requirements

### 3.1 I2C Bus Design
The MAX30102 and MPU-6050 shall share the same I2C bus.

Recommended bus:
- SDA: A4
- SCL: A5
- bus speed: 400 kHz

Requirements:
- keep trace lengths short
- keep bus away from noisy high-current traces
- use 100 nF decoupling near each sensor power pin

### 3.2 1-Wire Temperature Design
The DS18B20 uses a 1-Wire interface.

Recommended connection:
- data line: D2
- 4.7 kΩ pull-up resistor to 3.3V
- ground and VCC tied to 3.3V rail

### 3.3 Power Integrity Rules
- 100 nF ceramic decoupling at each sensor VCC pin
- 1 µF bulk capacitor on the 3.3V rail
- stable ground plane across sensor region
- avoid routing the sensor signal traces next to the Wi-Fi antenna path or power switching lines

---

## 4. Pin Mapping

| Component | Function | Arduino Pin | Notes |
| --- | --- | --- | --- |
| MAX30102 | SDA | A4 | I2C bus |
| MAX30102 | SCL | A5 | I2C bus |
| MAX30102 | INT | D3 or configurable GPIO | optional interrupt |
| DS18B20 | Data | D2 | 1-Wire |
| MPU-6050 | SDA | A4 | shared I2C |
| MPU-6050 | SCL | A5 | shared I2C |
| Serial Debug | TX/RX | USB or D0/D1 | monitoring |
| Power | Vin / 3.3V | regulated input | LiPo through regulator |

---

## 5. Sensor Interface Design

### 5.1 MAX30102
Operating notes:
- use proper clock and bus configuration for I2C
- enable heart-rate and pulse-ox modes carefully to avoid excessive power draw
- add filtering at firmware layer to reduce motion artifact

### 5.2 DS18B20
Operating notes:
- one-wire protocol requires careful timing and pull-up stability
- maintain a stable digital input level for clean sampling

### 5.3 MPU-6050
Operating notes:
- use calibrated zero-g and sensitivity configuration
- sample acceleration and gyro in a low-overhead loop
- compute motion magnitude for fall detection logic

---

## 6. Power Budget Considerations

### 6.1 Estimated Current Draw
| Device | Approximate Current |
| --- | --- |
| Arduino Uno R4 WiFi | 70-200 mA depending on load |
| MAX30102 | 1-10 mA |
| DS18B20 | <1 mA |
| MPU-6050 | 3-6 mA |

This suggests the wearable design should use a battery with sufficient headroom, and firmware should avoid continuous high-power Wi-Fi streaming if battery life is a critical project metric.

### 6.2 Battery Recommendation
- 3.7V LiPo, 650-1000 mAh typical range
- prefer low self-discharge chemistry for prototype deployment
- enable low-power sleep or reduce sample rates when battery is low

---

## 7. Schematic Design Notes

### 7.1 Basic Schematic Pattern
- Battery input to regulator
- regulator output to 3.3V rail
- common ground between board and sensors
- I2C share between MAX30102 and MPU-6050
- DS18B20 connected to D2 with 4.7 kΩ pull-up
- optional LED indicators for status and alarm state

### 7.2 Protection Elements
- reverse polarity protection (recommended)
- small series resistor on power entry if needed
- transient suppression or smoothing capacitor on battery input

---

## 8. Interference and Noise Considerations

- keep I2C traces short and matched in routing pattern
- avoid routing near antenna or switching regulator noise
- isolate analog sensor returns where possible
- use a solid ground plane underneath the sensor section

---

## 9. Firmware/Hardware Co-Design Constraints

Because the Arduino Uno R4 WiFi has only 256 KB SRAM, the firmware must:
- use compact, fixed-size buffers;
- avoid excessive sample history accumulation;
- reduce per-packet serial overhead;
- process data in streaming batches instead of storing large waveform arrays continuously.

---

## 10. Hardware Validation Plan

### 10.1 Connectivity Checks
- verify I2C addresses are detected correctly;
- ensure DS18B20 responds on the 1-Wire bus;
- confirm MPU-6050 can read X/Y/Z sensor values reliably.

### 10.2 Power Validation
- verify board remains stable under 3.3V rail load;
- confirm no brownout during sensor bursts;
- monitor battery drain during sustained sampling.

### 10.3 Signal Quality Checks
- validate PPG waveform under motion and still conditions;
- confirm temperature changes correspond to real thermal variation;
- test accelerometer noise under idle and moving conditions.

---

## 11. Safety and Practical Design Notes

- the system is intended for non-clinical monitoring and early-warning support;
- sensor placement should be standardized for consistency;
- battery and thermal behavior must be checked before long-run testing;
- firmware should degrade gracefully when sensor input becomes invalid or noisy.

---

## 12. Summary

The hardware architecture is intentionally simple and realistic for an academic prototype: a compact wearable node built around the Arduino Uno R4 WiFi, centralized sensor acquisition, and limited but effective Wi-Fi telemetry. The design balances cost, portability, and technical feasibility while retaining sufficient signal fidelity for a valid health-monitoring project.

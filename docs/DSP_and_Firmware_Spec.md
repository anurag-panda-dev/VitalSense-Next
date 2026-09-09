# VitalSense-Next: DSP and Firmware Specification

## 1. Purpose

This specification defines the real-time firmware logic for the Arduino Uno R4 WiFi-based VitalSense-Next wearable node. It covers signal acquisition, filtering, estimation logic, alert evaluation, and control flow required to convert raw sensor signals into usable health telemetry.

---

## 2. Firmware Architecture

### 2.1 Execution Model
The firmware shall operate as a lightweight real-time loop with sensor polling and signal processing. The major phases are:
1. sensor acquisition;
2. filtering and feature extraction;
3. alert evaluation;
4. telemetry transmission;
5. offline buffering and retry.

### 2.2 Timing Requirements
- MAX30102 sampling: approximately 100 Hz
- DS18B20 sampling: around 1 Hz
- MPU-6050 sampling: around 50-100 Hz
- alert evaluation: every 1 second
- telemetry send: event-driven and network-aware

---

## 3. Sensor Acquisition Plan

### 3.1 MAX30102 Acquisition
The firmware shall read red and infrared samples continuously and store them in a compact rolling buffer. Each acquisition loop shall:
- check for new data-ready status;
- read red and IR values;
- timestamp the sample;
- append to rolling buffer for processing.

### 3.2 DS18B20 Acquisition
The DS18B20 shall be polled at lower frequency to avoid unnecessary 1-Wire timing overhead.

### 3.3 MPU-6050 Acquisition
The firmware shall capture accelerometer and gyroscope values for fall detection and motion classification.

---

## 4. DSP and Signal Processing

### 4.1 PPG Processing Chain
The PPG signal pipeline shall include:
- DC removal or baseline tracking;
- moving-average smoothing;
- noise rejection via threshold and signal-quality logic;
- beat detection by peak interval detection;
- calculation of pulse rate and pulse quality.

### 4.2 Filtering Approach
A practical filter stack is:
- moving average window of 5-15 samples for short-term smoothing;
- band-pass-like low-frequency emphasis around rapid pulse activity;
- artifact suppression when motion acceleration magnitude crosses a threshold.

### 4.3 Heart Rate Estimation
The system shall compute heart rate from valid inter-beat intervals.

$$
HR_{BPM} = \frac{60}{\bar{T}_{beat}}
$$

where $\bar{T}_{beat}$ is the mean interval between consecutive valid peaks.

### 4.4 SpO2 Approximation
The ratio of red and infrared absorbance changes shall be calculated.

$$
R = \frac{AC_{red}/DC_{red}}{AC_{ir}/DC_{ir}}
$$

and then

$$
SpO_2 = a - bR
$$

where $a$ and $b$ are calibration constants.

### 4.5 Signal Quality Metric
A quality score between 0 and 100 shall be calculated using:
- peak validity
- signal-to-noise ratio
- motion artifact estimate
- continuity of detected pulse pattern

---

## 5. Fall Detection and Motion Logic

### 5.1 Vector Magnitude Unit (VMU)
The firmware shall compute a motion magnitude estimate for inertial events.

$$
VMU = \sqrt{a_x^2 + a_y^2 + a_z^2}
$$

### 5.2 Fall Detection Logic
A candidate fall event shall be flagged when:
- acceleration crosses a high-impact threshold;
- motion quickly drops to a low-energy state;
- a post-impact inactivity window is observed;
- the signal quality from motion data is sufficient to avoid false positives.

### 5.3 False Alarm Protection
- require sustained inactivity after a burst event;
- apply a configurable grace window before emergency escalation;
- provide a local user cancellation or confirmation path when available.

---

## 6. Alert Evaluation Logic

### 6.1 Alert Priority Levels
- normal
- warning
- critical
- emergency/fall-event

### 6.2 Example Decision Rules
| Metric | Warning | Critical |
| --- | --- | --- |
| Heart rate | >110 or <50 | >130 or <45 |
| SpO2 | <94% | <90% |
| Body temp | >37.8°C | >38.5°C |
| Fall state | impact spike | impact + inactivity |

### 6.3 Debounce Logic
Threshold triggers shall be validated over a short confirmation window to reduce false positives. The system shall avoid firing on a single noisy sample unless the event is severe.

---

## 7. Firmware Data Structures

```cpp
struct SensorConfig {
  uint32_t ppgSampleRateHz = 100;
  uint32_t tempSampleRateHz = 1;
  uint32_t imuSampleRateHz = 50;
  uint8_t ds18b20Pin = 2;
  uint8_t max30102Addr = 0x57;
  uint8_t mpu6050Addr = 0x68;
  uint32_t serialBaud = 115200;
};

struct VitalPacket {
  unsigned long timestampMs;
  float heartRateBpm;
  float spo2Percent;
  float temperatureC;
  float accelX;
  float accelY;
  float accelZ;
  float gyroX;
  float gyroY;
  float gyroZ;
  uint8_t signalQuality;
  uint8_t fallState;
  uint8_t alertCode;
};
```

---

## 8. Core Firmware Loop

```cpp
void loop() {
  readMax30102();
  readMpu6050();
  readDs18b20();

  if (ppgReady) {
    processPpgSamples();
  }

  if (imuReady) {
    detectMotionState();
  }

  if (secondTick) {
    VitalPacket packet = buildPacket();
    bool alert = evaluateAlertState(packet);

    if (alert || shouldTransmitNow()) {
      transmitTelemetry(packet);
    }
  }
}
```

---

## 9. Memory and Runtime Constraints

The Uno R4 WiFi has 256 KB SRAM and 1 MB flash, so the firmware must keep memory use tight:
- small fixed-size buffer for recent PPG values
- rolling temperature window for short-term smoothing
- short motion history for fall logic
- compact JSON serialization instead of large payload structures

---

## 10. Error Handling and Fault Handling

The firmware shall:
- detect sensor read interruptions;
- mark signal quality as low when the PPG waveform is unstable;
- continue monitoring in degraded mode instead of stopping completely;
- issue a local warning when Wi-Fi transmission fails repeatedly;
- buffer critical readings in memory or flash where available.

---

## 11. Validation Strategy

### 11.1 Functional Validation
- repeated heart-rate measurement under quiet conditions
- reduced quality conditions under motion
- threshold crossing for fever and oxygen events
- fall simulation and inactivity confirmation

### 11.2 Stress Validation
- continuous 30-60 minute runtime checks
- Wi-Fi interruption and reconnection checks
- sensor noise resilience and repeated sample bursts

---

## 12. Summary

The DSP and firmware design emphasizes low-complexity, low-memory, real-time signal processing. It is intentionally practical for an academic health IoT implementation and keeps the device capable of meaningful health monitoring while remaining robust under sensor noise and network interruption.

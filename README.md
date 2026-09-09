<div align="center">

# VitalSense-Next

### *Edge-IoT Biosignal Telemetry and Cloud Health Surveillance*

Arduino Uno R4 WiFi • MAX30102 • DS18B20 • MPU-6050 • React.js • FastAPI

<br>

<img src="https://img.shields.io/badge/Project-Academic_Prototype-1565C0?style=for-the-badge">
<img src="https://img.shields.io/badge/IoT-Wearable_Health_Monitoring-00C853?style=for-the-badge">
<img src="https://img.shields.io/badge/Stack-Arduino_%2B_React_%2B_FastAPI-7B1FA2?style=for-the-badge">
<img src="https://img.shields.io/badge/Status-Documentation_%26_Prototype-FFB300?style=for-the-badge">

<br>

<img src="https://img.shields.io/badge/Hardware-Arduino_UNO_R4_WiFi-00979D?style=for-the-badge&logo=arduino&logoColor=white">
<img src="https://img.shields.io/badge/Sensors-MAX30102_%2B_DS18B20_%2B_MPU-6050-455A64?style=for-the-badge">
<img src="https://img.shields.io/badge/Backend-FastAPI-009688?style=for-the-badge&logo=fastapi&logoColor=white">
<img src="https://img.shields.io/badge/Frontend-React.js-61DAFB?style=for-the-badge&logo=react&logoColor=black">
<img src="https://img.shields.io/badge/Storage-TimescaleDB_%2B_Redis-8E24AA?style=for-the-badge">

<br><br>

*Bringing low-cost wearable sensing, institutional triage, and cloud analytics into a single academic health-monitoring platform.*

</div>

---

# What is VitalSense-Next?

**VitalSense-Next** is an academic final-year project focused on real-time health surveillance for institutional environments such as colleges, hostels, care homes, and campus clinics.

The system combines a wearable edge device built around the **Arduino Uno R4 WiFi** with a **MAX30102** pulse oximeter and heart-rate sensor, a **DS18B20** digital temperature sensor, and an **MPU-6050** motion sensor to monitor heart rate, oxygen saturation, body temperature, and fall-risk behavior. Sensor data is processed locally on the embedded platform and transmitted through Wi-Fi to a **FastAPI** backend for validation, persistence, and alerting. A **React.js** dashboard presents live vitals and risk summaries to staff, wardens, and medical personnel.

> This project is intended as a functional academic prototype and research demonstrator. It is not a certified medical device and should not be used as a replacement for professional clinical monitoring.

---

# Vision

> **To create an affordable, transparent, and extensible institutional health-monitoring system that connects embedded biosensing, cloud analytics, and operational dashboards for early intervention.**

---

# Key Features

<table>
<tr>
<td width="50%" valign="top">

## Wearable Sensing

- Heart-rate and SpO2 monitoring via MAX30102
- Temperature surveillance using DS18B20
- Motion and fall detection using MPU-6050
- Real-time signal filtering and threshold logic
- Local firmware alert generation
- Wi-Fi telemetry transmission from Arduino Uno R4 WiFi

</td>

<td width="50%" valign="top">

## Embedded Intelligence

- Real-time Arduino loop scheduling
- PPG signal smoothing and baseline correction
- Peak detection for pulse-rate estimation
- Risk classification based on threshold conditions
- Local buffering during network interruptions
- Telemetry packet serialization for backend ingestion

</td>
</tr>
</table>

---

<table>
<tr>
<td width="50%" valign="top">

## Monitoring Dashboard

- React.js live vitals dashboard
- Patient list and ward-level triage view
- Historical trends for HR, SpO2, and temperature
- Alert cards with severity-based coloring
- Role-aware access for staff and administrators
- Device health indicators and connectivity status

</td>

<td width="50%" valign="top">

## Backend and Analytics

- FastAPI ingestion service
- Request validation and role-based security
- TimescaleDB time-series data store
- Redis live cache and session state
- Alert generation and escalation logic
- REST and WebSocket interfaces for live updates

</td>
</tr>
</table>

---

# System Architecture

```text
                VITALSENSE-NEXT

       ┌───────────────────────────────┐
       │ React.js Monitoring Dashboard │
       │ Staff / Warden / Medical UI   │
       └───────────────▲───────────────┘
                       │ HTTPS / WebSocket
                       │
       ┌───────────────┴───────────────┐
       │ FastAPI Backend               │
       │ Validation + Auth + Alerts     │
       └───────────────▲───────────────┘
                       │
       ┌───────────────┴───────────────┐
       │ Redis + TimescaleDB           │
       │ Live Cache + Time-Series Data  │
       └───────────────▲───────────────┘
                       │
       ┌───────────────┴───────────────┐
       │ Arduino Uno R4 WiFi           │
       │ MAX30102 + DS18B20 + MPU-6050 │
       │ Firmware DSP + Telemetry      │
       └───────────────────────────────┘
```

---

# Repository Structure

```text
vitalsense-next/
├── .github/
│   └── workflows/
│       ├── ci-firmware.yml               # Automated Arduino CLI / PlatformIO builds
│       ├── ci-backend.yml                # Pytest, flake8, and FastAPI container builds
│       ├── ci-web.yml                    # React.js build and ESLint checks
│       └── ci-mobile.yml                 # React Native test and verification checks
│
├── apps/
│   │
│   ├── firmware/                         # Arduino UNO R4 WiFi Embedded System
│   │   ├── platformio.ini                # Target: uno_r4_wifi (Renesas RA4M1 + ESP32-S3)
│   │   ├── include/
│   │   │   ├── config.h                  # 14-bit ADC config, sampling rates (250Hz), I2C pins
│   │   │   ├── ble_service.h             # Custom BLE GATT service & characteristic UUIDs
│   │   │   ├── wifi_mqtt.h               # ESP32-S3 Wi-Fi connection and MQTT telemetry
│   │   │   ├── dsp_filter.h              # 50Hz notch, 0.5-4Hz bandpass, moving average math
│   │   │   ├── fall_detector.h           # MPU-6050 Vector Magnitude Unit (VMU) fall logic
│   │   │   └── sensors.h                 # MAX30102 (PPG/SpO2) and MLX90614 (Temp) drivers
│   │   ├── src/
│   │   │   ├── main.cpp                  # Setup, hardware timers, and super-loop scheduler
│   │   │   ├── ble_service.cpp           # BLE advertisement, notify handlers, and GATT events
│   │   │   ├── wifi_mqtt.cpp             # MQTT pub/sub loop with automatic reconnect logic
│   │   │   ├── dsp_filter.cpp            # IIR digital filters, baseline DC tracking, peak detect
│   │   │   ├── fall_detector.cpp         # IMU shock, drop, and stillness phase transitions
│   │   │   └── sensors.cpp               # I2C read sequences and sensor health validation
│   │   └── test/
│   │       └── test_dsp/                 # Unit tests for filter stability and peak accuracy
│   │
│   ├── backend/                          # FastAPI Telemetry & Clinical Engine (Python 3.11+)
│   │   ├── Dockerfile
│   │   ├── requirements.txt              # fastapi, uvicorn, pydantic, sqlalchemy, asyncpg, redis, paho-mqtt
│   │   ├── pyproject.toml
│   │   ├── alembic/
│   │   │   ├── env.py
│   │   │   └── versions/
│   │   └── app/
│   │       ├── main.py                   # FastAPI app factory, CORS, and lifespan events
│   │       ├── core/
│   │       │   ├── config.py             # App settings (Pydantic BaseSettings), secrets, DB URLs
│   │       │   ├── security.py           # Password hashing, JWT token creation and verification
│   │       │   └── database.py           # Async SQLAlchemy engine and session factory
│   │       ├── models/
│   │       │   ├── user.py               # User, Doctor, Warden, and Patient auth profiles
│   │       │   ├── device.py             # Registered Arduino UNO R4 hardware IDs and status
│   │       │   ├── telemetry.py          # TimescaleDB hypertable model for vital logs
│   │       │   └── alert.py              # Incident and emergency alert event logs
│   │       ├── schemas/
│   │       │   ├── telemetry.py          # VitalsStreamIn, VitalsMetricsOut, WaveformPacket
│   │       │   ├── alert.py              # AlertCreate, AlertAcknowledge, SOSPayload
│   │       │   ├── device.py             # DeviceRegister, DeviceHeartbeat
│   │       │   └── user.py               # UserLogin, UserRegister, TokenResponse
│   │       ├── api/
│   │       │   ├── v1/
│   │       │   │   ├── auth.py           # Login, logout, refresh token endpoints
│   │       │   │   ├── patients.py       # Patient roster, bed assignment, medical history
│   │       │   │   ├── vitals.py         # Historical time-series queries and aggregations
│   │       │   │   ├── devices.py        # Hardware registration, telemetry status, ping
│   │       │   │   └── alerts.py         # Active alert resolution and emergency escalation
│   │       │   └── websockets/
│   │       │       └── telemetry_ws.py   # High-speed WebSocket stream to web dashboard & mobile
│   │       ├── services/
│   │       │   ├── mqtt_service.py       # Async Paho-MQTT subscriber consuming Uno R4 packets
│   │       │   ├── redis_service.py      # Session cache, live state, and alert debounce locks
│   │       │   ├── ews_engine.py         # Early Warning Score (EWS) anomaly evaluation
│   │       │   └── fcm_service.py        # Firebase Cloud Messaging push alert dispatcher
│   │       └── tests/
│   │           ├── conftest.py
│   │           ├── test_api_vitals.py
│   │           └── test_ews_engine.py
│   │
│   ├── web/                              # Clinical Management Dashboard (React.js + Vite)
│   │   ├── Dockerfile
│   │   ├── package.json
│   │   ├── vite.config.ts
│   │   ├── tailwind.config.js
│   │   ├── tsconfig.json
│   │   ├── index.html
│   │   └── src/
│   │       ├── main.tsx                  # Application entry point with React Router
│   │       ├── App.tsx                   # Main layout and global context providers
│   │       ├── assets/
│   │       ├── api/
│   │       │   ├── auth.ts
│   │       │   ├── patients.ts
│   │       │   └── telemetry.ts
│   │       ├── components/
│   │       │   ├── common/
│   │       │   ├── triage/
│   │       │   ├── charts/
│   │       │   └── alerts/
│   │       ├── hooks/
│   │       │   ├── useWebSocket.ts
│   │       │   ├── useAuth.ts
│   │       │   └── useSoundAlert.ts
│   │       ├── pages/
│   │       │   ├── LoginPage.tsx
│   │       │   ├── DashboardPage.tsx
│   │       │   ├── PatientDetailPage.tsx
│   │       │   ├── AlertHistoryPage.tsx
│   │       │   └── DeviceManagerPage.tsx
│   │       └── utils/
│   │           ├── ewsCalculator.ts
│   │           └── formatters.ts
│   │
│   └── mobile/                           # Cross-Platform Patient App (React Native / Expo)
│       ├── app.json
│       ├── package.json
│       ├── tsconfig.json
│       └── src/
│           ├── App.tsx
│           ├── navigation/
│           ├── screens/
│           │   ├── HomeScreen.tsx
│           │   ├── WaveformScreen.tsx
│           │   ├── SosScreen.tsx
│           │   └── PairingScreen.tsx
│           ├── services/
│           │   ├── bleService.ts
│           │   ├── offlineStorage.ts
│           │   └── locationService.ts
│           ├── hooks/
│           │   ├── useBLEConnection.ts
│           │   └── useVitalsSync.ts
│           └── theme/
│
├── docs/                                 # Institutional & Engineering Documentation
│   ├── PRD.md                            # Product Requirements Document
│   ├── TRD.md                            # Technical Requirements Document
│   ├── SRS.md                            # Software Requirements Specification (IEEE 830 standard)
│   ├── System_Architecture.md            # Hardware-to-Cloud telemetry pipeline diagrams
│   ├── Hardware_Design.md                # Arduino UNO R4 pinout, ADC configs, sensor schematics
│   ├── DSP_and_Firmware_Spec.md          # RA4M1 timer math, digital filters, fall-detection logic
│   ├── API_Spec.md                       # FastAPI OpenAPI schemas, WebSocket & MQTT contracts
│   ├── BLE_GATT_Profile.md               # Custom BLE UUIDs, descriptors, and byte schemas
│   ├── AppFlow_and_State_Machine.md      # Navigation graphs and device state transitions
│   ├── Design_and_Wireframe_Spec.md      # React & React Native UI layout specifications
│   └── Test_Plan_and_Validation.md       # Pytest suites, firmware test benches, latency reports
│
├── docker-compose.yml                    # Local multi-service development orchestrator
│                                         # (FastAPI backend, TimescaleDB, Redis, Mosquitto MQTT)
├── .gitignore
├── proposal.md                           # NSHM Annexure-II proposal document
├── LICENSE
├── .editorconfig
└── README.md                             # Root project readme
```

---

# Project Components

<table>
<tr>
<td align="center" width="25%">

## Hardware

Arduino Uno R4 WiFi

MAX30102

DS18B20

MPU-6050

</td>

<td align="center" width="25%">

## Firmware

Sensor polling

DSP filtering

Threshold logic

Wi-Fi telemetry

</td>

<td align="center" width="25%">

## Backend

FastAPI services

TimescaleDB storage

Redis cache

Alert engine

</td>

<td align="center" width="25%">

## Dashboard

React.js UI

Live triage panel

Historical charts

Staff alert handling

</td>
</tr>
</table>

---

# Documentation

Start with the project documentation set to understand the architecture, requirements, and academic submission package.

- [Project proposal](proposal.md)
- [Product requirements](docs/PRD.md)
- [Technical requirements](docs/TRD.md)
- [Software requirements](docs/SRS.md)
- [System architecture](docs/System_Architecture.md)
- [Hardware design](docs/Hardware_Design.md)
- [Firmware and DSP specification](docs/DSP_and_Firmware_Spec.md)
- [API specification](docs/API_Spec.md)
- [Application flow and state machine](docs/AppFlow_and_State_Machine.md)
- [Design and wireframes](docs/Design_and_Wireframe_Spec.md)
- [Test plan and validation](docs/Test_Plan_and_Validation.md)

---

# Quick Project Summary

This project addresses the gap between expensive clinical monitoring systems and limited consumer-grade wearable devices by offering a practical, low-cost, open monitoring framework for institutional use. It emphasizes:

- real-time biosignal monitoring;
- early warning for abnormal vitals;
- motion-aware safety and fall detection;
- cloud analytics and secure monitoring workflows;
- academic feasibility with production-oriented documentation.

---

# License and Academic Use

This repository is organized for academic evaluation, prototype development, and documentation-driven engineering work. The project is suitable for capstone demonstration, internal review, and research extension, while remaining aligned with the NSHM final-year project submission structure.

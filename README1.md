<div align="center">

# VitalSense-Next

### Edge-IoT Biosignal Telemetry and Cloud Health Surveillance

A Final-Year Academic Project for Institutional Health Monitoring

<br>

<img src="https://img.shields.io/badge/Project-Final_Year_Prototype-1565C0?style=for-the-badge">
<img src="https://img.shields.io/badge/Domain-IoT_Healthcare-00C853?style=for-the-badge">
<img src="https://img.shields.io/badge/Platform-Arduino_%2B_React_%2B_FastAPI-7B1FA2?style=for-the-badge">
<img src="https://img.shields.io/badge/Status-Submission_Ready-F9A825?style=for-the-badge">

<br>

<img src="https://img.shields.io/badge/Hardware-Arduino_UNO_R4_WiFi-00979D?style=for-the-badge&logo=arduino&logoColor=white">
<img src="https://img.shields.io/badge/Sensors-MAX30102_%2B_DS18B20_%2B_MPU-6050-455A64?style=for-the-badge">
<img src="https://img.shields.io/badge/Backend-FastAPI-009688?style=for-the-badge&logo=fastapi&logoColor=white">
<img src="https://img.shields.io/badge/Frontend-React.js-61DAFB?style=for-the-badge&logo=react&logoColor=black">
<img src="https://img.shields.io/badge/Storage-TimescaleDB_%2B_Redis-8E24AA?style=for-the-badge">

</div>

---

# Abstract

VitalSense-Next is an end-to-end health monitoring and early-warning system designed for institutional environments such as hostels, care homes, and campus clinics. The project integrates a wearable edge device built around the Arduino Uno R4 WiFi with MAX30102, DS18B20, and MPU-6050 sensors to monitor heart rate, pulse oxygen saturation, body temperature, and motion-based fall risk. Sensor data is locally processed and transmitted over Wi-Fi to a FastAPI backend for validation, storage, and alert generation. A React.js dashboard presents real-time vitals, historical trends, and risk summaries to medical staff, wardens, and administrators. The objective of the project is to provide an affordable, scalable, and practical solution for continuous health surveillance and timely intervention in constrained institutional settings.

---

# Project Overview

The growing need for continuous health surveillance in residential and institutional settings has highlighted the limitations of conventional monitoring systems. Existing clinical devices are often expensive, inflexible, and unsuitable for everyday use outside controlled hospital environments. VitalSense-Next addresses this gap by combining embedded sensing, real-time telemetry, and web-based monitoring in a low-cost academic prototype.

The system is structured into three primary layers:

1. Edge sensing and signal processing on the Arduino Uno R4 WiFi device.
2. Cloud ingestion, validation, alert generation, and persistence using FastAPI, TimescaleDB, and Redis.
3. Real-time healthcare dashboard and triage interface implemented using React.js.

This layered architecture supports early detection of abnormal health conditions, helps staff monitor vulnerable individuals, and provides a strong foundation for extension into a more advanced medical monitoring system.

---

# Objectives

The main objectives of the project are:

- to design and implement a low-cost wearable health monitoring system;
- to acquire real-time biometric data using Arduino-based embedded sensing;
- to process vital signals and identify abnormal conditions;
- to develop a secure backend for telemetry ingestion and data management;
- to create a web dashboard for clinical monitoring and triage;
- to support early alerting for abnormal vitals and fall events;
- to provide a practical academic prototype aligned with final-year project standards.

---

# Problem Statement

In institutional settings such as college hostels and elder-care facilities, health deterioration may remain unnoticed until it becomes severe. Delayed detection of abnormal heart rate, low oxygen saturation, fever conditions, or sudden fall events can increase risk and reduce response time. Existing wearable and medical monitoring systems are often costly, proprietary, or not suitable for widespread deployment in academic or community-based settings.

VitalSense-Next aims to resolve this by providing a cost-effective, open, and extensible monitoring framework that combines wearable sensing with cloud analytics and operational dashboards.

---

# Scope of the Project

The project focuses on building a functional prototype for remote health monitoring and early-warning generation. It includes:

- Arduino-based wearable sensing using MAX30102, DS18B20, and MPU-6050;
- signal processing and threshold logic on the embedded device;
- cloud ingestion and monitoring through FastAPI;
- time-series storage in TimescaleDB;
- Redis-based caching for live monitoring and session state;
- React.js dashboard for clinical visualization and alert management;
- system validation for normal operation, alert scenarios, and offline recovery behavior.

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

# Technologies Used

## Embedded Hardware
- Arduino Uno R4 WiFi
- MAX30102 for heart-rate and pulse oximetry
- DS18B20 for temperature sensing
- MPU-6050 for motion and fall detection

## Software Stack
- React.js for the web dashboard
- FastAPI for backend services and APIs
- TimescaleDB for time-series data storage
- Redis for caching and live state management

## Development Considerations
- low-power edge monitoring
- secure API communication
- real-time alerting and triage interface
- event-driven architecture for monitoring workflows

---

# Key Features

## Wearable Sensing
- continuous heart-rate monitoring;
- oxygen saturation estimation;
- temperature tracking;
- motion analysis for fall detection;
- live signal filtering and preliminary classification.

## Dashboard and Monitoring
- live vitals overview;
- patient-level monitoring;
- alert panels with severity levels;
- historical data visualization;
- role-based institutional access control.

## Backend Capabilities
- telemetry ingestion from edge devices;
- validation and schema enforcement;
- secure user and role management;
- time-series persistence and trend computation;
- alert generation and acknowledgement workflow.

---

# Expected Outcomes

The project is expected to deliver:

- a functioning wearable biosignal prototype;
- a real-time telemetry pipeline from embedded hardware to cloud services;
- a responsive dashboard for operational monitoring;
- a structured alerting mechanism for abnormal health conditions;
- a complete academic documentation package for evaluation and submission.

---

# Applications

The proposed system can be applied in:

- hostel health monitoring;
- elderly care and assisted living;
- workforce wellness monitoring;
- campus clinic observation work;
- low-cost prototype research in wearable IoT healthcare.

---

# Project Significance

VitalSense-Next is significant because it combines practical embedded hardware, cloud-based monitoring, and real-time analytics into a single educational framework. It contributes to the growing field of wearable health technology while remaining feasible for academic implementation within the scope of a final-year project. The system demonstrates how low-cost sensing and open software platforms can be used to design meaningful health intervention systems.

---

# Repository Structure

```text
VitalSense-Next/
├── .github/
│   └── workflows/
├── apps/
│   ├── firmware/
│   ├── backend/
│   ├── web/
│   └── mobile/
├── docs/
├── README.md
├── README1.md
├── proposal.md
├── Annexure_II_Proposal.md
├── IDEA.txt
├── MASTER.txt
├── PRD.md
├── TRD.md
├── SRS.md
├── System_Architecture.md
├── Hardware_Design.md
├── DSP_and_Firmware_Spec.md
├── API_Spec.md
├── AppFlow_and_State_Machine.md
├── Design_and_Wireframe_Spec.md
├── Test_Plan_and_Validation.md
├── DIRECTORY_STR.txt
├── docker-compose.yml
├── .gitignore
└── LICENSE
```

---

# Documentation

The project documentation is organized to support both academic review and technical implementation.

- [Project Proposal](proposal.md)
- [Product Requirements Document](docs/PRD.md)
- [Technical Requirements Document](docs/TRD.md)
- [Software Requirements Specification](docs/SRS.md)
- [System Architecture](docs/System_Architecture.md)
- [Hardware Design](docs/Hardware_Design.md)
- [DSP and Firmware Specification](docs/DSP_and_Firmware_Spec.md)
- [API Specification](docs/API_Spec.md)
- [Application Flow and State Machine](docs/AppFlow_and_State_Machine.md)
- [Design and Wireframe Specification](docs/Design_and_Wireframe_Spec.md)
- [Test Plan and Validation](docs/Test_Plan_and_Validation.md)

---

# Conclusion

VitalSense-Next presents a realistic and impactful solution for wearable health monitoring in institutional contexts. By combining embedded sensing, cloud-based analytics, and a responsive dashboard, the project demonstrates a complete and practical system architecture suitable for academic evaluation and future research expansion. The project balances technical feasibility, affordability, and utility, making it a strong final-year engineering initiative.

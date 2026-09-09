# VitalSense-Next: Software Requirements Specification (SRS)

## 1. Document Control

- Project: VitalSense-Next
- Type: Software Requirements Specification
- Status: Draft baseline for implementation
- Version: 1.0
- Date: 2026-09-09

---

## 2. Introduction

### 2.1 Purpose
The purpose of this Software Requirements Specification (SRS) is to define the functional and non-functional requirements of the VitalSense-Next system. The system is an institutional health monitoring platform that collects vital signs from a wearable Arduino Uno R4 WiFi node, transmits telemetry through Wi-Fi to a FastAPI backend, and presents monitored patient data through a React.js dashboard.

### 2.2 Scope
The system shall support:
- biometric sensing via MAX30102, DS18B20, and MPU-6050;
- onboard filtering and threshold logic on the Arduino Uno R4 WiFi;
- Wi-Fi transport to a FastAPI backend;
- time-series persistence in TimescaleDB;
- Redis-based session and alert caching;
- clinician and staff monitoring through a React.js dashboard;
- alert generation and escalation for abnormal vitals and fall events.

### 2.3 Product Perspective
VitalSense-Next is a cyber-physical health monitoring platform intended for use in institutional settings such as hostels, care homes, and campus monitoring programs. It is not a certified medical device, but it is engineered as a practical monitoring and early-warning system for health-oriented decision support.

---

## 3. Overall Description

### 3.1 Product Functions
The software shall:
1. read and validate sensor streams from wearable devices;
2. compute heart rate, SpO2, temperature, and motion status;
3. detect abnormal threshold conditions and potential falls;
4. transmit telemetry to backend services;
5. store readings in TimescaleDB;
6. provide role-based dashboards for monitoring; and
7. manage alert acknowledgment and escalation workflows.

### 3.2 User Classes
- Patient / resident
- Caregiver / guardian
- Warden / institutional staff
- Medical staff / clinician
- System administrator
- Developer / academic maintainer

### 3.3 Operating Environment
- Edge device: Arduino Uno R4 WiFi
- Backend: FastAPI running on Linux-based server
- Database: TimescaleDB
- Cache: Redis
- Frontend: React.js + Tailwind CSS
- Network: institutional Wi-Fi or internet-connected gateway

### 3.4 Constraints
- Arduino Uno R4 WiFi memory is limited to 256 KB SRAM and 1 MB flash.
- Sensor data quality depends on motion conditions and calibration.
- Wi-Fi reliability must be treated as a resilience constraint.
- The system must be privacy-conscious and role-aware.

---

## 4. Specific Requirements

### 4.1 Functional Requirements

#### FREQ-01: Device Initialization
The system shall initialize sensors and network modules during startup.

Requirements:
- MAX30102, DS18B20, and MPU-6050 must be detected and configured.
- Serial debug and Wi-Fi module shall initialize successfully before telemetry starts.
- If initialization fails, the system shall enter a degraded-safe mode and raise a device-health warning.

#### FREQ-02: Sensor Acquisition
The system shall collect sensor data from the wearable node.

Requirements:
- MAX30102 shall sample PPG data at approximately 100 Hz.
- DS18B20 shall sample temperature at around 1 Hz.
- MPU-6050 shall sample accelerometer and gyro data at 50-100 Hz.
- Each measurement packet shall include a timestamp and a device identifier.

#### FREQ-03: Signal Processing
The system shall process incoming sensor data to estimate health parameters.

Requirements:
- remove baseline or DC offset from PPG values;
- smooth signal estimates with moving averages;
- estimate heart rate from inter-beat intervals;
- compute oxygen estimation using red/IR ratio assumptions;
- detect sudden acceleration and inactivity states for fall detection.

#### FREQ-04: Alert Evaluation
The system shall evaluate thresholds for alert generation.

Example thresholds:
- HR warning >110 BPM or <50 BPM
- HR critical >130 BPM or <45 BPM
- SpO2 warning <94%
- SpO2 critical <90%
- temp warning >37.8°C
- temp critical >38.5°C

Requirements:
- an alert must be generated when threshold conditions persist for a defined validation window;
- fall detection must combine motion magnitude and inactivity confirmation;
- alert suppression must occur when signal quality is poor or drift is excessive.

#### FREQ-05: Telemetry Transmission
The system shall transmit telemetry from the edge device to the backend.

Requirements:
- the device shall serialize readings into JSON payloads;
- data shall be sent using Wi-Fi HTTP or WebSocket;
- the system shall support local buffering if the Wi-Fi network is temporary unavailable;
- the payload shall include deviceId, timestamp, and status metadata.

#### FREQ-06: Backend Ingestion
The FastAPI backend shall receive, validate, and store telemetry payloads.

Requirements:
- validate schema and timestamp integrity;
- reject malformed data with structured error codes;
- store valid readings into TimescaleDB;
- update Redis cache for state and alert tracking.

#### FREQ-07: Monitoring Dashboard
The frontend shall display monitoring information and system alerts.

Requirements:
- show current patient status and vitals;
- show trend graphs for HR, SpO2, and temperature;
- surface alert severity with color-coded status badges;
- allow staff to acknowledge or dismiss an alert after review.

#### FREQ-08: Role-Based Access
The system shall support user roles and permissions.

Requirements:
- admin can manage devices and users;
- medical staff can review vitals and incidents;
- wardens can view risk summaries and emergency states;
- guardians can access limited or assigned data views;
- unauthorized users must be denied access to patient-level telemetry.

#### FREQ-09: Audit Logging
The system shall log key actions.

Requirements:
- log authentication events;
- log alert generation and acknowledgment;
- log report exports and role changes;
- preserve timestamps and actor identities.

### 4.2 Non-Functional Requirements

#### NFR-01: Reliability
- the system must handle brief Wi-Fi outages without losing recent critical measurements;
- telemetry must be retried automatically when connection is restored;
- stale sensor data must be flagged to prevent silent failure.

#### NFR-02: Performance
- alert generation shall occur within a few seconds of threshold crossing;
- dashboard refresh shall occur within 1 second under normal conditions;
- sensor acquisition must not block core communication tasks.

#### NFR-03: Security
- all backend traffic shall use HTTPS/TLS 1.3;
- JWT-based authentication shall be enforced;
- tokens shall rotate securely and expire appropriately;
- all critical endpoints shall require authorization.

#### NFR-04: Scalability
- the architecture must support multiple active devices in a single facility;
- time-series data must remain queryable across hours and days;
- backend services shall reasonably handle burst ingestion and alert events.

#### NFR-05: Maintainability
- firmware logic must be modularized by sensor, filter, and threshold functions;
- API contracts shall be versioned;
- frontend and backend code shall be independently deployable.

---

## 5. Interface Requirements

### 5.1 Hardware Interface
- Arduino Uno R4 WiFi sensors connect using I2C and 1-Wire buses.
- I2C bus for MAX30102 and MPU-6050 on A4/A5.
- DS18B20 on D2 with 4.7 kΩ pull-up.

### 5.2 Software Interface
- Frontend consumes FastAPI JSON responses.
- Backend stores time-series data in TimescaleDB.
- Redis stores current device state and alert queue references.

### 5.3 User Interfaces
- React.js monitoring dashboard with status cards and live charts
- alert detail drawer for status review
- device health panel with battery and connectivity indicators

---

## 6. Assumptions and Dependencies

### 6.1 Assumptions
- users have access to a compatible internet-enabled device or browser;
- institutional Wi-Fi is present at deployment locations;
- a supervised user will review alerts and escalate to appropriate responders;
- the project is for early-warning monitoring rather than autonomous clinical diagnosis.

### 6.2 Dependencies
- Arduino libraries for MAX30102, DS18B20, Wi-Fi, and MPU-6050
- FastAPI runtime and authentication stack
- Redis service
- TimescaleDB service
- frontend charting and state-management frameworks

---

## 7. Requirements Traceability Matrix

| ID | Requirement | Related Component |
| --- | --- | --- |
| SRS-01 | Sensor initialization | Firmware |
| SRS-02 | HR and SpO2 acquisition | MAX30102 + firmware |
| SRS-03 | Temperature acquisition | DS18B20 + firmware |
| SRS-04 | Motion and fall acquisition | MPU-6050 + firmware |
| SRS-05 | Data validation | FastAPI |
| SRS-06 | Alerting | Backend + dashboard |
| SRS-07 | Triage monitoring | React.js dashboard |
| SRS-08 | RBAC | FastAPI security |
| SRS-09 | Audit logging | Backend |
| SRS-10 | Resilience | Edge buffering + retry logic |

---

## 8. Acceptance Criteria

The system shall be accepted as satisfying this SRS when:
1. the Arduino node can read all three sensors and transmit telemetry;
2. the backend successfully ingests and persists readings;
3. a threshold alert is generated and visible in the dashboard;
4. a staff user can acknowledge and review the alert;
5. a brief Wi-Fi outage does not permanently lose recent telemetry;
6. unauthorized access is denied according to RBAC rules.

---

## 9. Conclusion

This SRS defines a complete and implementation-ready baseline for the VitalSense-Next project. It aligns the embedded sensing system, cloud ingestion, and monitoring dashboard into a single operational workflow while keeping the design practical, secure, and academically sound for the final-year project.

# Final Year Project Report

## VitalSense-Next: Edge-IoT Biosignal Telemetry and Cloud Health Surveillance

### Department of Computer Science and Engineering
### NSHM Institute of Engineering & Technology

---

## 1. Introduction

Healthcare monitoring in institutional environments is a critical requirement, especially in settings such as hostels, elder-care homes, and campus clinics where continuous observation may not always be feasible. Many existing clinical monitoring systems are expensive, heavy, and designed for hospital settings, making them unsuitable for everyday institutional use. There is therefore a strong need for a low-cost, scalable, and effective solution that can monitor health conditions in real time and alert staff when abnormalities occur.

This project presents VitalSense-Next, a wearable health monitoring system designed to detect abnormal physiological signals and generate early warnings. The system integrates embedded sensing, edge processing, cloud storage, and a web-based monitoring dashboard. It combines the Arduino Uno R4 WiFi with MAX30102, DS18B20, and MPU-6050 sensors to monitor heart rate, body temperature, oxygen saturation, and motion-related activity. The collected data is transmitted to a FastAPI backend, stored in TimescaleDB, and displayed via a React.js dashboard for monitoring and decision support.

The purpose of this project is to design and implement a practical, affordable, and extendable health-monitoring platform suitable for academic evaluation and institutional deployment. It contributes toward the broader field of IoT-enabled healthcare by aligning embedded hardware, data processing, and cloud analytics into a unified real-time surveillance system.

---

## 2. Problem Definition

Traditional healthcare monitoring relies heavily on manual observation and expensive clinical infrastructure. In residential institutions, patients and residents may remain unattended during vulnerable periods, especially during late-night hours or low-staff periods. Early signs of physiological deterioration, such as abnormal heart rate, low oxygen saturation, fever, or sudden falls, may not be detected promptly.

This delay can lead to severe consequences, including emergency situations and delayed intervention. Existing commercial monitoring systems often depend on proprietary ecosystems, limiting transparency, flexibility, and accessibility. Moreover, these systems are not designed for low-cost institutional deployment.

The project addresses this challenge by creating an accessible end-to-end monitoring platform that provides real-time measurement, cloud-based processing, and staff-facing alerting without requiring a prohibitively expensive infrastructure.

---

## 3. Objectives

The objectives of the project are as follows:

1. To develop a low-cost wearable health monitoring system using embedded hardware.
2. To integrate sensors for physiological and motion monitoring.
3. To process sensor signals on the device and detect abnormal conditions.
4. To transmit data securely to a backend service using Wi-Fi.
5. To design a cloud-based system for validation, storage, and alert generation.
6. To provide a web dashboard for monitoring health trends and identifying critical conditions.
7. To evaluate the system using realistic test scenarios and confirm basic reliability.

---

## 4. Motivation

The motivation for this project arises from a growing need for intelligent, low-cost medical monitoring systems that can complement hospital-based care. In many settings, especially educational hostels and care facilities, a patient may be physically present but clinically unattended. A system that continuously monitors vital health indicators can provide staff with valuable information before a condition becomes critical.

The availability of affordable hardware and open-source software tools makes it feasible to prototype such a system in an academic environment. This project uses that opportunity to design a practical health monitoring platform that demonstrates real engineering value while remaining feasible for a final-year undergraduate project.

---

## 5. Scope of the Work

The project focuses on building a prototype health-monitoring system with the following features:

- wearable sensor acquisition using Arduino Uno R4 WiFi;
- heart rate and pulse oximetry estimation using MAX30102;
- body temperature monitoring using DS18B20;
- motion monitoring and fall detection using MPU-6050;
- signal processing and threshold-based alert generation;
- data transmission to a FastAPI backend;
- storage and query of telemetry in TimescaleDB;
- Redis-based caching and session state management;
- React.js dashboard for live monitoring and alert overview;
- comprehensive project documentation and testing.

The work is intended to serve as a practical and educational prototype rather than a clinically certified medical device.

---

## 6. Literature Review

Wearable health monitoring systems have become increasingly relevant due to the emergence of low-cost embedded devices and health-tech innovations. Systems based on pulse oximeters, motion sensors, and thermal sensing have shown the potential to monitor well-being continuously, particularly for chronic care and early warning applications.

However, many existing solutions are commercially proprietary and difficult to adapt for custom academic experimentation. In addition, hospital-grade systems are expensive and heavy, and are not designed for community or institutional deployments. There is a clear research gap for cost-effective, open, and transparent health-surveillance systems that provide real-time monitoring without requiring specialized medical infrastructure.

This project addresses that gap by combining embedded sensing, low-cost hardware, and modern web technologies. It follows the principle that health monitoring should be practical, affordable, and extensible while still providing meaningful insights to caregivers and staff.

---

## 7. System Architecture

The architecture of the system is designed in layers to separate hardware acquisition, cloud processing, and user interaction.

### 7.1 Edge Layer
The edge layer consists of an Arduino Uno R4 WiFi microcontroller connected to the MAX30102, DS18B20, and MPU-6050 sensors. These sensors collect physiological and motion data, while the embedded firmware performs local processing, filtering, and alert evaluation before sending data to the backend.

### 7.2 Communication Layer
The device transmits telemetry over Wi-Fi to the FastAPI backend. The communication process includes structured JSON payloads, timestamps, and device identifiers. The system is designed to handle temporary network interruptions by buffering readings and retrying transmission when the connection is restored.

### 7.3 Backend Layer
The backend is implemented using FastAPI and includes API endpoints for telemetry ingestion, device registration, alert management, and data retrieval. It validates incoming data, stores readings in TimescaleDB, and maintains real-time state in Redis for fast access.

### 7.4 Dashboard Layer
The front end is implemented with React.js and provides a monitoring dashboard for healthcare staff. It supports live vitals display, trend charts, alert visibility, and role-based monitoring views. The interface is designed to help staff quickly assess the condition of multiple individuals and identify urgent cases.

---

## 8. Hardware Design

The project uses the Arduino Uno R4 WiFi as the primary controller for the wearable node. This board was selected because it supports onboard Wi-Fi connectivity, has sufficient GPIO resources for sensor interfacing, and is practical for academic prototyping.

### 8.1 Sensor Configuration
- MAX30102: used for pulse oximetry and heart-rate estimation.
- DS18B20: used for temperature sensing.
- MPU-6050: used for motion and fall detection.

### 8.2 Communication Design
The sensors communicate through I2C and 1-Wire protocols. The MAX30102 and MPU-6050 share the I2C bus, while the DS18B20 is connected using a 1-Wire line with a pull-up resistor. This allows the microcontroller to read multiple physiological signals while maintaining a compact and efficient design.

### 8.3 Power and Embedded Constraints
The wearable design is optimized for portable use and needs to operate under low-power constraints. The design includes a battery-powered architecture, with sensor polling and processing kept efficient to preserve runtime. The Arduino Uno R4 WiFi has limited memory, requiring careful code optimization and compact data structures.

---

## 9. Firmware and Signal Processing

The firmware is responsible for reading sensor data, applying digital signal processing, and deciding whether an alert should be raised. The system uses filtering operations to smooth the signal and reduce noise. The MAX30102 reading is processed to estimate pulse waveform characteristics and compute heart rate and pulse oxygen level. The temperature sensor is sampled at regular intervals, and the MPU-6050 provides acceleration and angular velocity data used for motion analysis.

The firmware applies threshold logic to detect abnormal conditions such as tachycardia, hypoxemia, fever, or fall-like motion. Alert events are generated when conditions persist beyond a selected validation window, reducing false positive signals caused by temporary noise or motion artifacts.

---

## 10. Backend and Data Management

The backend is implemented using FastAPI, which provides a lightweight yet robust API layer. Its role is to accept telemetry from devices, validate the payload schema, and store records in a time-series database. TimescaleDB is used to handle large volumes of timestamped health data efficiently, while Redis provides support for live state caching and real-time operational information.

The backend also manages authentication, user access, and alert workflows. This makes the architecture suitable for institutional monitoring, where multiple roles may need different views and permissions. The backend can expose endpoints for patient summaries, device status, historical vitals, and alert acknowledgement actions.

---

## 11. Frontend Dashboard

The React.js dashboard is intended for staff and institutional users. It shows live vitals and persistent status summaries, allowing users to observe the health status of monitored individuals. It includes charts and alert cards that make critical conditions obvious at a glance. The dashboard is designed to support triage-related tasks by prioritizing higher-risk users and presenting recent trends in a clear manner.

The interface can be extended with role-based access for different user categories such as medical staff, wardens, and administrators. This ensures that the system supports operational monitoring without exposing sensitive information unnecessarily.

---

## 12. Testing and Validation

A structured validation plan was designed to evaluate the complete system. The testing strategy includes unit testing of firmware algorithms, validation of API endpoints, dashboard usability checks, and end-to-end monitoring tests.

The following validation aspects were considered:

- sensor initialization and data acquisition;
- signal processing stability and threshold accuracy;
- telemetry transmission from device to backend;
- database storage and retrieval;
- alert generation and acknowledgement workflow;
- offline recovery behavior during network interruption;
- role-based access validation.

The system is expected to pass validation when it reliably demonstrates accurate sensing, correct backend ingestion, dashboard updates, and meaningful alert behavior under real operating conditions.

---

## 13. Challenges and Limitations

Several challenges were encountered during the design and development process. The primary challenge was balancing performance with hardware constraints, especially given the limited memory and processing capability of the Arduino Uno R4 WiFi. Signal quality was also affected by motion artifacts and sensor noise, which required logical filtering and threshold-based validation strategies.

Another challenge was ensuring a clear and practical user experience for the monitoring dashboard. Since the system is meant for operational use, a simple but effective visualization method was preferred over complex or visually noisy interfaces.

The project is also limited by the fact that it is an academic prototype and not a certified clinical device. It should be viewed as a practical demonstrator for health monitoring and alerting rather than a replacement for professional medical equipment.

---

## 14. Future Enhancements

The current project lays down a strong foundation for future work. Potential improvements include:

- improved predictive analytics for health risk scoring;
- integration of notification services such as SMS or mobile push alerts;
- expansion of support for additional sensors and biomarkers;
- enhancement of dashboard analytics and historical reporting;
- stronger security and deployment hardening for production environments;
- integration with a real institutional workflow or clinical environment.

These enhancements would further improve the usability and reliability of the platform as a research and deployment-ready system.

---

## 15. Conclusion

VitalSense-Next demonstrates the feasibility of designing an institutional health monitoring system using affordable embedded hardware, a modern backend, and a responsive dashboard. The project combines sensor-driven data acquisition, real-time processing, secure cloud services, and operational monitoring into a unified solution for early health intervention.

By integrating Arduino Uno R4 WiFi, MAX30102, DS18B20, MPU-6050, React.js, FastAPI, TimescaleDB, and Redis, the project provides a practical academic prototype that addresses the challenge of continuous health surveillance in resource-constrained environments. It offers a meaningful contribution to the domain of IoT-enabled healthcare and demonstrates the value of combining low-cost hardware with intelligent monitoring systems.

---

## 16. References

1. Arduino Uno R4 WiFi technical documentation.
2. MAX30102 biosensor specification and usage notes.
3. DS18B20 temperature sensor documentation.
4. MPU-6050 accelerometer and gyroscope reference design.
5. React.js frontend development documentation.
6. FastAPI framework documentation.
7. TimescaleDB time-series database documentation.
8. Redis in-memory data store documentation.

---

## 17. Declaration

This project report is submitted as part of the final-year project requirements for the Bachelor of Technology program in Computer Science and Engineering. The work presented here reflects the original effort, design decisions, and implementation details of the VitalSense-Next project.

# VitalSense-Next: Test Plan and Validation

## 1. Purpose

This document defines the validation strategy for the VitalSense-Next project. It covers embedded firmware verification, backend API testing, dashboard validation, and end-to-end monitoring workflows required to verify that the system operates as intended.

---

## 2. Test Objectives

The system shall be considered validated when it can:
- acquire reliable sensor values from Arduino Uno R4 WiFi;
- transmit data to the FastAPI backend;
- store and query telemetry in TimescaleDB;
- deliver live monitoring updates through the React.js dashboard;
- generate and resolve alerts based on threshold logic;
- handle offline and recoverable failure states gracefully.

---

## 3. Test Categories

### 3.1 Unit Testing
- sensor read functions
- filtering and DSP routines
- threshold logic
- alert evaluation rules
- API schema validation
- frontend component rendering logic

### 3.2 Integration Testing
- Arduino to backend telemetry flow
- backend to database persistence
- Redis caching and alert freshness
- dashboard fetching of telemetry and alerts

### 3.3 System Testing
- full health-monitoring workflow
- alert generation and acknowledgement journey
- degraded network behavior
- role-based access checks

### 3.4 Acceptance Testing
- clinical/deployment simulation with realistic threshold conditions
- staff review workflow in triage dashboard
- emergency escalation and reporting path

---

## 4. Test Environment

### 4.1 Embedded System Setup
- Arduino Uno R4 WiFi connected to sensors:
  - MAX30102
  - DS18B20
  - MPU-6050
- serial monitor for debug logs
- local or lab Wi-Fi environment

### 4.2 Backend Setup
- FastAPI service
- TimescaleDB instance
- Redis instance
- test client for API simulation

### 4.3 Frontend Setup
- React.js dashboard running locally or in a test environment
- browser automation for dashboard testing
- mock and real data scenarios for triage layout verification

---

## 5. Embedded Firmware Tests

### 5.1 Sensor Read Validation

Test ID: FW-01
- Objective: confirm all sensors initialize and read valid data
- Steps:
  1. power the Arduino node
  2. initialize MAX30102, DS18B20, and MPU-6050
  3. read sensor outputs over a 1-minute interval
- Expected result:
  - all three sensors respond without repeated timeout errors
  - signal values remain within plausible ranges

### 5.2 Heart Rate Validation

Test ID: FW-02
- Objective: verify heart-rate estimation is valid under stable conditions
- Steps:
  1. place sensor on a stable pulse source or human subject
  2. collect 30-60 seconds of sample data
  3. compare computed HR against a standard reference such as a pulse oximeter or smartwatch
- Expected result:
  - computed HR remains within acceptable tolerance of reference device

### 5.3 SpO2 Validation

Test ID: FW-03
- Objective: confirm oximetry estimation is stable and not noisy
- Steps:
  1. collect IR and red channel values
  2. use the firmware SpO2 approximation
  3. compare with a known pulse oximeter reading
- Expected result:
  - values are within a reasonable error band after calibration

### 5.4 Temperature Validation

Test ID: FW-04
- Objective: check DS18B20 reading stability
- Steps:
  1. expose to ambient room temperature
  2. compare with thermometer reference
- Expected result:
  - readings track within expected tolerance

### 5.5 Motion and Fall Detection Validation

Test ID: FW-05
- Objective: verify motion logic identifies rapid impact and inactivity periods
- Steps:
  1. perform controlled movement sequence
  2. record accelerometer magnitude
  3. trigger a fall-like event simulation
- Expected result:
  - event is flagged only when combined conditions match fall logic

### 5.6 Wi-Fi Transmission Validation

Test ID: FW-06
- Objective: confirm backend telemetry can be transmitted successfully
- Steps:
  1. start the device and backend concurrently
  2. send repeated telemetry conditions over network
- Expected result:
  - data reaches backend with correct timestamps and device IDs

---

## 6. Backend API Tests

### 6.1 Telemetry Ingestion

Test ID: API-01
- Objective: validate telemetry endpoint accepts valid payloads
- Steps:
  1. send sample valid JSON payload
  2. submit via HTTP POST
- Expected result:
  - status 200 or 202
  - data written to TimescaleDB

### 6.2 Invalid Payload Rejection

Test ID: API-02
- Objective: ensure malformed data is rejected cleanly
- Steps:
  1. send body missing timestamp or device identifier
  2. send malformed numeric types
- Expected result:
  - structured error response
  - no invalid data persisted

### 6.3 Alert Endpoint Validation

Test ID: API-03
- Objective: verify alert list and acknowledgement endpoints work
- Steps:
  1. generate a new alert via telemetry input
  2. read `/alerts`
  3. acknowledge the alert
- Expected result:
  - alert appears with status active
  - acknowledgement updates record properly

### 6.4 Authorization Validation

Test ID: API-04
- Objective: verify role-based restrictions
- Steps:
  1. login as admin, clinician, and guardian
  2. access restricted patient data
- Expected result:
  - authorized users succeed
  - unauthorized users are denied access

---

## 7. Dashboard and UX Validation

### 7.1 Overview Rendering

Test ID: UI-01
- Objective: confirm dashboard summary cards load correctly
- Steps:
  1. load the monitoring dashboard
  2. verify patient status groups
  3. confirm alert counters update
- Expected result:
  - overview loads without blank states or broken layout

### 7.2 Real-Time Data Refresh

Test ID: UI-02
- Objective: ensure monitored data updates in near real time
- Steps:
  1. send new telemetry from backend or simulated stream
  2. watch patient cards and charts refresh
- Expected result:
  - live metrics change without manual reload

### 7.3 Alert Review Workflow

Test ID: UI-03
- Objective: verify alert acknowledgment and resolution operations work from dashboard
- Steps:
  1. trigger a high-risk alert
  2. open alert detail panel
  3. acknowledge alert
- Expected result:
  - status changes from active to acknowledged
  - audit trail reflects the action

### 7.4 Accessibility Review

Test ID: UI-04
- Objective: validate readability and keyboard accessibility
- Steps:
  1. review contrast ratio and focus states
  2. navigate via keyboard only
- Expected result:
  - no inaccessible controls and critical actions are reachable

---

## 8. End-to-End System Validation

### 8.1 Full Monitoring Cycle

Test ID: E2E-01
- Objective: validate the complete edge-to-cloud-to-dashboard lifecycle
- Steps:
  1. turn on Arduino node
  2. collect readings
  3. send telemetry to backend
  4. confirm storage in TimescaleDB
  5. inspect dashboard status
- Expected result:
  - patient metrics appear and remain synchronized with backend data

### 8.2 Threshold Alert Cycle

Test ID: E2E-02
- Objective: verify alert generation from abnormal vitals
- Steps:
  1. simulate abnormal HR/SpO2/temperature values
  2. send payload to API
  3. view alert history and dashboard summary
- Expected result:
  - warning or critical alert appears with correct severity

### 8.3 Fault Recovery Cycle

Test ID: E2E-03
- Objective: validate reconnect and offline recovery
- Steps:
  1. disconnect Wi-Fi while sampling
  2. reconnect after a short delay
  3. check buffered data upload and dashboard status
- Expected result:
  - device shows degraded state during outage
  - data is eventually recovered and restored without loss of recent records

---

## 9. Acceptance Criteria

The project passes validation when all critical tests satisfy the following:
1. the Arduino node reads valid sensor values and transmits them reliably;
2. the FastAPI backend accepts and stores valid packets;
3. dashboard pages display current health/state information correctly;
4. alert generation and acknowledgment work end-to-end;
5. unauthorized users are denied access to protected patient data;
6. degraded network conditions do not permanently lose recent telemetry.

---

## 10. Risk-Based Test Prioritization

### Priority 1: Must Pass
- sensor acquisition correctness
- core telemetry upload path
- alert rules and severity logic
- dashboard status visibility
- multi-role access control

### Priority 2: Should Pass
- battery and sensor calibration behavior
- live chart responsiveness
- offline buffering reliability

### Priority 3: Nice to Have
- aesthetic polish and additional reporting features
- extended analytics or historical comparisons

---

## 11. Summary

The validation process is designed to verify both the technical correctness and the operational usefulness of VitalSense-Next. By testing the system from the edge sensor layer to the dashboard triage workflow, the project can confirm that it is practical, resilient, and aligned with the final architecture defined in the master documentation set.

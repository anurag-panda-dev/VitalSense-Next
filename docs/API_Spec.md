# VitalSense-Next: API Specification

## 1. Purpose

This document defines the API contract between the Arduino Uno R4 WiFi edge client, the FastAPI backend, and the React.js monitoring interface. The API is designed for secure ingestion, monitoring, and alert handling for health telemetry.

---

## 2. API Architecture

### 2.1 Components
- Edge device posts telemetry to FastAPI
- FastAPI validates and stores data in TimescaleDB
- Redis stores live session and alert state
- React.js dashboard consumes summaries and event streams

---

## 3. Authentication and Security

### 3.1 Authentication
- JWT-based authentication for staff, clinicians, and administrators
- secure refresh-token flow for long-lived sessions

### 3.2 Authorization
- admin: device and user management
- clinician: patient monitoring and records
- warden: summary-level status access
- guardian: assigned-patient scope only

### 3.3 Transport Security
- HTTPS/TLS 1.3 for all REST/WebSocket traffic
- secure handling of patient metadata and telemetry identifiers

---

## 4. Base URL

```text
https://api.vitalsense.local/api/v1
```

For local development:
```text
http://localhost:8000/api/v1
```

---

## 5. Telemetry Ingestion API

### 5.1 Endpoint
```http
POST /api/v1/telemetry
```

### 5.2 Description
Receives health telemetry from the Arduino device.

### 5.3 Request Body
```json
{
  "deviceId": "VSN-UnoR4-001",
  "patientId": "P-1003",
  "timestamp": 1725879123456,
  "type": "vital_stream",
  "payload": {
    "hr": 76.2,
    "spo2": 97.3,
    "tempC": 36.8,
    "quality": 91,
    "alertCode": 0,
    "accel": { "x": 0.13, "y": -0.07, "z": 0.96 },
    "gyro": { "x": 5.2, "y": -2.1, "z": 8.7 }
  }
}
```

### 5.4 Success Response
```json
{
  "status": "accepted",
  "message": "Telemetry received",
  "id": "evt_1731234567890"
}
```

### 5.5 Error Responses
```json
{
  "status": "error",
  "code": "INVALID_PAYLOAD",
  "message": "Missing required telemetry fields"
}
```

---

## 6. Device and Patient Endpoints

### 6.1 Register Device
```http
POST /api/v1/devices/register
```

Request:
```json
{
  "deviceId": "VSN-UnoR4-001",
  "patientId": "P-1003",
  "model": "Arduino Uno R4 WiFi",
  "firmwareVersion": "1.0.0"
}
```

### 6.2 Get Device Status
```http
GET /api/v1/devices/{deviceId}/status
```

Response:
```json
{
  "deviceId": "VSN-UnoR4-001",
  "status": "online",
  "batteryPct": 87,
  "lastSeen": 1725879123456,
  "rssiDbm": -52
}
```

### 6.3 Get Patient Summary
```http
GET /api/v1/patients/{patientId}
```

Response:
```json
{
  "patientId": "P-1003",
  "name": "Asha Patil",
  "roomNo": "H1-204",
  "status": "watch",
  "lastHr": 112,
  "lastSpo2": 92,
  "lastTempC": 38.2
}
```

---

## 7. Alert Endpoints

### 7.1 List Alerts
```http
GET /api/v1/alerts
```

Query parameters:
- `status=active|acknowledged`
- `patientId={id}`
- `limit=50`

Response:
```json
{
  "alerts": [
    {
      "alertId": "AL-00128",
      "patientId": "P-1003",
      "severity": "critical",
      "type": "HIGH_HEART_RATE",
      "createdAt": 1725879123456,
      "status": "active"
    }
  ]
}
```

### 7.2 Acknowledge Alert
```http
POST /api/v1/alerts/ack
```

Request:
```json
{
  "alertId": "AL-00128",
  "acknowledgedBy": "staff-003",
  "note": "Reviewed and escalated to resident doctor"
}
```

### 7.3 Trigger SOS Dispatch
```http
POST /api/v1/sos/dispatch
```

Request:
```json
{
  "patientId": "P-1003",
  "source": "staff",
  "priority": "emergency",
  "location": "Hostel Block A"
}
```

---

## 8. Historical Telemetry Endpoints

### 8.1 Patient Vitals by Time Window
```http
GET /api/v1/patients/{patientId}/vitals?window=24h
```

Response:
```json
{
  "patientId": "P-1003",
  "window": "24h",
  "series": [
    { "timestamp": 1725879000000, "hr": 82, "spo2": 96, "tempC": 36.7 },
    { "timestamp": 1725879600000, "hr": 118, "spo2": 91, "tempC": 38.4 }
  ]
}
```

---

## 9. WebSocket Event Streams

### 9.1 Monitoring Stream
```text
/ws/monitoring
```

Payload:
```json
{
  "event": "patient_status_update",
  "patientId": "P-1003",
  "status": "watch",
  "severity": 2,
  "timestamp": 1725879123456,
  "metrics": {
    "hr": 112,
    "spo2": 92,
    "tempC": 38.2
  }
}
```

### 9.2 Alert Stream
```text
/ws/alerts
```

Payload:
```json
{
  "event": "alert_created",
  "alertId": "AL-00128",
  "type": "HIGH_HEART_RATE",
  "patientId": "P-1003",
  "severity": "critical"
}
```

### 9.3 Device Health Stream
```text
/ws/device-health
```

Payload:
```json
{
  "event": "device_status",
  "deviceId": "VSN-UnoR4-001",
  "online": true,
  "batteryPct": 78,
  "rssiDbm": -58
}
```

---

## 10. Error Codes

| Code | Meaning |
| --- | --- |
| `INVALID_PAYLOAD` | malformed or missing telemetry fields |
| `UNAUTHORIZED` | missing or invalid token |
| `FORBIDDEN` | insufficient role permissions |
| `NOT_FOUND` | device or patient not found |
| `RATE_LIMITED` | too many requests |
| `INTERNAL_ERROR` | server failure |

---

## 11. Data Contracts

### 11.1 TypeScript Interface
```ts
export interface VitalPacket {
  deviceId: string;
  patientId?: string;
  timestamp: number;
  type: 'vital_stream' | 'alert' | 'status' | 'fall_event';
  payload: {
    hr: number;
    spo2: number;
    tempC: number;
    quality: number;
    alertCode: number;
    accel?: { x: number; y: number; z: number };
    gyro?: { x: number; y: number; z: number };
  };
}
```

---

## 12. API Validation Rules

- timestamps must be integer epoch milliseconds;
- heart rate must be in a realistic range; invalid readings shall be rejected or marked low quality;
- patientId must resolve to a known profile before serious alerts are processed;
- all responses must use JSON objects with structured status keys.

---

## 13. Summary

The API layer provides a clean contract between the embedded telemetry source, the FastAPI backend, and the React.js monitoring interface. Its design supports reliable ingestion, role-aware access, live updates, and consistent alert workflows required for the VitalSense-Next system.

# VitalSense-Next: Design and Wireframe Specification

## 1. Purpose

This document defines the user experience and dashboard design system for the VitalSense-Next monitoring platform. The design is optimized for institutional operational use, quick triage, and alert visibility, while remaining accessible and readable under stress conditions.

---

## 2. Design Goals

- clear and immediate patient risk prioritization
- minimal cognitive load for staff during fast decision-making
- consistent color and spacing system for monitoring actions
- accessible interactions for critical events
- responsive layout for staff dashboard usage

---

## 3. Design Principles

### 3.1 Clarity Over Density
Staff should be able to answer three questions immediately:
1. which patients are at risk?
2. what is the severity?
3. what is the most recent signal trend?

### 3.2 Strong Alert Hierarchy
- green: stable
- yellow: watch / warning
- red: critical / emergency

### 3.3 Accessibility
- high contrast text and UI controls
- keyboard navigation support
- coherent focus states and semantic labels
- large interactive targets for emergency actions

---

## 4. Dashboard Layout

### 4.1 Main Overview Layout
```text
+---------------------------------------------------------------+
| VitalSense-Next Dashboard         [Search] [Filter] [Export] |
+---------------------------------------------------------------+
| Summary Cards: Total Monitored | Alerts | Critical | Offline   |
+---------------------------------------------------------------+
| Patient Triage Grid                                           |
| [Stable] [Watch] [Critical]                                   |
| +-----------------------------------------------------------+ |
| | Patient | HR | SpO2 | Temp | Status | Alert |            | |
| +-----------------------------------------------------------+ |
| | Asha    | 112| 92 | 38.2 | Watch  | High HR |            | |
| | Rahul   | 76 | 97 | 36.8 | Stable | None   |            | |
| | Riya    | 128| 89 | 39.1 | Critical| Fall   |            | |
| +-----------------------------------------------------------+ |
+---------------------------------------------------------------+
```

### 4.2 Patient Detail Panel
```text
+---------------------------------------------------------------+
| Patient: Asha Patil                   [Acknowledge] [SOS] |
+---------------------------------------------------------------+
| HR: 112 bpm | SpO2: 92% | Temp: 38.2C | Status: Watch        |
| Trend Chart: 24h historical view                             |
| ... line graph ...                                            |
| Recent events: 10:25 high heart rate, 10:41 fever rise        |
+---------------------------------------------------------------+
```

---

## 5. Screen Specifications

### 5.1 Overview Screen
- sortable patient list by severity or latest update time
- color-coded health state badges
- latest vitals and risk summary in each card
- alert count at the top for quick triage

### 5.2 Alert Inbox Screen
- list of active and acknowledged incidents
- severity labels and timestamps
- alert detail drawer with graphs and event log

### 5.3 Device Health Screen
- battery percentage, RSSI, last seen time, status label
- offline and degraded connectivity warnings

### 5.4 Admin / Management Screen
- user role management
- device registration and assignment
- reporting and audit log export

---

## 6. Interaction Model

### 6.1 Primary Actions
- acknowledge alert
- open patient profile
- view trend history
- trigger emergency workflow
- filter by room or severity

### 6.2 Behavior Rules
- critical patient rows must remain at top of the list
- alert banners must be persistent until acknowledged or resolved
- offline devices must be visually flagged without hiding the patient record

---

## 7. Accessibility Requirements

- contrast ratio should satisfy WCAG AA, with selective AAA emphasis for critical alarm elements
- color should not be the only indication of risk state
- text labels and icons used together for clarity
- keyboard focus ring and accessible names for all interactive commands
- screen readers must be able to announce patient risk status and alert actions

---

## 8. Visual Design System

### 8.1 Color Palette
| Role | Color | Use |
| --- | --- | --- |
| Stable / normal | green | no action needed |
| Warning | amber/yellow | moderate concern |
| Critical | red | emergency or severe condition |
| Offline | gray/blue | disconnected or degraded |
| Neutral surface | white / slate | UI elements |

### 8.2 Typography
- title: bold, large dashboard labels
- metric labels: medium emphasis
- impact text: high contrast for threshold values and status metrics

---

## 9. Wireframe Narrative

### 9.1 Overview
This is the operational landing screen. It prioritizes alert triage and high-visibility risk monitoring, which is the primary action for staff.

### 9.2 Patient Details
This screen presents historical data and event context. It should allow quick review without overwhelming the user with unsupported information.

### 9.3 Emergency Action
When a patient enters critical risk, the system should encourage immediate review and escalation. Emergency actions should be clearly presented but not accidental.

---

## 10. Acceptance Criteria

The design is accepted when:
- staff can detect critical patients within seconds from the overview screen;
- alert severity is visually distinct and readable;
- key monitoring actions are accessible without excessive navigation;
- the layout works on standard desktop monitors and smaller lap-sized screens;
- the dashboard remains understandable under stress and alert volume.

---

## 11. Summary

The design model prioritizes operational clarity and patient urgency over decorative complexity. The dashboard is intentionally structured to support real-time surveillance, rapid response, and institutional decision-making rather than general consumer wellness visualization.

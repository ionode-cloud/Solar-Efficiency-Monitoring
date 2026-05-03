# 🌞 AI-Power Smart Solar Monitoring System — Project Abstract

---

## Abstract

The **AI-Power Smart Solar Monitoring System** is a full-stack, real-time IoT web application designed to monitor, visualize, and analyze the performance of a solar photovoltaic (PV) panel array. The system bridges hardware-level sensor data with a cloud-hosted database and an interactive web dashboard, enabling engineers and operators to make data-driven decisions for maximizing solar energy output and system health.

The platform continuously ingests multi-parameter telemetry — including electrical metrics (voltage, current, power, cumulative energy), environmental data (ambient light intensity, panel temperature, dust accumulation level), and mechanical state (inclination angle, panel direction) — via a RESTful HTTP API. This data is persisted in a **MongoDB Atlas** cloud database and served to a responsive, browser-based dashboard that auto-refreshes every five seconds to provide near-real-time situational awareness.

---

## System Architecture

The system follows a decoupled **client-server (MERN-lite) architecture** consisting of two independently deployable modules:

```
┌────────────────────────────┐          ┌──────────────────────────┐
│       IoT Sensor Node      │──POST──▶│   Express.js REST API    │
│  (Hardware / Postman Test) │          │   (Node.js + Mongoose)   │
└────────────────────────────┘          │   MongoDB Atlas Cloud DB │
                                        └────────────┬─────────────┘
                                                     │ GET /data (poll every 5s)
                                        ┌────────────▼─────────────┐
                                        │   React.js Dashboard     │
                                        │   (Vite + Recharts)      │
                                        └──────────────────────────┘
```

### Backend — `Node.js / Express.js`
- **Framework**: Express.js v5 with ES Module syntax
- **Database**: MongoDB Atlas via Mongoose ODM (upsert-based single-document state model)
- **API**: Two core endpoints — `POST /data` (ingest/update sensor readings) and `GET /data` (retrieve latest state)
- **CORS**: Enabled globally to allow cross-origin requests from the frontend client
- **Environment**: Configuration via `.env` (MongoDB URI, server port)

### Frontend — `React.js / Vite`
- **Framework**: React 19 with Vite 7 build tooling
- **Charting**: Recharts library (AreaChart for dual-axis time-series visualization)
- **Icons**: Lucide React icon set
- **Polling**: `setInterval`-based data fetching every **5 seconds**, accumulating historical snapshots for chart rendering
- **Export**: In-browser JSON data-log download for offline analysis

---

## Key Parameters Monitored

| Parameter | Unit | Category |
|---|---|---|
| Total Array Efficiency | % | Performance |
| Instantaneous Power | kW | Electrical |
| Cumulative Energy | kWh | Electrical |
| Voltage | V | Electrical |
| Current | A | Electrical |
| Light Intensity | Lux | Environmental |
| Panel Temperature | °C | Environmental |
| Dust Level | % | Environmental |
| Inclination Angle | ° | Mechanical |
| Panel Direction | (Cardinal) | Mechanical |
| Sensor Health | Status | Diagnostics |

---

## Dashboard Components

| Component | Description |
|---|---|
| **Efficiency Panel** | Large KPI card displaying total array efficiency (%) with color-coded status — Optimal (≥75%), Monitoring (<75%), Critical Low (<60%) |
| **Primary Core Metrics** | Four metric cards for Power, Energy, Voltage, and Current |
| **Telemetry & Environment** | Six metric cards for light intensity, panel temp, dust level, angle, direction, and sensor health |
| **Real-Time Power Chart** | Dual-axis area chart plotting Power (kW) vs. Light Intensity (Lux) over time |
| **Efficiency vs. Temp Chart** | Correlation chart tracking Total Efficiency (%) against Panel Temperature (°C) over time |
| **Datalog Export** | One-click button to download all current session data as a JSON file |

---

## Technology Stack

| Layer | Technology |
|---|---|
| Frontend Framework | React 19 + Vite 7 |
| UI Components | Custom CSS + Lucide React Icons |
| Data Visualization | Recharts 3 (AreaChart, ResponsiveContainer) |
| Backend Runtime | Node.js with ES Modules |
| API Server | Express.js v5 |
| Database | MongoDB Atlas (Mongoose ODM v8) |
| Environment Config | dotenv |
| Deployment Target | Hostinger (Production) |

---

## Data Flow

1. **Sensor Node** (physical hardware or API client such as Postman) sends a `POST /data` request with a JSON payload of sensor readings.
2. The **Express server** receives the payload and performs a MongoDB **upsert** — updating the single persistent state document (or creating it on first run).
3. The **React dashboard** polls `GET /data` every 5 seconds, receiving the latest sensor snapshot.
4. Incoming data points are **appended to in-memory history arrays** which drive the real-time Recharts time-series graphs.
5. The user may click **DATALOG EXPORT** to download the full accumulated session data as a `.json` file for offline reporting.

---

## Use Cases

- **Solar Farm Operations**: Monitor array performance and detect efficiency drops caused by dust, shading, or overheating.
- **IoT Research & Prototyping**: Rapidly prototype sensor-to-cloud-to-dashboard pipelines with a minimal, extensible REST API.
- **Educational Demonstrations**: Illustrate real-time data ingestion and visualization patterns in engineering and computer science curricula.
- **Predictive Maintenance**: Correlate panel temperature vs. efficiency trends to schedule cleaning cycles or flag hardware anomalies.

---

## Project Structure

```
SolarMonitoring-main/
├── backend/
│   ├── server.js             # Express REST API + Mongoose schema + DB connection
│   ├── package.json          # Backend dependencies (express, mongoose, cors, dotenv)
│   └── .env                  # Environment variables (MONGO_URI, PORT)
│
├── frontend/
│   ├── src/
│   │   ├── App.jsx           # Root component — data fetching, layout, export logic
│   │   ├── components/
│   │   │   ├── MetricCard.jsx          # Generic KPI display card
│   │   │   ├── EfficiencyPanel.jsx     # Color-coded efficiency indicator
│   │   │   ├── RealTimeChart.jsx       # Power vs. Light dual-axis area chart
│   │   │   └── EfficiencyVsTempChart.jsx  # Efficiency vs. Temperature chart
│   │   ├── utils/utils.js    # Shared color constants & helpers
│   │   ├── App.css           # Global dashboard styles
│   │   └── index.css         # Base/reset styles
│   ├── index.html            # Vite HTML entry point
│   └── vite.config.js        # Vite build configuration
│
├── API_Documentation.doc     # REST API reference document
├── ABSTRACT.md               # This document
└── README.md                 # Project overview
```

---

## API Endpoints Summary

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/` | Health check — confirms server is running |
| `POST` | `/data` | Ingest sensor data (create or update) |
| `GET` | `/data` | Retrieve the latest sensor state snapshot |

---

*© 2026 — ionode-cloud / SolarMonitoring. Built with React, Express, and MongoDB Atlas.*

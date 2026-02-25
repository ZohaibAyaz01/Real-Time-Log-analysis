# Real-Time Log Analysis (SOC Monitor)

A Python-based Security Operations Center (SOC) monitoring project that ingests web server logs in real time, detects common attack patterns, stores events in SQLite, and exposes a live Flask dashboard for analysts.

## Features

- Real-time Apache-style log ingestion from `server_logs.txt`
- Detection for:
  - SQL Injection (SQLi)
  - Cross-Site Scripting (XSS)
  - Path Traversal
  - Command Injection
  - DDoS-like bursts (>10 requests/second per IP)
- Automatic and manual IP blocking
- Alert persistence in SQLite
- Dashboard APIs for stats, blocking/unblocking, and AI-assisted analysis
- Optional Gemini-based AI security summary
- Included synthetic traffic generator for demos/testing

## Project Structure

- `soc_monitor.py` — Core monitor, threat detection, Flask dashboard, and APIs
- `log_generator.py` — Synthetic web traffic and attack log generator
- `check_models.py` — Utility to list available Gemini models for your API key
- `templates/dashboard.html` — Front-end dashboard UI
- `api_key.txt` — Optional local Gemini key file (do not commit real keys)

## Requirements

- Python 3.9+
- Pip packages:
  - `flask`
  - `google-generativeai`

Install dependencies:

```bash
pip install flask google-generativeai
```

## Configuration

The monitor can load your Gemini key using one of the following (first one found is used):

1. Command line argument
2. Environment variables: `GEMINI_API_KEY` or `GEMINI_API`
3. `api_key.txt` in the project root

If no key is found, monitoring still runs and AI analysis endpoints are disabled.

## Run the Project

Open two terminals in the project directory.

### 1) Start synthetic log generation

```bash
python log_generator.py
```

### 2) Start SOC monitor + dashboard

```bash
python soc_monitor.py
```

Then open:

- Dashboard: `http://localhost:5000`

## API Endpoints

- `GET /api/stats` — Dashboard metrics, alerts, blocked IPs, traffic history
- `POST /api/block` — Manually block an IP
  - JSON body: `{"ip":"1.2.3.4","reason":"Manual block"}`
- `POST /api/unblock` — Remove a blocked IP
  - JSON body: `{"ip":"1.2.3.4"}`
- `POST /api/analyze` — AI-generated HTML security analysis (Gemini required)

## Database

The monitor creates `logs.db` with these tables:

- `logs` — Parsed requests, threat tags, blocked flag
- `blocked_ips` — Current blocked IP entries and reason
- `alerts` — Threat and block alerts with severity

## Notes

- This project is designed for security monitoring demos and learning workflows.
- Detection is regex/rate-based and may produce false positives/negatives.
- Avoid using real secrets in `api_key.txt`; prefer environment variables.

## Quick Health Check

Validate scripts compile:

```bash
python -m py_compile soc_monitor.py log_generator.py check_models.py
```

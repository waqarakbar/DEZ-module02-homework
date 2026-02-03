# DEZ Module 02 Homework - NYC Taxi Data Ingestion

A Kestra-based data pipeline for ingesting NYC Taxi trip data into PostgreSQL.

## Overview

This project implements an automated ETL pipeline using [Kestra](https://kestra.io/) to:
- Download NYC yellow and green taxi trip data from the [DataTalksClub repository](https://github.com/DataTalksClub/nyc-tlc-data/releases)
- Load data into PostgreSQL using a staging/merge pattern for idempotent ingestion
- Schedule monthly data loads via cron triggers

## Architecture

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│   Kestra        │────▶│   PostgreSQL    │◀────│    pgAdmin      │
│   (Orchestrator)│     │   (ny_taxi DB)  │     │    (UI)         │
│   :8080         │     │   :5432         │     │    :8085        │
└─────────────────┘     └─────────────────┘     └─────────────────┘
```

## Services

| Service | Port | Description |
|---------|------|-------------|
| Kestra | 8080 | Workflow orchestration UI |
| PostgreSQL (Data) | 5432 | NYC taxi data storage |
| pgAdmin | 8085 | Database management UI |
| PostgreSQL (Kestra) | - | Kestra metadata storage |

## Prerequisites

- Docker & Docker Compose
- Python 3.x (optional, for local development)
- uv (Python package manager)

## Quick Start

1. **Start the services:**
   ```bash
   docker compose up -d
   ```

2. **Access Kestra UI:**
   - URL: http://localhost:8080
   - Username: `admin@kestra.io`
   - Password: `Admin1234`

3. **Access pgAdmin:**
   - URL: http://localhost:8085
   - Email: `admin@admin.com`
   - Password: `root`

4. **Import the flow:**
   - Navigate to Kestra UI → Flows
   - Import `taxi_data_ingestion_pg_flow.yaml`

## Flow Details

### `taxi_data_ingestion_pg_flow`

**Namespace:** `zoomcamp`

**Features:**
- Supports both **yellow** and **green** taxi data
- Uses staging tables + MERGE for idempotent loads
- Generates unique row IDs using MD5 hash
- Scheduled monthly runs (green: 9 AM, yellow: 10 AM on 1st of month)

**Inputs:**
- `taxi`: Select taxi type (`yellow` or `green`)

## Database Connection

Connect to the NYC Taxi database using:
- **Host:** `localhost` (or `pgdatabase` from within Docker)
- **Port:** `5432`
- **Database:** `ny_taxi`
- **Username:** `root`
- **Password:** `root`

## Development

```bash
# Install dependencies with uv
uv sync

# Run Python scripts (if any)
uv run main.py
```

## Stopping Services

```bash
docker compose down

# To also remove volumes (data will be lost):
docker compose down -v
```

## License

This project is part of the [Data Engineering Zoomcamp](https://github.com/DataTalksClub/data-engineering-zoomcamp) homework.

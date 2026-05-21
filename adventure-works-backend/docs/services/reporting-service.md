# Reporting Service

## Overview

The **Reporting Service** provides analytical reports and dashboards for Adventure Works. It queries ClickHouse for aggregated business metrics — sales revenue, product performance, regional trends, and KPIs.

## Technology Stack

| Item         | Value                    |
|--------------|-------------------------|
| Language     | Python 3.12             |
| Framework    | FastAPI                 |
| Architecture | Vertical Slice          |
| Database     | ClickHouse 24.8 (OLAP)  |

## Port

| Environment | Port |
|-------------|------|
| Local (`uvicorn`) | 8000 |
| Docker (host) | 5005 |
| Docker (container) | 8080 |

## Project Structure

```
reporting-service/
├── Dockerfile
├── .dockerignore
└── src/
    ├── main.py               # FastAPI app entry point
    ├── features/
    │   ├── sales_reports/
    │   │   ├── router.py
    │   │   └── queries.py
    │   ├── product_reports/
    │   └── regional_reports/
    └── db/
        └── clickhouse.py     # ClickHouse client setup
```

## Environment Variables

| Variable | Description |
|----------|-------------|
| `PYTHONUNBUFFERED` | `1` (for real-time log output) |
| `CLICKHOUSE_HOST` | ClickHouse hostname (`clickhouse`) |
| `CLICKHOUSE_PORT` | ClickHouse HTTP port (`8123`) |
| `CLICKHOUSE_USER` | ClickHouse username (default: `default`) |
| `CLICKHOUSE_PASSWORD` | ClickHouse password for authenticated queries |

## Database

- **Server:** ClickHouse 24.8 (container: `clickhouse`)
- **HTTP Port:** `8123`
- **Native Port:** `9000`

## Dependencies

| Service | Purpose |
|---------|---------|
| `clickhouse` | OLAP query engine for aggregated data |

## Running Locally

```bash
# Via uvicorn
cd services/reporting-service/src
pip install fastapi uvicorn clickhouse-driver
uvicorn main:app --reload --port 8000

# Via Docker Compose
cd deployment
docker compose up -d reporting-service clickhouse
```

## Key Endpoints (Planned)

| Method | Route | Description |
|--------|-------|-------------|
| `GET` | `/reports/sales/summary` | Sales revenue summary |
| `GET` | `/reports/sales/by-region` | Sales by region |
| `GET` | `/reports/products/top-selling` | Top selling products |
| `GET` | `/health` | Health check |
| `GET` | `/health-ui-api` | Health payload for dashboard polling |
| `GET` | `/health-ui` | Browser dashboard for health visibility |

## Health Check Model

- Overall status is computed from dependency checks.
- `self` check: confirms the Reporting Service process is running.
- `clickhouse` check: executes authenticated `SELECT 1` over ClickHouse HTTP using `CLICKHOUSE_HOST`, `CLICKHOUSE_PORT`, `CLICKHOUSE_USER`, and `CLICKHOUSE_PASSWORD`.
- Each check includes `status`, `details`, and `duration`.

## Roadmap

- [ ] Initialize Python project with `requirements.txt`
- [ ] Set up FastAPI app structure
- [ ] Connect to ClickHouse with `clickhouse-driver`
- [ ] Implement Sales Reports slice
- [ ] Implement Product Reports slice
- [ ] Add health check endpoint

# HubSpot Deals Data Extraction Service

A robust Flask-RESTX API service for extracting **HubSpot Deals data** using DLT (Data Load Tool) and PostgreSQL. The system supports asynchronous data extraction, API-driven workflows, and production-ready deployment with Docker.

---

## Features

* **HubSpot Deals API Integration**: Fetches real deals data from HubSpot CRM
* **DLT Pipeline**: Efficient ETL pipeline for loading data into PostgreSQL
* **Flask-RESTX API**: Clean REST API with Swagger documentation
* **Async Processing**: Background scan execution with status tracking
* **Docker Support**: Multi-environment setup (dev/stage/prod)
* **Checkpointing**: Resume extraction from last state
* **Monitoring & Logging**: Health checks, logs, and pipeline info
* **Validation**: Input validation using schemas
* **Production Ready**: Error handling, structured logging, scalable design

---

## Project Structure

```
hubspot-deals-etl/
├── README.md
├── docker-compose.yml
├── Dockerfile.dev
├── Dockerfile.stage
├── Dockerfile.prod
├── requirements.txt
├── .env.example
├── .gitignore
├── .dockerignore
│
├── app.py
├── wsgi.py
├── config.py
├── loki_logger.py
├── utils.py
│
├── api/
│   ├── routes.py
│   └── schemas.py
│
├── services/
│   ├── extraction_service.py
│   ├── api_service.py
│   ├── data_source.py
│   └── database_service.py
│
├── models/
├── docs/
└── logs/
```

---

## Setup & Installation

### Prerequisites

* Docker & Docker Compose
* Python 3.11+ (optional for local run)
* HubSpot Private App Access Token

---

### Quick Start with Docker

#### 1. Clone repository

```bash
git clone <your-repo-link>
cd hubspot-deals-etl
```

---

#### 2. Create `.env`

```env
HUBSPOT_ACCESS_TOKEN=your_token_here
DB_HOST=localhost
DB_PORT=5432
DB_NAME=hubspot_db
DB_USER=postgres
DB_PASSWORD=postgres
```

---

#### 3. Start services

```bash
docker-compose up --build
```

---

#### 4. Verify setup

```bash
curl http://localhost:5200/api/health
```

Swagger UI:

```
http://localhost:5200/docs
```

---

## API Documentation

### Swagger UI

Access interactive docs:

```
http://localhost:5200/docs
```

---

### Key Endpoints

| Method | Endpoint                     | Description       |
| ------ | ---------------------------- | ----------------- |
| GET    | `/api/health`                | Health check      |
| POST   | `/api/scan/start`            | Start extraction  |
| GET    | `/api/scan/{scan_id}/status` | Check scan status |
| POST   | `/api/scan/{scan_id}/cancel` | Cancel scan       |
| GET    | `/api/scan/list`             | List scans        |
| GET    | `/api/pipeline/info`         | Pipeline info     |

---

## Example API Usage

### Start Extraction

```bash
curl -X POST http://localhost:5200/api/scan/start \
-H "Content-Type: application/json" \
-d '{
  "config": {
    "scanId": "hubspot-deals-scan-001",
    "organizationId": "org-123",
    "type": ["hubspot_deals"],
    "auth": {
      "accessToken": "YOUR_TOKEN"
    }
  }
}'
```

---

### Check Status

```bash
curl http://localhost:5200/api/scan/hubspot-deals-scan-001/status
```

---

## Data Flow

1. API receives scan request
2. Extraction service starts job
3. HubSpot API is called
4. Deals data is fetched
5. Data is transformed
6. Data is stored in PostgreSQL
7. Status is updated

---

## Configuration

### Environment Variables

| Variable             | Description               |
| -------------------- | ------------------------- |
| HUBSPOT_ACCESS_TOKEN | HubSpot private app token |
| DB_HOST              | Database host             |
| DB_PORT              | Database port             |
| DB_NAME              | Database name             |
| DB_USER              | Database user             |
| DB_PASSWORD          | Database password         |

---

## Database

* Uses PostgreSQL
* Stores extracted HubSpot deals
* Dataset format:

```
hubspot_deals_<organization_id>
```

---

## Development

### Run Locally

```bash
pip install -r requirements.txt
python app.py
```

---

### Access Database

```bash
docker-compose exec postgres psql -U postgres -d hubspot_db
```

---

## Troubleshooting

### Common Issues

**No data extracted**

* Ensure HubSpot has deals
* Check API token permissions

**401 Error**

* Invalid token

**Database issues**

* Restart containers

```bash
docker-compose down
docker-compose up --build
```

---

## Deployment

### Production

```bash
docker-compose --profile prod up -d
```

---

## Summary

This project implements a complete backend system that:

* Integrates with HubSpot API
* Extracts deals data
* Stores it in PostgreSQL
* Provides API access and monitoring

---

## References

* HubSpot API: https://developers.hubspot.com/docs/api/crm/deals
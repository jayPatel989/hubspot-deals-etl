# HubSpot Deals Data Extraction Service

A robust Flask-RESTX API service for extracting **HubSpot Deals data** using DLT (Data Load Tool) and PostgreSQL. The system supports asynchronous data extraction, API-driven workflows, and production-ready deployment with Docker.

---

# Project Overview

This project was generated using the DLT Generator framework and customized for HubSpot Deals extraction.

The service:

- Connects to HubSpot API using Private App Access Token
- Extracts HubSpot Deals data
- Processes and transforms records
- Loads extracted data into PostgreSQL using DLT
- Tracks extraction jobs and checkpoints
- Provides API endpoints for managing scans
- Includes Swagger API documentation

---

# Features

- HubSpot Deals API integration for securely fetching real-time deals data from HubSpot CRM using Private App access tokens.
- DLT-based ETL pipeline for extracting, transforming, normalizing, and loading HubSpot Deals data into PostgreSQL datasets.
- PostgreSQL database storage for persisting extracted deal records, pipeline metadata, and checkpoint information.
- REST API endpoints for starting extraction jobs, monitoring scan progress, checking status, and managing ETL workflows.
- Swagger/OpenAPI documentation for interactive API testing, endpoint visualization, and request/response validation.
- Dockerized development environment for running backend services, PostgreSQL, and Redis consistently across systems.
- Extraction checkpointing support for tracking progress and enabling reliable recovery during interrupted extraction jobs.
- Job status tracking system for monitoring scan lifecycle states such as pending, running, completed, and failed.
- Health monitoring endpoints for verifying backend availability, service status, and overall application readiness.
- Error handling and structured logging system for capturing failures, debugging issues, and monitoring pipeline activity.
- DLT is used to manage data extraction, normalization, schema generation, and loading into PostgreSQL datasets efficiently.

---

# Tech Stack

- Python 3.11
- Flask
- DLT (Data Load Tool)
- PostgreSQL
- SQLAlchemy
- Docker
- Redis
- Swagger / Flask-RESTX

---

## Project Structure

```
hubspot-deals-etl/
│
├── .dlt/
│
├── api/
│   ├── __init__.py
│   ├── routes.py
│   ├── schemas.py
│   └── swagger_schemas.py
│
├── docs/
│   ├── API-DOCS.md
│   ├── DATABASE-DESIGN-DOCS.md
│   └── INTEGRATION-DOCS.md
│
├── logs/
│   └── app.log
│
├── models/
│   ├── __init__.py
│   ├── database.py
│   └── models.py
│
├── services/
│   ├── __init__.py
│   ├── api_service.py
│   ├── data_source.py
│   ├── database_service.py
│   ├── extraction_service.py
│   └── job_service.py
│
├── test-results/
│   ├── api-response.json
│   ├── db_data.txt
│   └── sample-data.txt
│
├── .dockerignore
├── .gitignore
│
├── app.py
├── config.py
├── docker-compose.yml
│
├── Dockerfile.dev
├── Dockerfile.prod
├── Dockerfile.stage
├── Dockerfile.test
│
├── encrypter.py
├── loki_logger.py
├── README.md
├── requirements.txt
├── utils.py
└── wsgi.py
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

Example:
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

#### 5. HubSpot Setup

* Create HubSpot Developer Account
- Go to: https://developers.hubspot.com/
- Create a developer account
- Create a test account
- Navigate to: Settings → Integrations → Private Apps
- Create a Private App
- Enable scope: crm.objects.deals.read
- Generate access token

---

#### 6. Create Test Deals

* Example Data Used:

| Deal Name      | Amount | Stage                      |
| -------------- | ------ | -------------------------- |
| Test Deal 1    | 5000   | qualified                  |
| Test Deal 2    | 2000   | appointment scheduled      |
| Test Deal 3    | 8000   | presentation scheduled     |
| Test Deal 4    | 3000   | Decision Maker Bought in   |
| Test Deal 5    | 1500   | Contract Sent              |

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

## Deployment

### Production

```bash
docker-compose --profile prod up -d
```

---

### Database Verification
The extraction pipeline stores HubSpot Deals data in PostgreSQL using DLT.

* Connect to PostgreSQL Container
```bash
docker exec -it hubspot_deals_postgres_dev psql -U postgres -d hubspot_deals_data_dev
```

* View Available Schemas
```SQL
\dn
```
Expected schema: hubspot_deals_org_123

* Switch to Extraction Schema:
```SQL
SET search_path TO hubspot_deals_org_123;
```

* View Extracted Tables
```SQL
\dt;
```

* Verify Extracted Records
```SQL
SELECT * FROM hubspot_deals LIMIT 5;
```
---

## Test Results
### Project verification files are included in: 
* test-results/

### Contents include:
- API responses
- Database verification output
- Extracted records proof
- Testing screenshots

## Documentation Files
### Additional documentation is available in: 
* docs/

### Files:
- API-DOCS.md
- DATABASE-DESIGN-DOCS.md
- INTEGRATION-DOCS.md

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

## Summary

This project implements a complete backend system that:

* Integrates with HubSpot API
* Extracts deals data
* Stores it in PostgreSQL
* Provides API access and monitoring

---

## References

* HubSpot API: https://developers.hubspot.com/docs/api/crm/deals

# 🗄️ HubSpot Deals ETL - Database Design

---

## 📋 Overview

This database is designed to store HubSpot deals data and track extraction jobs.

The system uses two main tables:

1. **scan_jobs** → tracks extraction process
2. **hubspot_deals** → stores deals data

---

# 🏗️ 1. Scan Jobs Table

## Purpose

This table tracks each extraction job (scan), including its status and progress.

---

## Table: `scan_jobs`

| Column Name     | Type      | Description                         |
| --------------- | --------- | ----------------------------------- |
| id              | UUID      | Internal unique ID                  |
| scan_id         | VARCHAR   | External scan identifier            |
| status          | VARCHAR   | pending, running, completed, failed |
| scan_type       | VARCHAR   | Type of scan (hubspot_deals)        |
| started_at      | TIMESTAMP | Start time                          |
| completed_at    | TIMESTAMP | End time                            |
| total_items     | INTEGER   | Total records                       |
| processed_items | INTEGER   | Successfully processed              |
| failed_items    | INTEGER   | Failed records                      |
| created_at      | TIMESTAMP | Created time                        |
| updated_at      | TIMESTAMP | Last updated                        |

---

## SQL Create Table

```sql id="o6v8sp"
CREATE TABLE scan_jobs (
    id UUID PRIMARY KEY,
    scan_id VARCHAR(255) UNIQUE NOT NULL,
    status VARCHAR(50) NOT NULL,
    scan_type VARCHAR(100) NOT NULL,
    started_at TIMESTAMP,
    completed_at TIMESTAMP,
    total_items INTEGER DEFAULT 0,
    processed_items INTEGER DEFAULT 0,
    failed_items INTEGER DEFAULT 0,
    created_at TIMESTAMP NOT NULL,
    updated_at TIMESTAMP NOT NULL
);
```

---

## Indexes

```sql id="v2ux5y"
CREATE INDEX idx_scan_status ON scan_jobs(status);
CREATE INDEX idx_scan_created ON scan_jobs(created_at);
```

---

# 🏗️ 2. HubSpot Deals Table

## Purpose

Stores deal data fetched from HubSpot.

---

## Table: `hubspot_deals`

| Column Name  | Type      | Description            |
| ------------ | --------- | ---------------------- |
| id           | UUID      | Internal ID            |
| scan_job_id  | UUID      | Reference to scan_jobs |
| hubspot_id   | VARCHAR   | Deal ID from HubSpot   |
| deal_name    | VARCHAR   | Name of deal           |
| amount       | NUMERIC   | Deal amount            |
| stage        | VARCHAR   | Deal stage             |
| close_date   | TIMESTAMP | Closing date           |
| created_date | TIMESTAMP | Created date           |
| extracted_at | TIMESTAMP | Extraction time        |
| raw_data     | JSON      | Full API response      |
| created_at   | TIMESTAMP | Record created         |
| updated_at   | TIMESTAMP | Record updated         |

---

## SQL Create Table

```sql id="nmn17k"
CREATE TABLE hubspot_deals (
    id UUID PRIMARY KEY,
    scan_job_id UUID REFERENCES scan_jobs(id),
    hubspot_id VARCHAR(255),
    deal_name VARCHAR(255),
    amount NUMERIC,
    stage VARCHAR(100),
    close_date TIMESTAMP,
    created_date TIMESTAMP,
    extracted_at TIMESTAMP,
    raw_data JSON,
    created_at TIMESTAMP NOT NULL,
    updated_at TIMESTAMP NOT NULL
);
```

---

## Indexes

```sql id="2r5m61"
CREATE INDEX idx_deals_scan_job ON hubspot_deals(scan_job_id);
CREATE INDEX idx_deals_stage ON hubspot_deals(stage);
CREATE INDEX idx_deals_close_date ON hubspot_deals(close_date);
```

---

# 🔗 Relationships

* One scan job → many deals

```text id="p4h5si"
scan_jobs.id → hubspot_deals.scan_job_id
```

---

# 📊 Data Flow

1. User starts extraction
2. A new record is created in `scan_jobs`
3. HubSpot data is fetched
4. Each deal is stored in `hubspot_deals`
5. Progress is updated in `scan_jobs`

---

# ⚡ Performance Considerations

* Use indexes on:

  * scan_job_id
  * stage
  * close_date
* Use pagination when fetching data
* Store raw JSON for flexibility

---

# 🔒 Data Integrity

* Ensure valid status values:

  * pending
  * running
  * completed
  * failed

* Use foreign key:

```sql id="5o0sbo"
FOREIGN KEY (scan_job_id) REFERENCES scan_jobs(id)
```

---

# 💡 Design Decisions

* Separate scan tracking from data storage
* Store raw API response for debugging
* Support multi-run extraction
* Keep schema simple and scalable

---

# 📈 Future Improvements

* Add tenant_id for multi-tenant support
* Add indexing for faster queries
* Add partitioning for large datasets
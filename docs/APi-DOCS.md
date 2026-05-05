# 📡 HubSpot Deals ETL - API Documentation

---

## 📋 Overview

This API provides endpoints to extract, monitor, and manage HubSpot deals data.

The service fetches deal data from HubSpot, stores it in a database, and allows users to track extraction progress and retrieve results.

---

## 🔐 Authentication

Currently, the API does not require authentication for internal use.

HubSpot API authentication is handled internally using a private access token stored in environment variables.

---

## 🌐 Base URLs

### Development

```bash
http://localhost:5200
```

### API Base Path

```bash
/api/v1
```

### Swagger Docs

```bash
http://localhost:5200/docs
```

---

## 📊 Common Response Format

### Success Response

```json
{
  "status": "success",
  "data": {},
  "message": "Operation successful"
}
```

### Error Response

```json
{
  "status": "error",
  "message": "Something went wrong"
}
```

---

# 🔍 API Endpoints

---

## 1. Start Extraction

### Endpoint

```bash
POST /api/v1/scan/start
```

### Description

Starts a new HubSpot deals extraction process.

---

### Request Body

```json
{
  "config": {
    "scanId": "scan-001",
    "type": ["hubspot_deals"],
    "auth": {
      "access_token": "YOUR_HUBSPOT_ACCESS_TOKEN"
    }
  }
}
```

---

### Response

```json
{
  "message": "Extraction started",
  "scanId": "scan-001",
  "status": "running"
}
```

---

## 2. Get Scan Status

### Endpoint

```bash
GET /api/v1/scan/{scan_id}/status
```

### Description

Returns the current status of the extraction process.

---

### Response

```json
{
  "scanId": "scan-001",
  "status": "running",
  "total_items": 100,
  "processed_items": 60,
  "failed_items": 2
}
```

---

## 3. Cancel Scan

### Endpoint

```bash
POST /api/v1/scan/{scan_id}/cancel
```

### Description

Stops an ongoing extraction.

---

### Response

```json
{
  "message": "Scan cancelled",
  "scanId": "scan-001",
  "status": "cancelled"
}
```

---

## 4. List All Scans

### Endpoint

```bash
GET /api/v1/scan/list
```

### Description

Returns all scan jobs.

---

### Response

```json
[
  {
    "scanId": "scan-001",
    "status": "completed"
  },
  {
    "scanId": "scan-002",
    "status": "running"
  }
]
```

---

## 5. Get Pipeline Info

### Endpoint

```bash
GET /api/v1/pipeline/info
```

### Description

Returns system and pipeline information.

---

### Response

```json
{
  "service": "hubspot_deals",
  "version": "1.0",
  "status": "active"
}
```

---

## 6. Cleanup Old Data

### Endpoint

```bash
POST /api/v1/maintenance/cleanup
```

### Description

Deletes old scan data.

---

### Response

```json
{
  "message": "Cleanup completed"
}
```

---

# 🏥 Health Endpoint

---

## Health Check

### Endpoint

```bash
GET /api/v1/health
```

### Response

```json
{
  "status": "healthy"
}
```

---

# ⚠️ Error Handling

| Code | Meaning      |
| ---- | ------------ |
| 400  | Bad request  |
| 404  | Not found    |
| 500  | Server error |

---

# 📌 Example Workflow

### Step 1: Start Extraction

```bash
POST /api/v1/scan/start
```

---

### Step 2: Check Status

```bash
GET /api/v1/scan/scan-001/status
```

---

### Step 3: Wait Until Completed

---

### Step 4: Fetch Data from Database

---

# 💡 Notes

* Uses HubSpot API internally
* Supports pagination
* Handles rate limits
* Stores data in PostgreSQL

---

# 📞 References

* HubSpot API: https://developers.hubspot.com/docs/api/crm/deals

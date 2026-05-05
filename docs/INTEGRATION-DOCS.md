# 📋 HubSpot Deals Service - Integration with HubSpot API

This document explains how the HubSpot REST API is used to extract **Deals data** for the HubSpot Deals ETL service.

---

## 📋 Overview

The HubSpot Deals ETL service integrates with HubSpot CRM APIs to fetch deal records. These records are then processed and stored in a PostgreSQL database.

The integration uses HubSpot’s REST API with private app authentication. Data is fetched in a paginated format and transformed before storage.

---

## ✅ Required Endpoint (Essential)

| API Endpoint            | Purpose              | Version | Required Permissions     | Usage    |
| ----------------------- | -------------------- | ------- | ------------------------ | -------- |
| `/crm/v3/objects/deals` | Fetch all deals data | v3      | `crm.objects.deals.read` | Required |

---

## 🎯 Recommendation

Start with only the required endpoint `/crm/v3/objects/deals`.
This endpoint provides all necessary deal data for extraction and analysis.

---

## 🔐 Authentication Requirements

### Private App Token Authentication

```http
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

### Required Permissions

* `crm.objects.deals.read` → Read deals data

---

## 🌐 HubSpot API Endpoint

### 🎯 Primary Endpoint (Required)

### 1. Fetch Deals

**Endpoint:**

```http
GET https://api.hubapi.com/crm/v3/objects/deals
```

---

### 🔹 Query Parameters

| Parameter    | Description                             |
| ------------ | --------------------------------------- |
| `limit`      | Number of records per request (max 100) |
| `after`      | Pagination cursor                       |
| `properties` | Fields to retrieve                      |
| `archived`   | Include archived deals                  |

---

### 🔹 Request Example

```http
GET https://api.hubapi.com/crm/v3/objects/deals?limit=50&archived=false
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

---

### 🔹 Response Example

```json
{
  "results": [
    {
      "id": "12345",
      "properties": {
        "dealname": "Big Deal",
        "amount": "50000",
        "dealstage": "closedwon",
        "closedate": "2024-01-01T00:00:00Z",
        "createdate": "2023-12-01T00:00:00Z"
      }
    }
  ],
  "paging": {
    "next": {
      "after": "123456"
    }
  }
}
```

---

## 📊 Data Extraction Flow

### Simple Extraction Flow (Recommended)

1. Send request to `/crm/v3/objects/deals`
2. Receive list of deals
3. Store results
4. Check `paging.next.after`
5. Repeat until no more data

---

### Example Logic

```python
while True:
    response = get_deals(after_cursor)
    
    save_data(response["results"])
    
    if "paging" not in response:
        break
    
    after_cursor = response["paging"]["next"]["after"]
```

---

## ⚡ Performance Considerations

### Rate Limits

* 150 requests per 10 seconds

### Best Practices

* Use pagination (limit ≤ 100)
* Add retry logic
* Avoid unnecessary requests

---

## 🚨 Error Handling

| Status Code | Meaning                        |
| ----------- | ------------------------------ |
| 401         | Unauthorized (invalid token)   |
| 403         | Forbidden (missing permission) |
| 429         | Rate limit exceeded            |
| 500         | Server error                   |

---

## 🔒 Security Requirements

* Store access token in `.env`
* Never expose token publicly
* Use HTTPS always

---

## 🧪 Testing API

### Test with curl

```bash
curl -X GET \
"https://api.hubapi.com/crm/v3/objects/deals?limit=5" \
-H "Authorization: Bearer YOUR_ACCESS_TOKEN"
```

---

## 🚨 Common Issues

### 401 Unauthorized

* Token is invalid or expired

### 403 Forbidden

* Missing permission `crm.objects.deals.read`

### 429 Rate Limit

* Too many requests → add delay

---

## 💡 Implementation Recommendation

### Phase 1 (Required)

* Fetch deals using `/crm/v3/objects/deals`
* Store basic fields

### Phase 2 (Optional)

* Add more properties
* Add filtering
* Optimize performance

---

## 📞 References

* HubSpot API Docs: https://developers.hubspot.com/docs/api/crm/deals

# API Reference

Complete API documentation for Lobster Kingdom.

## Base URL

```
http://localhost:3995
```

## Endpoints

### GET /api/lobsters

Returns all lobster data with optional filtering.

#### Query Parameters

| Parameter | Type | Description | Example |
|-----------|------|-------------|---------|
| `zone` | string | Filter by zone (working/resting/idle) | `?zone=working` |
| `level` | number | Filter by level (0-5) | `?level=5` |
| `search` | string | Search by name (max 50 chars) | `?search=general` |
| `limit` | number | Limit results (max 500) | `?limit=10` |

#### Response

```json
{
  "lobsters": [
    {
      "id": "1234567890",
      "name": "General Discussion",
      "tokens": 15000000,
      "messages": 250,
      "level": 3,
      "levelName": "中虾",
      "zone": "working",
      "lastActive": "2026-03-12T07:30:00.000Z",
      "size": 110,
      "color": "#2196F3"
    }
  ],
  "stats": {
    "total": 121,
    "working": 7,
    "resting": 20,
    "idle": 94,
    "totalTokens": 2000000000,
    "avgLevel": "2.3"
  }
}
```

#### Examples

```bash
# Get all lobsters
curl http://localhost:3995/api/lobsters

# Get working lobsters only
curl http://localhost:3995/api/lobsters?zone=working

# Get level 5 lobsters
curl http://localhost:3995/api/lobsters?level=5

# Search for "dev" channels
curl http://localhost:3995/api/lobsters?search=dev

# Get top 10 lobsters
curl http://localhost:3995/api/lobsters?limit=10

# Combine filters
curl http://localhost:3995/api/lobsters?zone=working&level=5&limit=5
```

#### Rate Limiting

- **Limit**: 1 request per second per IP
- **Response**: 429 Too Many Requests if exceeded

---

### GET /api/stats

Returns lightweight statistics without lobster details.

#### Response

```json
{
  "total": 121,
  "working": 7,
  "resting": 20,
  "idle": 94,
  "totalTokens": 2000000000,
  "avgLevel": "2.3"
}
```

#### Example

```bash
curl http://localhost:3995/api/stats
```

---

### GET /health

Health check endpoint for monitoring.

#### Response

```json
{
  "ok": true,
  "uptime": 3600.5,
  "memory": "414MB",
  "lobsters": 121,
  "timestamp": "2026-03-12T07:30:00.000Z"
}
```

#### Example

```bash
curl http://localhost:3995/health
```

---

## Response Codes

| Code | Description |
|------|-------------|
| 200 | Success |
| 429 | Too Many Requests (rate limit exceeded) |
| 500 | Internal Server Error |
| 503 | Service Unavailable (data not ready) |

## Error Responses

```json
{
  "error": "too many requests"
}
```

```json
{
  "error": "data not ready"
}
```

```json
{
  "error": "internal error"
}
```

## Compression

All responses support Gzip compression. Include the header:

```
Accept-Encoding: gzip
```

This reduces response size by ~78% (23KB → 5KB for typical data).

## Caching

- **Result Cache**: 15 seconds TTL
- **File Metadata Cache**: Tracks mtime and bytes read
- **Client Caching**: Use `ETag` headers for conditional requests

## CORS

CORS is not enabled by default. To enable, add to `server.js`:

```javascript
app.use((req, res, next) => {
  res.setHeader('Access-Control-Allow-Origin', '*');
  res.setHeader('Access-Control-Allow-Methods', 'GET');
  next();
});
```

## WebSocket (Future)

WebSocket support is planned for real-time updates. Track progress in [Issue #1](https://github.com/yourusername/lobster-kingdom/issues/1).

## Client Libraries

### JavaScript/TypeScript

```javascript
async function getLobsters(filters = {}) {
  const params = new URLSearchParams(filters);
  const response = await fetch(`http://localhost:3995/api/lobsters?${params}`);
  return response.json();
}

// Usage
const data = await getLobsters({ zone: 'working', limit: 10 });
console.log(data.lobsters);
```

### Python

```python
import requests

def get_lobsters(filters=None):
    response = requests.get('http://localhost:3995/api/lobsters', params=filters)
    return response.json()

# Usage
data = get_lobsters({'zone': 'working', 'limit': 10})
print(data['lobsters'])
```

### cURL

```bash
# Save to file
curl http://localhost:3995/api/lobsters > lobsters.json

# Pretty print
curl -s http://localhost:3995/api/lobsters | jq .

# Watch in real-time
watch -n 5 'curl -s http://localhost:3995/api/stats | jq .'
```

## Performance

- **Incremental Parsing**: Only reads new data, 90%+ faster
- **Gzip Compression**: 78% bandwidth savings
- **Rate Limiting**: Prevents abuse
- **Caching**: 15s result cache reduces load

## Security

- **Rate Limiting**: 1 req/sec per IP
- **Input Validation**: Query parameters are sanitized
- **Security Headers**: XSS, clickjacking protection
- **No Authentication**: Add your own auth layer if needed

# 📡 API 文档

## GET /api/lobsters

获取所有龙虾数据（支持 Gzip 压缩）。

**响应示例：**

```json
{
  "lobsters": [
    {
      "id": "1477358529139707934",
      "name": "龙虾看板",
      "tokens": 45070996,
      "messages": 259,
      "level": 4,
      "levelName": "大虾",
      "zone": "working",
      "lastActive": "2026-03-12T16:23:21.056Z",
      "size": 140,
      "color": "#9C27B0"
    }
  ],
  "stats": {
    "total": 127,
    "working": 5,
    "resting": 22,
    "idle": 100,
    "totalTokens": 2130055607,
    "avgLevel": "2.1"
  }
}
```

## GET /api/stats

轻量统计数据（不含完整龙虾列表）。

## GET /health

健康检查端点。

**响应：**

```json
{
  "status": "ok",
  "uptime": 12345,
  "lobsters": 127
}
```

## 速率限制

所有 API 端点限制 1次/秒/IP。

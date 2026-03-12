# Lobster Kingdom 🦞

A real-time visualization game that displays Discord channel activity as pixel-art lobsters. Each lobster represents a channel, with size and level determined by token consumption.

![Lobster Kingdom Screenshot](docs/screenshot.png)

## Features

- **Real-time Visualization**: Watch lobsters move between work, rest, and idle zones
- **Level System**: 6 levels from 虾米 (Shrimp) to 虾皇 (Emperor), based on token consumption
- **Pixel Art Style**: Retro 8-bit aesthetic with smooth animations
- **Performance Optimized**: 
  - Gzip compression (78% bandwidth savings)
  - Incremental session file parsing
  - Security headers (XSS, clickjacking protection)
  - Rate limiting on API endpoints
- **Interactive UI**:
  - Fullscreen mode (F key)
  - FPS counter (D key)
  - Activity log with relative timestamps
  - Token consumption rate tracker
  - Mobile responsive

## Quick Start

### Local Development

```bash
# Install dependencies
npm install

# Copy config
cp config.json.example config.json

# Edit config.json to point to your session data directory
# sessionsDir: "./data/sessions"  # or your actual path

# Start server
npm start

# Visit http://localhost:3995
```

### Server Deployment (with FRP tunnel)

```bash
# Copy server config
cp config.server.json config.json

# Edit config.json
# sessionsDir: "/opt/apps/your-app/sessions"

# Start with PM2
pm2 start server.js --name lobster-kingdom

# Setup FRP tunnel (optional)
# See docs/frp-setup.md
```

## Configuration

### config.json

```json
{
  "port": 3995,
  "basePath": "/lobster-kingdom",
  "sessionsDir": "./data/sessions",
  "refreshInterval": 30000,
  "levels": [
    { "name": "虾米", "minTokens": 0, "size": 70, "color": "#888888" },
    { "name": "小虾", "minTokens": 500000, "size": 90, "color": "#4CAF50" },
    { "name": "中虾", "minTokens": 2000000, "size": 110, "color": "#2196F3" },
    { "name": "大虾", "minTokens": 8000000, "size": 140, "color": "#9C27B0" },
    { "name": "虾王", "minTokens": 25000000, "size": 170, "color": "#F44336" },
    { "name": "虾皇", "minTokens": 60000000, "size": 210, "color": "#FFD700" }
  ]
}
```

### Level Thresholds

The default thresholds are based on real-world AI agent token consumption data:

- **虾米 (Lv1)**: 0+ tokens - New/light users
- **小虾 (Lv2)**: 500K+ tokens - A few conversations
- **中虾 (Lv3)**: 2M+ tokens - Light monthly users
- **大虾 (Lv4)**: 8M+ tokens - Medium monthly users
- **虾王 (Lv5)**: 25M+ tokens - Heavy users
- **虾皇 (Lv6)**: 60M+ tokens - Power users

Adjust these in `config.json` to match your usage patterns.

## Data Format

The app expects session files in JSONL format (one JSON object per line):

```jsonl
{"type":"message","message":{"role":"user","content":"...","timestamp":"2026-03-12T07:00:00.000Z","usage":{"totalTokens":1234}}}
{"type":"message","message":{"role":"assistant","content":"...","timestamp":"2026-03-12T07:00:05.000Z","usage":{"totalTokens":5678}}}
```

### Required Fields

- `type`: "message"
- `message.role`: "user" or "assistant"
- `message.timestamp`: ISO 8601 timestamp
- `message.usage.totalTokens`: Token count for this message

### File Naming

- Topic-based: `session-topic-1234567890.jsonl`
- UUID-based: `abc123-def456-ghi789.jsonl` (requires `sessions.json` mapping)

### sessions.json (optional)

Maps session UUIDs to topic IDs:

```json
{
  "discord:channel:1234567890": {
    "sessionId": "abc123-def456-ghi789"
  }
}
```

### channel-names.json (optional)

Maps topic IDs to display names:

```json
{
  "1234567890": "General Discussion",
  "9876543210": "Development"
}
```

## API Endpoints

### GET /api/lobsters

Returns all lobster data.

**Query Parameters:**
- `zone`: Filter by zone (working/resting/idle)
- `level`: Filter by level (0-5)
- `search`: Search by name (max 50 chars)
- `limit`: Limit results (max 500)

**Response:**
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

### GET /api/stats

Lightweight stats-only endpoint.

### GET /health

Health check endpoint.

```json
{
  "ok": true,
  "uptime": 3600.5,
  "memory": "414MB",
  "lobsters": 121,
  "timestamp": "2026-03-12T07:30:00.000Z"
}
```

## Architecture

```
lobster-kingdom/
├── server.js              # Express server (75 lines)
├── config.json            # Configuration
├── routes/
│   └── api.js            # API routes with rate limiting
├── services/
│   └── session-parser.js # Incremental session file parser
└── public/
    ├── index.html        # Main page
    ├── css/style.css     # Pixel art styles
    ├── assets/           # Lobster sprites
    └── js/
        ├── game.js       # Game loop & state
        ├── lobster.js    # Lobster class
        ├── renderer.js   # Canvas rendering
        ├── ui.js         # UI interactions
        ├── leaderboard.js # Ranking display
        └── activity-log.js # Event log
```

## Performance

### Incremental Parsing

The session parser only reads new bytes since the last parse, dramatically improving performance for large session files:

```javascript
// Only read new bytes
const newBytes = fileSize - cached.bytesRead;
const fd = fs.openSync(fp, 'r');
const buf = Buffer.alloc(newBytes);
fs.readSync(fd, buf, 0, newBytes, cached.bytesRead);
```

### Caching

- Result cache: 15s TTL
- File metadata cache: Tracks mtime and bytes read
- Parse stats: Full parses / incremental parses / cache hits

### Compression

Gzip compression reduces API response size by 78% (23KB → 5KB).

## Security

- **Rate Limiting**: 1 request/second per IP
- **Security Headers**: XSS, clickjacking, MIME sniffing protection
- **Input Validation**: Query parameter filtering and sanitization
- **No Sensitive Data**: All paths and names are configurable

## Keyboard Shortcuts

- `F`: Toggle fullscreen
- `D`: Toggle FPS counter

## Browser Support

- Chrome/Edge 90+
- Firefox 88+
- Safari 14+

## License

MIT

## Credits

Inspired by real-world AI agent usage patterns and designed for Discord channel activity visualization.

---

**Note**: This is a visualization tool. Adjust level thresholds in `config.json` to match your specific use case.

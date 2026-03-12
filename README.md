<div align="center">

# 🦞 Lobster Kingdom

**A real-time visualization game that turns Discord channel activity into a living pixel-art aquarium**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Node.js Version](https://img.shields.io/badge/node-%3E%3D16.0.0-brightgreen)](https://nodejs.org/)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)

[Features](#-features) • [Demo](#-demo) • [Quick Start](#-quick-start) • [Documentation](#-documentation) • [Contributing](#-contributing)

![Lobster Kingdom Screenshot](docs/screenshot.png)

*Watch your Discord channels come alive as pixel-art lobsters, sized and colored by their token consumption*

</div>

---

## 🎮 What is Lobster Kingdom?

Lobster Kingdom transforms your Discord server's activity into an interactive pixel-art game. Each channel becomes a lobster that grows and evolves based on AI token consumption, moving between work, rest, and idle zones in real-time.

Perfect for:
- 🤖 **AI Development Teams** - Visualize which channels are most active
- 📊 **Community Managers** - Gamify server engagement
- 🎨 **Pixel Art Enthusiasts** - Enjoy retro 8-bit aesthetics
- 🔧 **DevOps Teams** - Monitor system activity in a fun way

## ✨ Features

### 🎨 Visual & Interactive
- **Pixel Art Style** - Retro 8-bit aesthetic with smooth animations
- **6 Level System** - From 虾米 (Shrimp) to 虾皇 (Emperor)
- **Three Zones** - Work, Rest, and Idle areas with unique behaviors
- **Real-time Updates** - Watch lobsters move and grow live
- **Fullscreen Mode** - Press `F` for immersive experience
- **Mobile Responsive** - Works great on phones and tablets

### ⚡ Performance
- **Incremental Parsing** - Only reads new data, 90%+ faster
- **Gzip Compression** - 78% bandwidth savings (23KB → 5KB)
- **Smart Caching** - 15s result cache with file metadata tracking
- **Rate Limiting** - 1 req/sec per IP to prevent abuse

### 🔒 Security
- **Security Headers** - XSS, clickjacking, MIME sniffing protection
- **Input Validation** - Query parameter filtering and sanitization
- **No Sensitive Data** - All paths and names are configurable
- **Graceful Degradation** - Demo mode when no data available

### 🛠️ Developer Friendly
- **Zero Config Demo** - Works out of the box with fake data
- **Flexible Configuration** - Customize levels, thresholds, colors
- **Clean API** - RESTful endpoints with filtering
- **Lightweight** - Only 2 dependencies (Express + compression)
- **Well Documented** - Comprehensive README and inline comments

## 🚀 Quick Start

### Option 1: Demo Mode (No Setup Required)

```bash
# Clone the repository
git clone https://github.com/yourusername/lobster-kingdom.git
cd lobster-kingdom

# Install dependencies
npm install

# Start server (will use demo data)
npm start

# Visit http://localhost:3995
```

That's it! The app will generate 25 fake lobsters for you to explore.

### Option 2: With Real Data

```bash
# Copy config
cp config.json.example config.json

# Edit config.json to point to your session data
# sessionsDir: "/path/to/your/sessions"

# Start server
npm start
```

See [Data Format](#data-format) for session file requirements.

## 📸 Demo

<div align="center">

### Main View
![Main View](docs/demo-main.png)

### Lobster Details
![Details Card](docs/demo-details.png)

### Activity Log
![Activity Log](docs/demo-log.png)

</div>

## 🎯 Level System

The level thresholds are based on real-world AI agent token consumption data:

| Level | Name | Threshold | Description |
|-------|------|-----------|-------------|
| 🦐 Lv1 | 虾米 | 0+ | New/light users |
| 🦐 Lv2 | 小虾 | 500K+ | A few conversations |
| 🦐 Lv3 | 中虾 | 2M+ | Light monthly users |
| 🦞 Lv4 | 大虾 | 8M+ | Medium monthly users |
| 🦞 Lv5 | 虾王 | 25M+ | Heavy users |
| 👑 Lv6 | 虾皇 | 60M+ | Power users |

Adjust these in `config.json` to match your usage patterns.

## 📊 API Endpoints

### GET /api/lobsters

Returns all lobster data with optional filtering.

**Query Parameters:**
- `zone` - Filter by zone (working/resting/idle)
- `level` - Filter by level (0-5)
- `search` - Search by name (max 50 chars)
- `limit` - Limit results (max 500)

**Example:**
```bash
curl "http://localhost:3995/api/lobsters?zone=working&level=5"
```

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

## 📁 Data Format

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

- **Topic-based**: `session-topic-1234567890.jsonl`
- **UUID-based**: `abc123-def456-ghi789.jsonl` (requires `sessions.json` mapping)

See [Data Format Documentation](docs/data-format.md) for details.

## 🏗️ Architecture

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
    ├── assets/           # Lobster sprites (6 levels)
    └── js/
        ├── game.js       # Game loop & state
        ├── lobster.js    # Lobster class
        ├── renderer.js   # Canvas rendering
        ├── ui.js         # UI interactions
        ├── leaderboard.js # Ranking display
        └── activity-log.js # Event log
```

## 🎨 Customization

### Change Level Thresholds

Edit `config.json`:

```json
{
  "levels": [
    { "name": "虾米", "minTokens": 0, "size": 70, "color": "#888888" },
    { "name": "小虾", "minTokens": 500000, "size": 90, "color": "#4CAF50" }
  ]
}
```

### Add Custom Themes

Modify `public/css/style.css` or create a new theme file.

### Adjust Refresh Rate

Change `refreshInterval` in `config.json` (default: 30000ms).

## 🚢 Deployment

### Local Development

```bash
npm start
```

### Production (PM2)

```bash
pm2 start server.js --name lobster-kingdom
pm2 save
pm2 startup
```

### Docker

```bash
docker build -t lobster-kingdom .
docker run -p 3995:3995 -v /path/to/sessions:/data/sessions lobster-kingdom
```

### With FRP Tunnel

See [FRP Setup Guide](docs/frp-setup.md) for exposing your local instance to the internet.

## ⌨️ Keyboard Shortcuts

- `F` - Toggle fullscreen
- `D` - Toggle FPS counter

## 🌐 Browser Support

- Chrome/Edge 90+
- Firefox 88+
- Safari 14+

## 📚 Documentation

- [Contributing Guide](CONTRIBUTING.md)
- [FRP Tunnel Setup](docs/frp-setup.md)
- [Data Format Specification](docs/data-format.md)
- [API Reference](docs/api.md)

## 🤝 Contributing

We welcome contributions! Please see [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

### Areas for Contribution

- 📈 Historical data trends (charts)
- 🎨 Custom themes and skins
- 🔊 Sound effects
- 🌍 Internationalization (i18n)
- 🧪 Unit tests
- 🐳 Docker/Kubernetes support
- 📱 Mobile app (React Native)

## 🐛 Bug Reports

Found a bug? Please [open an issue](https://github.com/yourusername/lobster-kingdom/issues) with:

- Browser and version
- Node.js version
- Steps to reproduce
- Expected vs actual behavior
- Console errors (if any)

## 📜 License

MIT License - see [LICENSE](LICENSE) for details.

## 🙏 Acknowledgments

- Inspired by real-world AI agent usage patterns
- Level thresholds based on Claude Code and OpenRouter data
- Pixel art style inspired by classic 8-bit games

## ⭐ Star History

[![Star History Chart](https://api.star-history.com/svg?repos=yourusername/lobster-kingdom&type=Date)](https://star-history.com/#yourusername/lobster-kingdom&Date)

---

<div align="center">

**Made with 🦞 by the community**

[Report Bug](https://github.com/yourusername/lobster-kingdom/issues) • [Request Feature](https://github.com/yourusername/lobster-kingdom/issues) • [Discussions](https://github.com/yourusername/lobster-kingdom/discussions)

</div>

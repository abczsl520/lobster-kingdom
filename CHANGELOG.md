# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] - 2026-03-12

### Added
- Initial release of Lobster Kingdom
- Real-time Discord channel activity visualization
- Pixel art style with 6 lobster levels
- Three zones: Work, Rest, and Idle
- Incremental session file parsing (90%+ faster)
- Gzip compression (78% bandwidth savings)
- Security headers (XSS, clickjacking protection)
- Rate limiting (1 req/sec per IP)
- Demo mode for testing without real data
- Fullscreen mode (F key)
- FPS counter (D key)
- Activity log with relative timestamps
- Token consumption rate tracker
- Mobile responsive UI
- RESTful API with filtering
- Health check endpoint
- Comprehensive documentation
- GitHub Actions CI workflow
- Issue templates
- Contributing guidelines

### Performance
- Incremental parsing only reads new bytes
- Result cache with 15s TTL
- File metadata caching
- Smart zone detection (working/resting/idle)

### Security
- Rate limiting per IP
- Input validation and sanitization
- Security headers (5 types)
- No sensitive data exposure
- Graceful error handling

### Documentation
- Complete README with examples
- API reference
- Data format specification
- FRP tunnel setup guide
- Contributing guidelines
- Issue templates

## [Unreleased]

### Planned
- Historical data trends (charts)
- WebSocket for real-time updates
- Custom themes
- Sound effects
- Docker support
- Unit tests
- Internationalization (i18n)

---

[1.0.0]: https://github.com/abczsl520/lobster-kingdom/releases/tag/v1.0.0

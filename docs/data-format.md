# Data Format Specification

Lobster Kingdom reads session data from JSONL files (JSON Lines format - one JSON object per line).

## File Format

### JSONL Structure

Each line must be a valid JSON object:

```jsonl
{"type":"message","message":{"role":"user","content":"Hello","timestamp":"2026-03-12T07:00:00.000Z","usage":{"totalTokens":1234}}}
{"type":"message","message":{"role":"assistant","content":"Hi!","timestamp":"2026-03-12T07:00:05.000Z","usage":{"totalTokens":5678}}}
```

### Required Fields

| Field | Type | Description |
|-------|------|-------------|
| `type` | string | Must be "message" |
| `message.role` | string | "user" or "assistant" |
| `message.timestamp` | string | ISO 8601 timestamp |
| `message.usage.totalTokens` | number | Token count for this message |

### Optional Fields

| Field | Type | Description |
|-------|------|-------------|
| `message.content` | string | Message content (not used for stats) |
| `message.model` | string | Model name (not used for stats) |

## File Naming Conventions

### Topic-Based Files

Format: `session-topic-{TOPIC_ID}.jsonl`

Example: `session-topic-1234567890.jsonl`

The topic ID is extracted from the filename and used as the lobster ID.

### UUID-Based Files

Format: `{UUID}.jsonl`

Example: `abc123-def456-ghi789.jsonl`

Requires a `sessions.json` mapping file in the same directory:

```json
{
  "discord:channel:1234567890": {
    "sessionId": "abc123-def456-ghi789"
  }
}
```

## Channel Names

Create a `channel-names.json` file to map topic IDs to display names:

```json
{
  "1234567890": "General Discussion",
  "9876543210": "Development",
  "1111111111": "Design"
}
```

If this file doesn't exist, channels will be displayed as "频道 #{TOPIC_ID}".

## Example Session File

```jsonl
{"type":"message","message":{"role":"user","content":"What's the weather?","timestamp":"2026-03-12T07:00:00.000Z","usage":{"totalTokens":1234}}}
{"type":"message","message":{"role":"assistant","content":"It's sunny!","timestamp":"2026-03-12T07:00:05.000Z","usage":{"totalTokens":5678}}}
{"type":"message","message":{"role":"user","content":"Thanks!","timestamp":"2026-03-12T07:01:00.000Z","usage":{"totalTokens":890}}}
```

This will result in:
- **Total tokens**: 7802
- **Message count**: 2 (only user messages are counted)
- **Last active**: 2026-03-12T07:01:00.000Z

## Incremental Parsing

Lobster Kingdom uses incremental parsing for performance:

1. On first read, the entire file is parsed
2. File metadata (size, mtime) is cached
3. On subsequent reads, only new bytes are parsed
4. Results are merged with the cache

This makes parsing 90%+ faster for large files.

## Performance Tips

- Keep session files under 10MB for best performance
- Use topic-based naming when possible (faster than UUID lookup)
- Rotate old sessions to archive directories
- Use the `refreshInterval` config to control update frequency

## Validation

The parser is lenient and will skip invalid lines:

- Lines that don't parse as JSON are ignored
- Lines without required fields are ignored
- Malformed timestamps are ignored

This ensures the app keeps running even with corrupted data.

## Testing Your Data

Use the `/health` endpoint to verify your data is being parsed:

```bash
curl http://localhost:3995/health
```

Expected response:

```json
{
  "ok": true,
  "uptime": 3600.5,
  "memory": "414MB",
  "lobsters": 121,
  "timestamp": "2026-03-12T07:30:00.000Z"
}
```

If `lobsters` is 0, check:
1. `sessionsDir` path in `config.json`
2. File naming conventions
3. JSONL format validity
4. Required fields presence

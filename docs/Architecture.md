# 🏗️ 技术架构

## 数据流

```
OpenClaw Session Files (.jsonl)
        ↓
  session-parser.js (增量解析)
        ↓
    lobsterCache (内存缓存)
        ↓
    Express API (/api/lobsters)
        ↓
    Canvas 2D 渲染 (前端)
```

## 核心优化

### 增量解析
- 使用 `bytesRead` 追踪文件读取位置
- 文件增长时只读新增字节
- 文件缩小时触发全量重解析

### 自动命名
- 支持三种模式提取频道名称
- 每分钟自动刷新（冷却机制）
- 同时处理 `-topic-` 和 UUID 格式文件

### 性能
- Gzip 压缩（78% 节省）
- 15秒结果缓存
- 安全响应头（5项）

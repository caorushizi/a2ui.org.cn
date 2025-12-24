# 数据流

消息如何从代理流向 UI。

## 架构

```
代理 (Agent / LLM) → A2UI 生成器 → 传输层 (SSE/WS/A2A)
                                      ↓
客户端 (Stream Reader) → 消息解析器 → 渲染器 → 原生 UI
```

![端到端数据流](../assets/end-to-end-data-flow.png)

## 消息格式

A2UI 定义了一系列描述 UI 的 JSON 消息。当进行流式传输时，这些消息通常被格式化为 **JSON Lines (JSONL)**，其中每一行都是一个完整的 JSON 对象。

```jsonl
{"surfaceUpdate":{"surfaceId":"main","components":[...]}}
{"dataModelUpdate":{"surfaceId":"main","contents":[{"key":"user","valueMap":[{"key":"name","valueString":"Alice"}]}]}}
{"beginRendering":{"surfaceId":"main","root":"root-component"}}
```

**为什么使用这种格式？** 自包含的 JSON 对象序列对流式传输友好，易于 LLM 增量生成，并且对错误具有弹性。

## 生命周示例：餐厅预订

**用户：** "Book a table for 2 tomorrow at 7pm" (明天晚上 7 点预订一张 2 人的桌子)

**1. 代理定义 UI 结构：**

```json
{"surfaceUpdate": {"surfaceId": "booking", "components": [
  {"id": "root", "component": {"Column": {"children": {"explicitList": ["header", "guests-field", "submit-btn"]}}}},
  {"id": "header", "component": {"Text": {"text": {"literalString": "Confirm Reservation"}, "usageHint": "h1"}}},
  {"id": "guests-field", "component": {"TextField": {"label": {"literalString": "Guests"}, "text": {"path": "/reservation/guests"}}}},
  {"id": "submit-btn", "component": {"Button": {"child": "submit-text", "action": {"name": "confirm", "context": [{"key": "details", "value": {"path": "/reservation"}}]}}}}
]}}
```

**2. 代理填充数据：**

```json
{"dataModelUpdate": {"surfaceId": "booking", "path": "/reservation", "contents": [
  {"key": "datetime", "valueString": "2025-12-16T19:00:00Z"},
  {"key": "guests", "valueString": "2"}
]}}
```

**3. 代理发出渲染信号：**

```json
{"beginRendering": {"surfaceId": "booking", "root": "root"}}
```

**4. 用户将人数编辑为 "3"** → 客户端自动更新 `/reservation/guests`（此时没有发送消息给代理）

**5. 用户点击 "Confirm" (确认)** → 客户端发送带有更新数据的操作：

```json
{"userAction": {"name": "confirm", "surfaceId": "booking", "context": {"details": {"datetime": "2025-12-16T19:00:00Z", "guests": "3"}}}}
```

**6. 代理响应** → 更新 UI 或发送 `{"deleteSurface": {"surfaceId": "booking"}}` 以进行清理

## 传输选项

- **A2A 协议**: 多代理系统，也可用于代理到 UI 的通信
- **AG UI**: 双向、实时
- ... 其他

有关详细信息，请参阅 [传输层](../transports.md)。

## 渐进式渲染

不需要等待生成完整的响应后再向用户显示任何内容，响应的块可以在生成时流式传输到客户端并渐进式渲染。

用户可以看到 UI 实时构建，而不是盯着加载微调器。

## 错误处理

**格式错误的消息：** 跳过并继续，或向代理发送错误以进行更正
**网络中断：** 显示错误状态，重新连接，代理重新发送或恢复

## 性能

**批处理 (Batching)：** 缓冲更新 16ms，批量渲染
**差异比较 (Diffing)：** 比较旧/新组件，仅更新更改的属性
**粒度更新 (Granular updates)：** 更新 `/user/name` 而不是整个 `/` 模型
# 传输层 (消息传递)

传输层将 A2UI 消息从代理传递到客户端。A2UI 与传输层无关——可以使用任何能发送 JSON 的方法。

实际的组件渲染由 [渲染器](renderers.md) 完成，
而 [代理](agents.md) 负责生成 A2UI 消息。
将消息从代理传递到客户端是传输层的工作。

## 工作原理

```
代理 (Agent) → 传输层 (Transport) → 客户端渲染器 (Client Renderer)
```

A2UI 定义了一系列 JSON 消息。传输层负责将此序列从代理传递到客户端。一种常见的传输机制是使用诸如 JSON Lines (JSONL) 之类的格式流，其中每一行都是一条单独的 A2UI 消息。

## 可用的传输层

| 传输层 | 状态 | 用例 |
|-----------|--------|----------|
| **A2A 协议** | ✅ 稳定 | 多代理系统，企业网格 |
| **AG UI** | ✅ 稳定 | 全栈 React 应用程序 |
| **REST API** | 📋 计划中 | 简单的 HTTP 端点 |
| **WebSockets** | 💡 提议中 | 实时双向通信 |
| **SSE (Server-Sent Events)** | 💡 提议中 | Web 流式传输 |

## A2A 协议

[Agent2Agent (A2A) 协议](https://a2a-protocol.org) 提供安全、标准化的代理通信。A2A 扩展提供了与 A2UI 的轻松集成。

**优势：**

- 内置安全和身份验证
- 针对多种消息格式、身份验证和传输协议的绑定
- 清晰的关注点分离

如果你正在使用 A2A，这几乎应该是自动完成的。

TODO: 添加详细指南。

**参见：** [A2A 扩展规范](specification/v0.8-a2a-extension.md)

## AG UI

[AG UI](https://ag-ui.com/) 将 A2UI 消息转换为 AG UI 消息，并自动处理传输和状态同步。

如果你正在使用 AG UI，这应该是自动完成的。

TODO: 添加详细指南。

## 自定义传输层

你可以使用任何发送 JSON 的传输方式：

**HTTP/REST:**

```javascript
// TODO: 添加示例
```

**WebSockets:**

```javascript
// TODO: 添加示例
```

**Server-Sent Events:**

```javascript
// TODO: 添加示例
```

```

## 下一步

- **[A2A 协议文档](https://a2a-protocol.org)**: 了解 A2A
- **[A2A 扩展规范](specification/v0.8-a2a-extension.md)**: A2UI + A2A 详情

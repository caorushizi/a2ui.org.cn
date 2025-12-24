# 我该如何使用 A2UI？

选择适合你的角色和用例的集成路径。

## 三条路径

### 路径 1：构建宿主应用程序 (前端)

将 A2UI 渲染集成到你现有的应用中，或构建一个新的代理驱动的前端。

**选择一个渲染器：**

- **Web:** Lit, Angular
- **移动端/桌面端:** Flutter GenUI SDK
- **React:** 2026 年第一季度发布

**快速设置：**

如果我们使用的是 Angular 应用程序，我们可以添加 Angular 渲染器：

```bash
npm install @a2ui/angular 
```

连接到代理消息（SSE, WebSockets, 或 A2A）并自定义样式以匹配你的品牌。

**下一步：** [客户端设置指南](../guides/client-setup.md) | [主题与样式](../guides/theming.md)

---

### 路径 2：构建代理 (后端)

创建一个为任何兼容客户端生成 A2UI 响应的代理。

**选择你的框架：**

- **Python:** Google ADK, LangChain, 自定义
- **Node.js:** A2A SDK, Vercel AI SDK, 自定义

在你的 LLM 提示词中包含 A2UI 模式，生成 JSONL 消息，并通过 SSE、WebSockets 或 A2A 流式传输到客户端。

**下一步：** [代理开发指南](../guides/agent-development.md)

---

### 路径 3：使用现有框架

通过内置支持的框架使用 A2UI：

- **[AG UI / CopilotKit](https://ag-ui.com/)** - 带有 A2UI 渲染的全栈 React 框架
- **[Flutter GenUI SDK](https://docs.flutter.dev/ai/genui)** - 跨平台生成式 UI（内部使用 A2UI）

**下一步：** [代理 UI 生态系统](agent-ui-ecosystem.md) | [A2UI 用在哪里？](where-is-it-used.md)
# 代理 (服务器端)

代理是服务器端程序，它们响应用户请求生成 A2UI 消息。

实际的组件渲染由 [渲染器](renderers.md) 完成，
在消息被 [传输](transports.md) 到客户端之后。
代理仅负责生成 A2UI 消息。

## 代理如何工作

```
用户输入 → 代理逻辑 → LLM → A2UI JSON → 发送到客户端
```

1. **接收** 用户消息
2. **处理** 使用 LLM (Gemini, GPT, Claude 等)
3. **生成** A2UI JSON 消息作为结构化输出
4. **发送** 通过传输层发送到客户端

来自客户端的用户交互可以被视为新的用户输入。

## 示例代理

A2UI 仓库包含你可以学习的示例代理：

- [餐厅查找器 (Restaurant Finder)](https://github.com/google/A2UI/tree/main/samples/agent/adk/restaurant_finder)
    - 带表单的餐桌预订
    - 使用 ADK 编写
- [联系人查找 (Contact Lookup)](https://github.com/google/A2UI/tree/main/samples/agent/adk/contact_lookup)
    - 带结果列表的搜索
    - 使用 ADK 编写
- [Rizzcharts](https://github.com/google/A2UI/tree/main/samples/agent/adk/rizzcharts)
    - A2UI 自定义组件演示
    - 使用 ADK 编写

## 你会使用 A2A 的不同类型的代理

### 1. 面向用户的代理 (独立)

面向用户的代理是用户直接与之交互的代理。

### 2. 作为远程代理宿主的面向用户代理

这是一种模式，其中面向用户的代理是一个或多个远程代理的宿主。面向用户的代理将调用远程代理，远程代理将生成 A2UI 消息。这是 [A2A](https://a2a-protocol.org) 中的常见模式，客户端代理调用服务器代理。

- 面向用户的代理可以“透传” A2UI 消息而不改变它们
- 面向用户的代理可以在将 A2UI 消息发送到客户端之前对其进行修改

### 3. 远程代理

远程代理不是面向用户的 UI 的直接组成部分。相反，它被注册为远程代理，并且可以由面向用户的代理调用。这是 [A2A](https://a2a-protocol.org) 中的常见模式，客户端代理调用服务器代理。

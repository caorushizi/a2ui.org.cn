# 快速入门：5 分钟运行 A2UI

通过运行餐厅查找器演示来亲身体验 A2UI。本指南将让你在不到 5 分钟的时间内体验代理生成的 UI。

## 你将构建什么

在本快速入门结束时，你将拥有：

- ✅ 一个运行 A2UI Lit 渲染器的 Web 应用程序
- ✅ 一个由 Gemini 驱动的生成动态 UI 的代理
- ✅ 一个交互式餐厅查找器，具有表单生成、时间选择和确认流程
- ✅ 了解 A2UI 消息如何从代理流向 UI

## 先决条件

在你开始之前，请确保你拥有：

- **Node.js** (v18 或更高版本) - [在此下载](https://nodejs.org/)
- **一个 Gemini API 密钥** - [从 Google AI Studio 免费获取](https://aistudio.google.com/apikey)

!!! warning "安全须知"
    此演示运行一个 A2A 代理，该代理使用 Gemini 生成 A2UI 响应。代理有权访问你的 API 密钥，并将向 Google 的 Gemini API 发出请求。在生产环境中运行之前，请务必审查代理代码。

## 第一步：克隆仓库

```bash
git clone https://github.com/google/a2ui.git
cd a2ui
```

## 第二步：设置你的 API 密钥

将你的 Gemini API 密钥导出为环境变量：

```bash
export GEMINI_API_KEY="your_gemini_api_key_here"
```

## 第三步：导航到 Lit 客户端

```bash
cd samples/client/lit
```

## 第四步：安装并运行

运行单命令演示启动器：

```bash
npm install
npm run demo:all
```

此命令将：

1. 安装所有依赖项
2. 构建 A2UI 渲染器
3. 启动 A2A 餐厅查找器代理（Python 后端）
4. 启动开发服务器
5. 在浏览器中打开 `http://localhost:5173`

!!! success "演示运行中"
    如果一切正常，你应该会在浏览器中看到 Web 应用程序。代理现在已准备好生成 UI！

## 第五步：试一试

在 Web 应用程序中，尝试这些提示：

1. **"Book a table for 2"** - 观看代理生成预订表单
2. **"Find Italian restaurants near me"** - 查看动态搜索结果
3. **"What are your hours?"** - 体验针对不同意图的不同 UI 布局

### 幕后发生了什么

```
┌─────────────┐         ┌──────────────┐         ┌────────────────┐
│   You Type  │────────>│ A2A Agent    │────────>│  Gemini API    │
│  a Message  │         │  (Python)    │         │  (LLM)         │
└─────────────┘         └──────────────┘         └────────────────┘
                               │                         │
                               │ Generates A2UI JSON     │
                               │<────────────────────────┘
                               │
                               │ Streams JSONL messages
                               v
                        ┌──────────────┐
                        │   Web App    │
                        │ (A2UI Lit    │
                        │  Renderer)   │
                        └──────────────┘
                               │
                               │ Renders native components
                               v
                        ┌──────────────┐
                        │   Your UI    │
                        └──────────────┘
```

1. **你通过 Web UI 发送消息**
2. **A2A 代理** 接收消息并将对话发送给 Gemini
3. **Gemini 生成** 描述 UI 的 A2UI JSON 消息
4. **A2A 代理流式传输** 这些消息回 Web 应用程序
5. **A2UI 渲染器** 将它们转换为原生 Web 组件
6. **你看到 UI** 在你的浏览器中渲染

## A2UI 消息剖析

让我们看看代理发送了什么。这是 JSON 消息的简化示例：

### 定义 UI

```json
{
  "surfaceUpdate": {
    "surfaceId": "main",
    "components": [
      {
        "id": "header",
        "component": {
          "Text": {
            "text": {"literalString": "Book Your Table"},
            "usageHint": "h1"
          }
        }
      },
      {
        "id": "date-picker",
        "component": {
          "DateTimeInput": {
            "label": {"literalString": "Select Date"},
            "value": {"path": "/reservation/date"},
            "enableDate": true
          }
        }
      },
      {
        "id": "submit-btn",
        "component": {
          "Button": {
            "child": "submit-text",
            "action": {"name": "confirm_booking"}
          }
        }
      },
      {
        "id": "submit-text",
        "component": {
          "Text": {"text": {"literalString": "Confirm Reservation"}}
        }
      }
    ]
  }
}
```

这定义了界面的 UI 组件：一个文本标题、一个日期选择器和一个按钮。

### 填充数据

```json
{
  "dataModelUpdate": {
    "surfaceId": "main",
    "contents": [
      {
        "key": "reservation",
        "valueMap": [
          {"key": "date", "valueString": "2025-12-15"},
          {"key": "time", "valueString": "19:00"},
          {"key": "guests", "valueInt": 2}
        ]
      }
    ]
  }
}
```

这将填充组件可以绑定的数据模型。

### 信号渲染

```json
{"beginRendering": {"surfaceId": "main", "root": "header"}}
```

这告诉客户端它有足够的信息来渲染 UI。

!!! tip "这只是 JSON"
    注意到这有多易读和结构化了吗？LLM 可以轻松生成它，并且可以安全地传输和渲染——无需执行代码。

## 探索其他演示

该仓库包含其他几个演示：

### 组件库（无需代理）

查看所有可用的 A2UI 组件：

```bash
npm start -- gallery
```

这将运行一个仅客户端的演示，展示每个标准组件（Card, Button, TextField, Timeline 等），并附带实时示例和代码示例。

### 联系人查找演示

尝试不同的代理用例：

```bash
npm run demo:contact
```

这演示了一个联系人查找代理，它生成搜索表单和结果列表。

## 下一步是什么？

现在你已经看到了 A2UI 的实际效果，你可以准备：

- **[学习核心概念](concepts/overview.md)**：了解界面、组件和数据绑定
- **[设置你自己的客户端](guides/client-setup.md)**：将 A2UI 集成到你自己的应用中
- **[构建代理](guides/agent-development.md)**：创建生成 A2UI 响应的代理
- **[探索协议](reference/messages.md)**：深入了解技术规范

## 故障排除

### 端口已被占用

如果端口 5173 已被占用，开发服务器将自动尝试下一个可用端口。检查终端输出以获取实际 URL。

### API 密钥问题

如果你看到有关缺少 API 密钥的错误：

1. 验证密钥是否已导出：`echo $GEMINI_API_KEY`
2. 确保它是来自 [Google AI Studio](https://aistudio.google.com/apikey) 的有效 Gemini API 密钥
3. 尝试重新导出：`export GEMINI_API_KEY="your_key"`

### Python 依赖项

该演示使用 Python 作为 A2A 代理。如果你遇到 Python 错误：

```bash
# 确保安装了 Python 3.10+
python3 --version

# 演示应通过 npm 脚本自动安装依赖项
# 如果没有，请手动安装它们：
cd ../../agent/adk/restaurant_finder
pip install -r requirements.txt
```

### 仍有问题？

- 检查 [GitHub Issues](https://github.com/google/a2ui/issues)
- 查看 [samples/client/lit/README.md](https://github.com/google/a2ui/tree/main/samples/client/lit)
- 加入社区讨论

## 理解演示代码

想看看它是如何工作的？查看：

- **代理代码**: `samples/agent/adk/restaurant_finder/` - Python A2A 代理
- **客户端代码**: `samples/client/lit/` - 带 A2UI 渲染器的 Lit Web 客户端
- **A2UI 渲染器**: `web-lib/` - Web 渲染器实现

每个目录都有自己的 README，其中包含详细的文档。

---

**祝贺！** 你已成功运行了你的第一个 A2UI 应用程序。你已经看到了 AI 代理如何通过安全的、声明式的 JSON 消息生成丰富的、交互式的 UI，并在 Web 应用程序中原生渲染。
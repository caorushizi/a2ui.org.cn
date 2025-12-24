# A2UI 用在哪里？

A2UI 正被 Google 的团队和合作伙伴组织采用，用于构建下一代代理驱动的应用程序。以下是 A2UI 正在发挥作用的真实示例。

## 生产部署

### Google Opal: 适合所有人的 AI 迷你应用

[Opal](http://opal.google) 使成千上万的人能够使用自然语言构建、编辑和分享 AI 迷你应用——无需编码。

**Opal 如何使用 A2UI:**

Google 的 Opal 团队从一开始就是 **A2UI 的核心贡献者**。他们使用 A2UI 来驱动动态的、生成式的 UI 系统，从而使 Opal 的 AI 迷你应用成为可能。

- **快速原型设计**: 快速构建和测试新的 UI 模式
- **用户生成的应用**: 任何人都可以创建带有自定义 UI 的应用
- **动态界面**: UI 会自动适应每个用例

> "A2UI 是我们工作的基础。它给了我们灵活性，让 AI 以新颖的方式驱动用户体验，而不受固定前端的限制。它的声明性质和对安全性的关注使我们能够快速、安全地进行实验。"
>
> **— Dimitri Glazkov**, Principal Engineer, Opal Team

**了解更多:** [opal.google](http://opal.google)

---

### Gemini Enterprise: 面向企业的自定义代理

Gemini Enterprise 使企业能够构建强大的、自定义的 AI 代理，专门针对其特定的工作流程和数据。

**Gemini Enterprise 如何使用 A2UI:**

A2UI 正在被集成，以允许企业代理在业务应用程序中渲染 **丰富的、交互式的 UI**——超越简单的文本响应，引导员工完成复杂的工作流程。

- **数据输入表单**: 用于结构化数据收集的 AI 生成表单
- **审批仪表板**: 用于审查和审批流程的动态 UI
- **工作流自动化**: 用于复杂任务的分步界面
- **自定义企业 UI**: 针对行业特定需求的定制界面

> "我们的客户需要他们的代理不仅仅是回答问题；他们需要代理引导员工完成复杂的工作流程。A2UI 将允许在 Gemini Enterprise 上构建的开发人员让他们的代理生成任何任务所需的动态、自定义 UI，从数据输入表单到审批仪表板，从而显着加速工作流自动化。"
>
> **— Fred Jabbour**, Product Manager, Gemini Enterprise

**了解更多:** [Gemini Enterprise](https://cloud.google.com/gemini)

---

### Flutter GenUI SDK: 移动端的生成式 UI

[Flutter GenUI SDK](https://docs.flutter.dev/ai/genui) 将动态的、AI 生成的 UI 带到了跨移动端、桌面端和 Web 的 Flutter 应用程序中。

**GenUI 如何使用 A2UI:**

GenUI SDK 使用 **A2UI 作为底层协议**，用于服务器端代理和 Flutter 应用程序之间的通信。当你使用 GenUI 时，你在底层使用的是 A2UI。

- **跨平台支持**: 同一个代理在 iOS, Android, Web, 桌面端上工作
- **原生性能**: Flutter 小部件在每个平台上原生渲染
- **品牌一致性**: UI 匹配你的应用设计系统
- **服务器驱动的 UI**: 代理控制显示内容，无需应用更新

> "我们的开发人员选择 Flutter，是因为它让他们能够快速创建富有表现力、品牌丰富、在每个平台上都感觉棒极了的自定义设计系统。A2UI 非常适合 Flutter 的 GenUI SDK，因为它确保每个平台上的每个用户都能获得高质量的原生感觉体验。"
>
> **— Vijay Menon**, Engineering Director, Dart & Flutter

**试一试:**
- [GenUI 文档](https://docs.flutter.dev/ai/genui)
- [入门视频](https://www.youtube.com/watch?v=nWr6eZKM6no)
- [Verdure 示例](https://github.com/flutter/genui/tree/main/examples/verdure) (客户端-服务器 A2UI 示例)

---

## 合作伙伴集成

### AG UI / CopilotKit: 全栈代理框架

[AG UI](https://ag-ui.com/) 和 [CopilotKit](https://www.copilotkit.ai/) 提供了一个用于构建代理应用程序的综合框架，具有 **零日 A2UI 兼容性**。

**它们如何协同工作:**

AG UI 擅长在自定义前端与其专用代理之间创建高带宽连接。通过添加 A2UI 支持，开发人员可以两全其美：

- **状态同步**: AG UI 处理应用状态和聊天记录
- **A2UI 渲染**: 渲染来自第三方代理的动态 UI
- **多代理支持**: 协调来自多个代理的 UI
- **React 集成**: 与 React 应用程序的无缝集成

> "AG UI 擅长在自定义构建的前端与其专用代理之间创建高带宽连接。通过添加对 A2UI 的支持，我们为开发人员提供了两全其美的方案。他们现在可以构建丰富的、状态同步的应用程序，还可以通过 A2UI 渲染来自第三方代理的动态 UI。这是多代理世界的完美匹配。"
>
> **— Atai Barkai**, Founder of CopilotKit and AG UI

**了解更多:**
- [AG UI](https://ag-ui.com/)
- [CopilotKit](https://www.copilotkit.ai/)

---

### Google 的 AI 驱动产品

随着 Google 在全公司范围内采用 AI，A2UI 提供了一种 **标准化的方式让 AI 代理交换用户界面**，而不仅仅是文本。

**内部代理采用:**

- **多代理工作流**: 多个代理为同一个界面做出贡献
- **远程代理支持**: 运行在不同服务上的代理可以提供 UI
- **标准化**: 跨团队的通用协议减少了集成开销
- **外部暴露**: 内部代理可以轻松地对外暴露（例如，Gemini Enterprise）

> "就像 A2A 让任何代理都可以与另一个代理对话一样，无论平台如何，A2UI 标准化了用户界面层，并通过编排器支持远程代理用例。这对内部团队来说非常强大，使他们能够快速开发代理，其中丰富的用户界面是常态，而不是例外。随着 Google 进一步推进生成式 UI，A2UI 提供了一个完美的平台，用于在任何客户端上渲染的服务器驱动 UI。"
>
> **— James Wren**, Senior Staff Engineer, AI Powered Google

---

## 社区项目

A2UI 社区正在构建令人兴奋的项目：

### 开源示例

- **餐厅查找器 (Restaurant Finder)** ([samples/agent/adk/restaurant_finder](https://github.com/google/A2UI/tree/main/samples/agent/adk/restaurant_finder))
  - 带有动态表单的餐桌预订
  - Gemini 驱动的代理
  - 提供完整源代码

- **联系人查找 (Contact Lookup)** ([samples/agent/adk/contact_lookup](https://github.com/google/A2UI/tree/main/samples/agent/adk/contact_lookup))
  - 带有结果列表的搜索界面
  - A2A 代理示例
  - 演示数据绑定

- **组件库 (Component Gallery)** ([samples/client/angular - gallery mode](https://github.com/google/A2UI/tree/main/samples/client/angular))
  - 所有组件的交互式展示
  - 带有代码的实时示例
  - 非常适合学习

### 社区贡献

你用 A2UI 构建了什么吗？[与社区分享！](../community.md)

---

## 下一步

- [快速入门指南](../quickstart.md) - 尝试演示
- [代理开发](../guides/agent-development.md) - 构建代理
- [客户端设置](../guides/client-setup.md) - 集成渲染器
- [社区](../community.md) - 加入社区

---

**在生产环境中使用 A2UI？** 在 [GitHub Discussions](https://github.com/google/A2UI/discussions) 上分享你的故事。
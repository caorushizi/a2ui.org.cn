---
hide:
  - toc
---

<!-- markdownlint-disable MD041 -->
<!-- markdownlint-disable MD033 -->
<div style="text-align: center; margin: 2rem 0 3rem 0;" markdown>

<!-- Logo for Light Mode (shows dark logo on light background) -->
<img src="assets/A2UI_dark.svg" alt="A2UI Logo" width="120" class="light-mode-only" style="margin-bottom: 1rem;">
<!-- Logo for Dark Mode (shows light logo on dark background) -->
<img src="assets/A2UI_light.svg" alt="A2UI Logo" width="120" class="dark-mode-only" style="margin-bottom: 1rem;">

# 代理驱动界面的协议 (A Protocol for Agent-Driven Interfaces)

<p style="font-size: 1.2rem; max-width: 800px; margin: 0 auto 1rem auto; opacity: 0.9; line-height: 1.6;">
A2UI 使 AI 代理能够生成丰富的、交互式的用户界面，并在 Web、移动端和桌面上原生渲染——无需执行任意代码。
</p>

</div>

!!! warning "️状态: 早期公开预览"
    A2UI 目前处于 **v0.8 (公开预览版)**。规范和实现功能正常，但仍在不断演进。我们开放此项目是为了促进合作、收集反馈并征集贡献（例如，在客户端渲染器方面）。
    请预期会有变动。

## 概览

A2UI 目前版本为 [v0.8](specification/v0.8-a2ui.md)，
采用 Apache 2.0 许可，
由 Google 创建，并由 CopilotKit 和开源社区贡献，
目前在 [GitHub](https://github.com/google/A2UI) 上积极开发中。

A2UI 解决的问题是：**AI 代理如何安全地跨越信任边界发送丰富的 UI？**

A2UI 不再使用纯文本响应或危险的代码执行，而是允许代理发送 **声明式的组件描述**，客户端使用其自己的原生小部件进行渲染。这就像让代理说一种通用的 UI 语言。

在这个仓库中，你会发现
[A2UI 规范](specification/v0.8-a2ui.md)
和客户端的 [渲染器](renderers.md) 实现（例如：Angular, Flutter 等），
以及在代理和客户端之间传递 A2UI 消息的 [传输层](/transports.md)（例如：A2A 等）。

<div class="grid cards" markdown>

- :material-shield-check: **设计安全**

    ---

    声明式数据格式，而非可执行代码。代理只能使用你的目录中预先批准的组件——没有 UI 注入攻击。

- :material-rocket-launch: **对 LLM 友好**

    ---

    扁平的、流式的 JSON 结构，专为易于生成而设计。LLM 可以增量构建 UI，而无需一次性生成完美的 JSON。

- :material-devices: **框架无关**

    ---

    一个代理响应随处可用。在 Angular、Flutter、React 或原生移动应用上，使用你自己的样式化组件渲染相同的 UI。

- :material-chart-timeline: **渐进式渲染**

    ---

    随着生成的进行流式传输 UI 更新。用户可以实时看到界面的构建，而无需等待完整的响应。

</div>

## 5 分钟快速入门

<div class="grid cards" markdown>

- :material-clock-fast:{ .lg .middle } **[快速入门指南](quickstart.md)**

    ---

    运行餐厅查找器演示，查看 A2UI 与 Gemini 驱动的代理的实际效果。

    [:octicons-arrow-right-24: 开始使用](quickstart.md)

- :material-book-open-variant:{ .lg .middle } **[核心概念](concepts/overview.md)**

    ---

    了解界面 (Surfaces)、组件 (Components)、数据绑定 (Data Binding) 和邻接表模型 (Adjacency List Model)。

    [:octicons-arrow-right-24: 学习概念](concepts/overview.md)

- :material-code-braces:{ .lg .middle } **[开发者指南](guides/client-setup.md)**

    ---

    将 A2UI 渲染器集成到你的应用中，或构建生成 UI 的代理。

    [:octicons-arrow-right-24: 开始构建](guides/client-setup.md)

- :material-file-document:{ .lg .middle } **[协议参考](specification/v0.8-a2ui.md)**

    ---

    深入了解完整的技术规范和消息类型。

    [:octicons-arrow-right-24: 阅读规范](specification/v0.8-a2ui.md)

</div>

## 工作原理

1. **用户发送消息** 给 AI 代理
2. **代理生成 A2UI 消息** 描述 UI（结构 + 数据）
3. **消息流式传输** 到客户端应用程序
4. **客户端渲染** 使用原生组件（Angular, Flutter, React 等）
5. **用户交互** 与 UI 进行交互，将操作发送回代理
6. **代理响应** 并更新 A2UI 消息

![端到端数据流](assets/end-to-end-data-flow.png)

## A2UI 实战

### 景观设计师演示

<div style="margin: 2rem 0;">
  <div style="border-radius: .8rem; overflow: hidden; box-shadow: var(--md-shadow-z2);">
    <video width="100%" height="auto" controls playsinline style="display: block; aspect-ratio: 16/9; object-fit: cover;">
      <source src="assets/landscape-architect-demo.mp4" type="video/mp4">
      您的浏览器不支持 video 标签。
    </video>
  </div>
  <p style="text-align: center; margin-top: 1rem; opacity: 0.8;">
    观看一个代理为景观设计师应用程序生成所有界面。用户上传一张照片；代理使用 Gemini 理解它并生成一个定制的景观需求表单。
  </p>
</div>

### 自定义组件：交互式图表与地图

<div style="margin: 2rem 0;">
  <div style="border-radius: .8rem; overflow: hidden; box-shadow: var(--md-shadow-z2);">
    <video width="100%" height="auto" controls playsinline style="display: block; aspect-ratio: 16/9; object-fit: cover;">
      <source src="assets/a2ui-custom-compnent.mp4" type="video/mp4">
      您的浏览器不支持 video 标签。
    </video>
  </div>
  <p style="text-align: center; margin-top: 1rem; opacity: 0.8;">
    观看代理选择响应一个图表组件来回答数值摘要问题。然后代理选择一个 Google 地图组件来回答位置问题。这两者都是客户端提供的自定义组件。
  </p>
</div>

### A2UI Composer

CopilotKit 也有一个公开的 [A2UI Widget Builder](https://go.copilotkit.ai/A2UI-widget-builder) 供尝试。

[![A2UI Composer](assets/A2UI-widget-builder.png)](https://go.copilotkit.ai/A2UI-widget-builder)
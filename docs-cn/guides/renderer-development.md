# A2UI 渲染器实现指南

本文档概述了基于 0.8 版规范的 A2UI 协议新渲染器实现所需的功能。它面向构建新渲染器（例如，用于 React、Flutter、iOS 等）的开发人员。

## I. 核心协议实现清单

本节详细介绍了 A2UI 协议的基本机制。合规的渲染器必须实现这些系统，以成功解析服务器流、管理状态并处理用户交互。

### 消息处理与状态管理

- **JSONL 流解析**: 实现一个解析器，可以逐行读取流式响应，将每一行解码为不同的 JSON 对象。
- **消息分发器**: 创建一个分发器以识别消息类型（`beginRendering`、`surfaceUpdate`、`dataModelUpdate`、`deleteSurface`）并将其路由到正确的处理程序。
- **界面管理**:
  - 实现一个数据结构来管理多个 UI 界面，每个界面都以其 `surfaceId` 为键。
  - 处理 `surfaceUpdate`: 在指定界面的组件缓冲区中添加或更新组件。
  - 处理 `deleteSurface`: 移除指定的界面及其所有相关数据和组件。
- **组件缓冲（邻接表）**:
  - 对于每个界面，维护一个组件缓冲区（例如 `Map<String, Component>`），以按 `id` 存储所有组件定义。
  - 能够通过解析容器组件（`children.explicitList`、`child`、`contentChild` 等）中的 `id` 引用，在渲染时重建 UI 树。
- **数据模型存储**:
  - 对于每个界面，维护一个单独的数据模型存储（例如 JSON 对象或 `Map<String, any>`）。
  - 处理 `dataModelUpdate`: 在指定的 `path` 更新数据模型。`contents` 将采用邻接表格式（例如 `[{ "key": "name", "valueString": "Bob" }]`）。

### 渲染逻辑

- **渐进式渲染控制**:
  - 缓冲所有传入的 `surfaceUpdate` 和 `dataModelUpdate` 消息，而不立即渲染。
  - 处理 `beginRendering`: 此消息作为执行界面初始渲染并设置根组件 ID 的显式信号。
    - 从指定的 `root` 组件 ID 开始渲染。
    - 如果提供了 `catalogId`，确保使用相应的组件目录（如果省略，则默认为标准目录）。
    - 应用此消息中提供的任何全局 `styles`（例如 `font`、`primaryColor`）。
- **数据绑定解析**:
  - 为组件属性中发现的 `BoundValue` 对象实现解析器。
  - 如果仅存在 `literal*` 值（`literalString`、`literalNumber` 等），则直接使用它。
  - 如果仅存在 `path`，则根据界面的数据模型对其进行解析。
  - 如果同时存在 `path` 和 `literal*`，首先使用字面量值更新 `path` 处的数据模型，然后将组件属性绑定到该 `path`。
- **动态列表渲染**:
  - 对于带有 `children.template` 的容器，迭代在 `template.dataBinding` 处找到的数据列表（解析为数据模型中的列表）。
  - 对于数据列表中的每个项目，渲染由 `template.componentId` 指定的组件，使该项目的数据可用于模板内的相对数据绑定。

### 客户端到服务器通信

- **事件处理**:
  - 当用户与定义了 `action` 的组件交互时，构建 `userAction` 负载。
  - 根据数据模型解析 `action.context` 中的所有数据绑定。
  - 将完整的 `userAction` 对象发送到服务器的事件处理端点。
- **客户端能力报告**:
  - 在发送到服务器的 **每条** A2A 消息（作为元数据的一部分）中，包含一个 `a2uiClientCapabilities` 对象。
  - 此对象应通过 `supportedCatalogIds` 声明你的客户端支持的组件目录（例如，包括标准 0.8 目录的 URI）。
  - 可选地，如果服务器支持，提供 `inlineCatalogs` 用于自定义的、即时的组件定义。
- **错误报告**: 实现一种机制，向服务器发送 `error` 消息以报告任何客户端错误（例如，数据绑定失败、未知组件类型）。

## II. 标准组件目录清单

为了确保跨平台的一致用户体验，A2UI 定义了一组标准组件。你的客户端应将这些抽象定义映射到其相应的原生 UI 小部件。

### 基础内容

- **Text**: 渲染文本内容。必须支持 `text` 上的数据绑定以及用于样式的 `usageHint` (h1-h5, body, caption)。
- **Image**: 从 URL 渲染图像。必须支持 `fit` (cover, contain 等) 和 `usageHint` (avatar, hero 等) 属性。
- **Icon**: 从目录中指定的标准集中渲染预定义图标。
- **Video**: 为给定的 URL 渲染视频播放器。
- **AudioPlayer**: 为给定的 URL 渲染音频播放器，可选择带有描述。
- **Divider**: 渲染视觉分隔符，支持 `horizontal` 和 `vertical` 轴。

### 布局与容器

- **Row**: 水平排列子级。必须支持 `distribution` (justify-content) 和 `alignment` (align-items)。子级可以有 `weight` 属性来控制 flex-grow 行为。
- **Column**: 垂直排列子级。必须支持 `distribution` 和 `alignment`。子级可以有 `weight` 属性来控制 flex-grow 行为。
- **List**: 渲染可滚动的项目列表。必须支持 `direction` (`horizontal`/`vertical`) 和 `alignment`。
- **Card**: 一个容器，用于在视觉上对其子内容进行分组，通常带有边框、圆角和/或阴影。具有单个 `child`。
- **Tabs**: 一个显示一组选项卡的容器。包括 `tabItems`，每个项目都有一个 `title` 和一个 `child`。
- **Modal**: 一个显示在主要内容之上的对话框。它由 `entryPointChild`（例如按钮）触发，并在激活时显示 `contentChild`。

### 交互与输入组件

- **Button**: 一个可点击的元素，触发 `userAction`。必须能够包含 `child` 组件（通常是 Text 或 Icon），并且样式可能会根据 `primary` 布尔值而变化。
- **CheckBox**: 一个可以切换的复选框，反映布尔值。
- **TextField**: 文本输入字段。必须支持 `label`、`text`（值）、`textFieldType` (`shortText`, `longText`, `number`, `obscured`, `date`) 和 `validationRegexp`。
- **DateTimeInput**: 用于选择日期和/或时间的专用输入。必须支持 `enableDate` 和 `enableTime`。
- **MultipleChoice**: 用于从列表 (`options`) 中选择一个或多个选项的组件。必须支持 `maxAllowedSelections` 并将 `selections` 绑定到列表或单个值。
- **Slider**: 用于从定义的范围 (`minValue`, `maxValue`) 中选择数值 (`value`) 的滑块。
# A2UI 协议演进指南：v0.8.1 到 v0.9

本文档作为 A2UI 0.8 版和 0.9 版之间更改的综合指南。它详细介绍了理念、架构和实现方面的转变，为在版本之间迁移的利益相关者和开发人员提供了参考。

## 1. 执行摘要

**0.9 版代表了从“结构化输出优先”到“提示词优先”的根本性理念转变。**

- **v0.8.1** 旨在由 LLM 使用结构化输出（Structured Output）生成，最重要的是针对支持严格 JSON 模式或函数调用（这也是结构化输出的一种形式）的 LLM 进行了优化。它依赖于深度嵌套和特定的包装结构，这些结构可以在有限的模式功能中定义，但通常会让 LLM 难以生成。
- **v0.9** 旨在 **直接嵌入到 LLM 的系统提示词中**。模式经过重构，更具可读性，并且对于模型理解来说更“节省 Token”。它优先考虑 LLM 自然擅长的模式（如用于映射的标准 JSON 对象），而不是严格的结构化输出友好结构（如键值对数组）。

### 摘要表

| 特性 | v0.8.1 | v0.9 |
| :--- | :--- | :--- |
| **理念** | 结构化输出 / 函数调用 | 提示词优先 / 上下文模式 |
| **消息类型** | `beginRendering`, `surfaceUpdate`, ... | `createSurface`, `updateComponents`, ... |
| **界面创建** | 显式 `beginRendering` | 显式 `createSurface` |
| **组件类型** | 基于键的包装器 (`{"Text": ...}`) | 基于属性的鉴别器 (`"component": "Text"`) |
| **数据模型更新** | 键值对数组 | 标准 JSON 对象 |
| **数据绑定** | `dataBinding` / `literalString` | `path` / 原生 JSON 类型 |
| **按钮上下文** | 键值对数组 | 标准 JSON 对象 |
| **辅助规则** | N/A | `standard_catalog_rules.txt` |
| **验证** | 基本模式 | 严格的 `ValidationFailed` 反馈循环 |

## 2. 架构和模式变更

### 2.1. 模块化模式架构

**v0.8.1:**

- 整体化倾向。`server_to_client.json` 通常包含深度定义或依赖于难以分解的复杂 `oneOf` 结构。
- `standard_catalog_definition.json` 存在，但通常是隐式耦合的。

**v0.9:**

- **模块化**: 模式严格拆分为：
  - `common_types.json`: 可重用的原语（ID, 路径, 权重）。
  - `server_to_client.json`: 定义消息类型的“信封”。
  - `standard_catalog_definition.json`: 特定的 UI 组件。
- **优势**: 这允许开发人员将 `standard_catalog_definition.json` 替换为 `custom_catalog.json`，而无需触及核心协议信封。

### 2.2. 严格的消息类型

**v0.8.1:**

- 消息是对象，其中 `surfaceUpdate` 等属性是可选键。
- 验证通常依赖于 "minProperties: 1" 约束。

**v0.9:**

- 在 `server_to_client.json` 中使用顶级 **`oneOf`** 约束。
- **原因**: 这是向 LLM 表达模式的更自然的方式，并且更易于 LLM 推理。对于开发人员来说，这也是一种更自然的阅读形式。

### 2.3. 辅助规则文件

**v0.9:**

- **新工件**: `standard_catalog_rules.txt`.
- **目的**: 包含使用目录模式规则的纯文本提示片段（例如，“Button 必须提供 'action'”）。
- **用法**: 旨在与目录模式一起包含在系统提示词中。
- **原因**: 某些约束（如条件要求或特定属性组合）难以用 JSON Schema 表达或表达起来很冗长，但很容易用自然语言规则向 LLM 表达，并且它可以与目录模式一起打包，以便于为特定目录自定义提示词。

## 3. 协议生命周期变更

### 3.1. `createSurface` 取代 `beginRendering`

**v0.8.1 (`beginRendering`):**

- **显式信号**: 服务器发送 `beginRendering` 消息告诉客户端“我已经发送完初始批次的组件，你现在可以绘制了。”
- **根定义**: 根组件 ID 在此消息中定义。
- **样式信息**: 该消息包含界面的样式信息。

**v0.9 (`createSurface`):**

- **替换**: `beginRendering` 被 `createSurface` **取代**。
- **目的**: `createSurface` 信号通知客户端创建一个新界面并准备渲染。
- **样式信息移除**: `createSurface` **不** 包含样式信息。主题化现在通过客户端样式处理，使其与消息流解耦。
- **根规则**: 规则是：“必须且仅有一个 ID 为 `root` 的组件。” `beginRendering` 中的“root”属性已被移除。只要有一个带有根组件的有效树，客户端就应该进行渲染。
- **新要求**: `createSurface` 现在需要一个 **`catalogId`** (URI) 来显式说明正在使用哪个组件集。

**示例:**

**v0.8.1 (`beginRendering`)**:
```json
{
  "beginRendering": {
    "surfaceId": "user_profile_card",
    "root": "root",
    "styles": {
      "primaryColor": "#007bff",
    }
  }
}
```

**v0.9 (`createSurface`)**:
```json
{
  "createSurface": {
    "surfaceId": "user_profile_card",
    "catalogId": "https://a2ui.dev/specification/0.9/standard_catalog_definition.json"
  }
}
```

## 4. 消息结构比较

### 4.1. 组件更新

**v0.8.1 (`surfaceUpdate`)**:

- 组件包装在一个对象中，其中 **键** 是组件类型。
- **结构**: `{ "id": "...", "component": { "Text": { "text": "..." } } }`

**v0.9 (`updateComponents`)**:

- **重命名**: `surfaceUpdate` -> `updateComponents`.
- **重构**: 组件使用带有常量鉴别器属性 `component` 的扁平化结构。
- **结构**: `{ "id": "...", "component": "Text", "text": "..." }`
- **原因**: 这种带有鉴别器字段 (`component: "Text"`) 的“扁平”结构比动态键 (`"Text": {...}`) 更容易让 LLM 一致地生成。这也简化了许多 JSON 解析器中的多态性。

指定未知的 surfaceId 将导致错误。建议客户端在内部实现命名空间方案，以防止单独的代理创建具有相同 ID 的界面，并防止代理修改其他代理创建的界面。

#### 并排示例

**v0.8.1:**

```json
{
  "surfaceUpdate": {
    "surfaceId": "main",
    "components": [
      {
        "id": "title",
        "component": {
          "Text": { "text": { "literalString": "Hello" } }
        }
      }
    ]
  }
}
```

**v0.9:**

```json
{
  "updateComponents": {
    "surfaceId": "main",
    "components": [
      {
        "id": "root",
        "component": "Column",
        "children": ["title"]
      },
      {
        "id": "title",
        "component": "Text",
        "text": "Hello"
      }
    ]
  }
}
```

### 4.2. 数据模型更新

**v0.8.1 (`dataModelUpdate`)**:

- **邻接表**: `contents` 属性是键值对对象的 **数组**。
- **类型化值**: 每个条目需要显式类型，如 `valueString`, `valueNumber`, `valueBoolean`。
- **结构**: `[{ "key": "name", "valueString": "Alice" }]`

**v0.9 (`updateDataModel`)**:

- **重命名**: `dataModelUpdate` -> `updateDataModel`.
- **标准 JSON**: `value` 属性现在是标准的 **JSON 对象**。
- **Op**: 添加了 `op` 属性以允许更复杂的更新（例如 `replace`, `remove`）。
- **结构**: `{ "name": "Alice" }`
- **原因**: LLM 经过训练可以生成 JSON 对象。强迫它们生成映射的“邻接表”表示既低效又容易出错。

## 5. 数据绑定与状态

### 5.1. `path` 的标准化

**v0.8.1:**

- 在 `childrenProperty` 模板中使用 `dataBinding`。
- 在 `BoundValue` 对象中使用 `path`。
- 术语不一致。

**v0.9:**

- **统一**: 所有内容现在都是 **`path`**。
- **原因**: 减少 LLM 的认知负荷。“Path” 总是意味着“数据的 JSON Pointer”。

### 5.2. 简化的绑定值

**v0.8.1:**

- `{ "literalString": "foo" }` 或 `{ "path": "/foo" }`。
- 键中的显式类型 (`literalNumber`, `literalBoolean`)。

**v0.9:**

- **隐式类型**: `stringOrPath`, `numberOrPath` 等在 `common_types.json` 中定义。
- **结构**: 模式允许 `string` 或 `{ "path": "..." }`。
- **原因**: 更自然的 JSON。`{ "text": "Hello" }` 是有效的。`{ "text": { "path": "/msg" } }` 是有效的。不需要 `{ "text": { "literalString": "Hello" } }`。

## 6. 组件特定的变更

### 6.1. 按钮上下文

**v0.8.1:**

- **对数组**: `context: [{ "key": "id", "value": "123" }]`
- **原因**: 易于解析，难以生成。

**v0.9:**

- **标准映射**: `context: { "id": "123" }`
- **原因**: Token 效率。LLM 原生地将 JSON 对象理解为映射。

### 6.2. TextField

**v0.8.1:**

- 属性: `textFieldType` (例如 "email", "password")。

**v0.9:**

- 属性: **`usageHint`**.
- **原因**: 与已使用 `usageHint` 的 `Text` 和 `Image` 组件保持一致。

### 6.3. ChoicePicker (vs MultipleChoice)

**v0.8.1:**

- 组件: **`MultipleChoice`**.
- 属性: `selections` (数组), `maxAllowedSelections` (整数).

**v0.9:**

- 组件: **`ChoicePicker`**.
- 属性: **`value`** (数组), **`usageHint`** (枚举: `multipleSelection`, `mutuallyExclusive`)。`maxAllowedSelections` 属性已被移除。
- **原因**: `ChoicePicker` 是一个更通用的名称，涵盖了单选按钮（互斥）和复选框（多选）。`usageHint` 控制行为，简化了组件表面积。

### 6.4. Slider

**v0.8.1:**

- 属性: `minValue`, `maxValue`.

**v0.9:**

- 属性: **`min`**, **`max`**.
- **原因**: 标准化为更短、更常见的属性名称。

## 7. 错误处理

**v0.9** 在 `client_to_server.json` 中引入了严格的 **`ValidationFailed`** 错误格式。

- **目的**: 使“提示-生成-验证”循环有效工作。
- **机制**: 如果 LLM 生成无效的 JSON，系统将发回结构化错误：
  ```json
  {
    "error": {
      "code": "VALIDATION_FAILED",
      "surfaceId": "...",
      "path": "/components/0/text",
      "message": "Expected string, got number"
    }
  }
  ```
- **结果**: LLM 看到此信息并可以在下一轮中“自我纠正”。
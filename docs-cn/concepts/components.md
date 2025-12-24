# 组件与结构

A2UI 使用 **邻接表模型** 来表示组件层级结构。组件不是嵌套的 JSON 树，而是带有 ID 引用的扁平列表。

## 为什么使用扁平列表？

**传统的嵌套方法：**
- LLM 必须一次性生成完美的嵌套结构
- 难以更新深层嵌套的组件
- 难以进行增量流式传输

**A2UI 邻接表：**
- ✅ 扁平结构，易于 LLM 生成
- ✅ 增量发送组件
- ✅ 通过 ID 更新任何组件
- ✅ 结构与数据清晰分离

## 邻接表模型

```json
{
  "surfaceUpdate": {
    "components": [
      {"id": "root", "component": {"Column": {"children": {"explicitList": ["greeting", "buttons"]}}}},
      {"id": "greeting", "component": {"Text": {"text": {"literalString": "Hello"}}}},
      {"id": "buttons", "component": {"Row": {"children": {"explicitList": ["cancel-btn", "ok-btn"]}}}},
      {"id": "cancel-btn", "component": {"Button": {"child": "cancel-text", "action": {"name": "cancel"}}}},
      {"id": "cancel-text", "component": {"Text": {"text": {"literalString": "Cancel"}}}},
      {"id": "ok-btn", "component": {"Button": {"child": "ok-text", "action": {"name": "ok"}}}},
      {"id": "ok-text", "component": {"Text": {"text": {"literalString": "OK"}}}}
    ]
  }
}
```

组件通过 ID 引用子级，而不是通过嵌套。

## 组件基础

每个组件都有：

1. **ID**: 唯一标识符 (`"welcome-message"`)
2. **类型 (Type)**: 组件类型 (`Text`, `Button`, `Card`)
3. **属性 (Properties)**: 特定于该类型的配置

```json
{"id": "welcome", "component": {"Text": {"text": {"literalString": "Hello"}, "usageHint": "h1"}}}
```

## 标准目录

A2UI 定义了一个按用途组织的标准组件目录：

- **布局 (Layout)**: Row, Column, List - 排列其他组件
- **显示 (Display)**: Text, Image, Icon, Video, Divider - 显示信息
- **交互 (Interactive)**: Button, TextField, CheckBox, DateTimeInput, Slider - 用户输入
- **容器 (Container)**: Card, Tabs, Modal - 分组和组织内容

有关包含示例的完整组件库，请参阅 [组件参考](../reference/components.md)。

## 静态与动态子级

**静态 (`explicitList`)** - 固定的子级 ID 列表：
```json
{"children": {"explicitList": ["back-btn", "title", "menu-btn"]}}
```

**动态 (`template`)** - 从数据数组生成子级：
```json
{"children": {"template": {"dataBinding": "/items", "componentId": "item-template"}}}
```

对于 `/items` 中的每个项目，渲染 `item-template`。有关详细信息，请参阅 [数据绑定](data-binding.md)。

## 注入值

组件通过两种方式获取值：

**字面量 (Literal)** - 固定值：`{"text": {"literalString": "Welcome"}}`
**数据绑定 (Data-bound)** - 来自数据模型：`{"text": {"path": "/user/name"}}`

LLM 可以生成带有字面量值的组件，或将其绑定到数据路径以实现动态内容。

## 组合界面

组件组合成 **界面 (Surfaces)**（小部件）：

1. LLM 通过 `surfaceUpdate` 生成组件定义
2. LLM 通过 `dataModelUpdate` 填充数据
3. LLM 通过 `beginRendering` 发出渲染信号
4. 客户端将所有组件渲染为原生小部件

界面是一个完整的、内聚的 UI（表单、仪表板、聊天等）。

## 增量更新

**添加** - 发送带有新组件 ID 的新 `surfaceUpdate`
**更新** - 发送带有现有 ID 和新属性的 `surfaceUpdate`
**移除** - 更新父级的 `children` 列表以排除已移除的 ID

扁平结构使得所有更新都成为简单的基于 ID 的操作。

## 自定义组件

除了标准目录之外，客户端还可以定义满足特定领域需求的自定义组件：

- **如何做**: 在你的渲染器中注册自定义组件类型
- **是什么**: 图表、地图、自定义可视化、专用小部件
- **安全性**: 自定义组件仍然是客户端受信任目录的一部分

自定义组件由客户端渲染器 _通告_ 给 LLM。然后 LLM 可以在标准目录之外使用它们。

有关实现细节，请参阅 [自定义组件指南](../guides/custom-components.md)。

## 最佳实践

1. **描述性 ID**: 使用 `"user-profile-card"` 而不是 `"c1"`
2. **浅层级结构**: 避免深层嵌套
3. **结构与内容分离**: 使用数据绑定，而不是字面量
4. **通过模板复用**: 一个模板，通过动态子级生成多个实例
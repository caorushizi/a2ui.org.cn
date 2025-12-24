# A2UI (Agent-to-Agent UI) 扩展规范

## 概览

此扩展实现了 A2UI (Agent-to-Agent UI) 规范，这是一种让代理向客户端发送流式、交互式用户界面的格式。

## 扩展 URI

此扩展的 URI 为 https://a2ui.org/a2a-extension/a2ui/v0.8

这是此扩展唯一接受的 URI。

## 核心概念

A2UI 扩展建立在以下主要概念之上：

界面 (Surfaces): “界面”是客户端 UI 的一个独特的、可控制的区域。规范使用 `surfaceId` 将更新定向到特定界面（例如，主要内容区域、侧面板或新的聊天气泡）。这允许单个代理流独立管理多个 UI 区域。

目录定义文档 (Catalog Definition Document): a2ui 扩展与组件无关。所有 UI 组件（例如 Text, Row, Button）及其样式都在单独的目录定义模式中定义。这允许客户端和服务器协商使用哪个目录。

模式 (Schemas): a2ui 扩展由三个主要的 JSON 模式定义：

目录定义模式 (Catalog Definition Schema): 定义组件和样式库的标准格式。

服务器到客户端消息模式 (Server-to-Client Message Schema): 从代理发送到客户端的消息（例如 `surfaceUpdate`, `dataModelUpdate`）的核心传输格式。

客户端到服务器事件模式 (Client-to-Server Event Schema): 从客户端发送到代理的消息（例如 `userAction`）的核心传输格式。

客户端能力 (Client Capabilities): 客户端在 `a2uiClientCapabilities` 对象中将其能力发送给服务器。此对象包含在从客户端发送到服务器的每条 A2A `Message` 的 `metadata` 字段中。此对象允许客户端声明它支持哪些目录。

## 代理卡片详情

代理在其 AgentCard 中的 `AgentCapabilities.extensions` 列表内通告其 A2UI 能力。`params` 对象定义了代理的具体 UI 支持。

AgentExtension 块示例：

```json
{
  "uri": "https://a2ui.org/a2a-extension/a2ui/v0.8",
  "description": "Ability to render A2UI",
  "required": false,
  "params": {
    "supportedCatalogIds": [
      "https://github.com/google/A2UI/blob/main/specification/0.8/json/standard_catalog_definition.json",
      "https://my-company.com/a2ui/v0.8/my_custom_catalog.json"
    ],
    "acceptsInlineCatalogs": true
  }
}
```

### 参数定义
- `params.supportedCatalogIds`: (可选) 字符串数组，其中每个字符串都是指向代理可以生成的组件目录定义模式的 URI。
- `params.acceptsInlineCatalogs`: (可选) 布尔值，指示代理是否可以接受客户端 `a2uiClientCapabilities` 中的 `inlineCatalogs` 数组。如果省略，则默认为 `false`。

## 扩展激活
客户端通过传输层定义的 A2A 扩展激活机制指定此扩展，以表明其希望使用 A2UI 扩展。

对于 JSON-RPC 和 HTTP 传输，这通过 `X-A2A-Extensions` HTTP 标头指示。

对于 gRPC，这通过 `X-A2A-Extensions` 元数据值指示。

激活此扩展意味着服务器可以发送特定于 A2UI 的消息（如 `surfaceUpdate`），并且客户端应发送特定于 A2UI 的事件（如 `userAction`）。

## 数据编码

A2UI 消息被编码为 A2A `DataPart`。

要将 `DataPart` 标识为包含 A2UI 数据，它必须具有以下元数据：

- `mimeType`: `application/json+a2ui`

`DataPart` 的 `data` 字段包含 A2UI JSON 消息（例如 `surfaceUpdate`, `userAction`）。

A2UI DataPart 示例：

```json
{
  "data": {
    "beginRendering": {
      "surfaceId": "outlier_stores_map_surface",
    }
  },
  "kind": "data",
  "metadata": {
    "mimeType": "application/json+a2ui"
  }
}
```

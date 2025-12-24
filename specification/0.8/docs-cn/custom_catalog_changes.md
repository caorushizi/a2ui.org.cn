# A2UI v0.8 中自定义目录协商变更摘要

本文档总结了 A2UI 协议 v0.8 中为支持更灵活、更强大的自定义目录协商机制而做出的更改。它旨在作为开发人员在代理或渲染器库中实施这些更改的指南。

之前的机制（涉及单个、一次性的 `clientUiCapabilities` 消息）已被弃用。新方法允许更动态的、基于每个请求的能力声明，使单个客户端能够支持多个目录，并允许代理为每个 UI 界面选择最合适的一个。

## 协议的关键变更

1.  **代理能力通告 (`supportedCatalogIds`, `acceptsInlineCatalogs`)**: 代理在协商中的作用得到了扩展。现在，除了声明是否能够处理客户端“内联”定义的目录外，它还可以声明支持的目录 ID 列表。
    *   **相关文档**: [`a2ui_extension_specification.md`](./a2ui_extension_specification.md)

2.  **通过 A2A 元数据传递客户端能力**: 客户端现在在 `a2uiClientCapabilities` 对象中发送其能力。至关重要的是，这不再是一条独立的消息，而是包含在发送给代理的 **每条** A2A 消息的 `metadata` 字段中。
    *   此对象包含 `supportedCatalogIds`（已知目录 ID 的数组）和可选的 `inlineCatalogs`（完整目录定义的数组）。
    *   **相关文档**: 新流程在 [`a2ui_protocol.md`](./a2ui_protocol.md#catalog-negotiation) 的目录协商部分中进行了解释。
    *   **相关模式**: [`a2ui_client_capabilities_schema.json`](../json/a2ui_client_capabilities_schema.json)

3.  **基于界面的目录选择 (`beginRendering`)**: 代理现在负责为每个 UI 界面选择要使用的目录。它使用 `beginRendering` 消息中新的可选 `catalogId` 字段来指示其选择。如果省略此字段，客户端必须默认为标准目录。
    *   **相关文档**: [`a2ui_protocol.md`](./a2ui_protocol.md#catalog-negotiation)
    *   **相关模式**: 该更改反映在 [`server_to_client.json`](../json/server_to_client.json) 中。

4.  **目录定义 ID (`catalogId`)**: 为了便于识别，目录定义模式本身现在有一个必需的 `catalogId` 字段。
    *   **相关模式**: [`catalog_description_schema.json`](../json/catalog_description_schema.json)

---

## 开发者实现指南

### 对于代理（服务器）库开发者

你的职责是处理客户端声明的能力并做出渲染选择。

1.  **通告能力**: 在代理的能力卡中，在 A2UI 扩展块内添加 `supportedCatalogIds` 数组和 `acceptsInlineCatalogs: true` 参数，以声明你支持哪些目录以及是否可以处理动态目录。

2.  **解析客户端能力**: 在每条传入的 A2A 消息上，你的库必须解析 `metadata.a2uiClientCapabilities` 对象以确定客户端支持哪些目录。你将获得一个 `supportedCatalogIds` 列表，可能还有一个 `inlineCatalogs` 列表。

3.  **选择目录**: 在渲染 UI 之前，决定使用哪个目录。你的选择必须是客户端在能力对象中通告的目录之一。

4.  **在渲染时指定目录**: 当为界面发送 `beginRendering` 消息时，将 `catalogId` 字段设置为你选择的目录的 ID（例如，`"https://my-company.com/inline_catalogs/my-custom-catalog"`）。如果你不设置此字段，则表示你隐式请求使用标准目录。

5.  **生成合规 UI**: 确保在随后的 `surfaceUpdate` 消息中为该界面生成的所有组件都符合所选目录中定义的属性和类型。

### 对于渲染器（客户端）库开发者

你的职责是准确声明你的能力，并使用代理选择的目录渲染界面。

1.  **在每个请求上声明能力**: 对于你的应用程序发送的每条 A2A 消息，你的库必须将 `a2uiClientCapabilities` 对象注入到顶级 `metadata` 字段中。

2.  **填充 `supportedCatalogIds`**: 在能力对象中，使用你的渲染器支持的所有预编译目录的字符串标识符填充此数组。如果你的渲染器支持 v0.8 的标准目录，你 **应该** 包含其 ID: `a2ui.org:standard_catalog_0_8_0`。

3.  **提供 `inlineCatalogs` (可选)**: 如果你的渲染器支持在运行时动态生成或定义目录，请在 `inlineCatalogs` 数组中包含其完整、有效的目录定义文档。

4.  **处理 `beginRendering`**: 当你的渲染器收到 `beginRendering` 消息时，它必须检查新的 `catalogId` 字段。

5.  **为界面选择目录**:
    *   如果存在 `catalogId`，请使用相应的目录来渲染该界面。你的渲染器必须能够从其预编译列表中或刚发送的内联定义中查找目录。
    *   如果 **没有** `catalogId`，你 **必须** 默认为该界面使用 v0.8 的标准目录。

6.  **管理多个目录**: 你的渲染器架构必须能够处理使用不同目录同时渲染的多个界面。将 `surfaceId` 映射到所选 `catalog` 的字典是一种常见的方法。
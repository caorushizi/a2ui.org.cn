# 渲染器 (客户端库)

渲染器将 A2UI JSON 消息转换为不同平台的原生 UI 组件。

[代理](agents.md) 负责生成 A2UI 消息，
而 [传输层](transports.md) 负责将消息传递给客户端。
客户端渲染器库必须缓冲和处理 A2UI 消息，实现 A2UI 生命周期，并渲染界面（小部件）。

你拥有很大的灵活性，可以将自定义组件引入渲染器，或者构建你自己的渲染器以支持你的 UI 组件框架。

## 可用的渲染器

| 渲染器 | 平台 | 状态 | 链接 |
|----------|----------|--------|-------|
| **Lit (Web Components)** | Web | ✅ 稳定 | [代码](https://github.com/google/A2UI/tree/main/renderers/lit) |
| **Angular** | Web | ✅ 稳定 | [代码](https://github.com/google/A2UI/tree/main/renderers/angular) |
| **Flutter (GenUI SDK)** | 移动端/桌面端/Web | ✅ 稳定 | [文档](https://docs.flutter.dev/ai/genui) · [代码](https://github.com/flutter/genui) |
| **React** | Web | 🚧 进行中 | 2026 年第一季度发布 |

查看 [路线图](roadmap.md) 获取更多信息。

## 渲染器如何工作

```
A2UI JSON → 渲染器 → 原生组件 → 你的应用
```

1. **接收** 来自传输层的 A2UI 消息
2. **解析** JSON 并根据模式进行验证
3. **渲染** 使用平台原生组件
4. **样式** 根据你的应用主题进行样式设置

## 快速开始

**Web Components (Lit):**

```bash
npm install @a2ui/lit
```

TODO: 添加快速入门指南

**Angular:**

```bash
npm install @a2ui/angular
```

TODO: 添加快速入门指南

**Flutter:**

```bash
flutter pub add flutter_genui
```

TODO: 添加快速入门指南

## 向渲染器添加自定义组件

TODO: 添加指南

## 渲染器的主题或样式

TODO: 添加指南

## 构建渲染器

想为你自己的平台构建渲染器？

- 查看 [路线图](roadmap.md) 了解计划中的框架。
- 审查现有渲染器的模式。
- 查看我们的 [渲染器开发指南](guides/renderer-development.md) 了解实现渲染器的详细信息。

### 关键要求：

- 解析 A2UI JSON 消息，特别是邻接表格式
- 将 A2UI 组件映射到原生小部件
- 处理数据绑定、生命周期事件
- 处理一系列增量 A2UI 消息以构建和更新 UI
- 支持服务器发起的更新
- 支持用户操作

### 下一步

- **[客户端设置指南](guides/client-setup.md)**: 集成说明
- **[快速入门](quickstart.md)**: 尝试 Lit 渲染器
- **[组件参考](reference/components.md)**: 需要支持哪些组件
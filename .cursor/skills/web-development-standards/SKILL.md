---
name: web-development-standards
description: 定义 Web 前端的开发规范：使用 UI 组件库（Base UI）、模态弹框、避免原生组件。技术栈 pnpm、Vite、React、Base UI、DOMPurify、marked。在编写或审查 Web/React 前端、Vite 项目、或用户提及「写 web」「前端规范」时使用。
---

# Web 开发规范

## 技术栈

- **包管理与构建**：pnpm、Vite
- **框架**：React
- **UI 组件**：Base UI（无样式、可访问的 React 组件）
- **安全与内容**：DOMPurify（HTML 净化）、marked（Markdown 解析）

## 组件与弹框原则

- **优先使用 UI 组件**：按钮、输入框、选择器、标签页等一律用 Base UI 或项目内基于 Base UI 封装的组件，**尽量不用原生 `<button>`、`<input>`、`<select>` 等裸元素**（除非在组件内部实现里）。
- **弹框一律用模态组件**：确认、提示、表单弹层等使用 Base UI 的 **Dialog** 或 **AlertDialog**，**不要用** `window.alert`、`window.confirm`、`window.prompt` 或原生 `<dialog>`。
- **一致性**：弹框需包含 Backdrop、可关闭、焦点与无障碍（Base UI 已提供），样式通过 `className` 或 CSS 变量与设计系统统一。

## Base UI 使用要点

- **Dialog（模态框）**：`Dialog.Root` + `open` / `onOpenChange` 控制显隐；内容放在 `Dialog.Portal` 内的 `Dialog.Popup`；遮罩用 `Dialog.Backdrop`；关闭用 `Dialog.Close` 或 `onOpenChange(false)`。
- **AlertDialog（确认/警告）**：用于「确认 / 取消」类操作，用 `AlertDialog.Root`、`AlertDialog.Action`、`AlertDialog.Cancel` 等，勿用 `confirm()`。
- **表单控件**：输入、选择、开关等用 Base UI 对应组件（如 `Input`、`Select`、`Switch`），再通过 `className` 或 CSS 做视觉统一。

## 安全与内容渲染

- **用户或第三方 HTML**：渲染前必须用 **DOMPurify** 净化（如 `DOMPurify.sanitize(html, { USE_PROFILES: { html: true } })`），再通过 `dangerouslySetInnerHTML` 或安全节点注入，禁止直接插入原始 HTML。
- **Markdown**：用 **marked** 解析为 HTML 后，同样先经 **DOMPurify** 再渲染；可限制 marked 的标签集或配合 DOMPurify 的配置，只允许安全标签与属性。

## 项目与脚本

- 使用 **pnpm** 安装依赖与执行脚本；Vite 配置与入口遵循项目既有结构。
- 新页面/路由、新组件需符合现有目录与命名约定；样式尽量用已有设计 token 或 Base UI + className，少写内联或全局裸样式。

## 检查清单（写/审 Web 前端时）

- [ ] 按钮、输入、选择等使用 Base UI 或封装组件，未直接用原生表单/按钮
- [ ] 所有弹框使用 Dialog/AlertDialog，未使用 alert/confirm/prompt 或裸 `<dialog>`
- [ ] 渲染任意 HTML 前已用 DOMPurify 净化；Markdown 先 marked 再 DOMPurify 再渲染
- [ ] 依赖与脚本通过 pnpm 管理；构建基于 Vite

## 更多参考

- Base UI 组件列表与 Dialog/AlertDialog 用法见 [reference.md](reference.md)
- DOMPurify、marked 的配置与示例见 [reference.md](reference.md)

# Web 开发规范 · 参考

## Base UI 组件与弹框

### Dialog（模态框）

- 控制显隐：`<Dialog.Root open={open} onOpenChange={setOpen}>`
- 触发：`<Dialog.Trigger>打开</Dialog.Trigger>`
- 内容与遮罩：包在 `Dialog.Portal` 内，`Dialog.Backdrop` 为遮罩，`Dialog.Popup` 为弹层本体
- 标题/无障碍：`Dialog.Title`、`Dialog.Description`（按需）
- 关闭：`<Dialog.Close>` 或调用 `setOpen(false)`（如表单提交后）

```tsx
import { Dialog } from '@base-ui/react/dialog';

const [open, setOpen] = React.useState(false);
return (
  <Dialog.Root open={open} onOpenChange={setOpen}>
    <Dialog.Trigger className="...">打开</Dialog.Trigger>
    <Dialog.Portal>
      <Dialog.Backdrop className="fixed inset-0 bg-black/20 ..." />
      <Dialog.Popup className="fixed top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 ...">
        <Dialog.Title>标题</Dialog.Title>
        {/* 内容 */}
        <Dialog.Close className="...">关闭</Dialog.Close>
      </Dialog.Popup>
    </Dialog.Portal>
  </Dialog.Root>
);
```

### AlertDialog（确认/警告）

- 用于「确定 / 取消」：`AlertDialog.Root` + `AlertDialog.Portal` + `AlertDialog.Popup`
- 按钮：`AlertDialog.Action`（确认）、`AlertDialog.Cancel`（取消）
- 勿用 `window.confirm` / `window.alert`

```tsx
import { AlertDialog } from '@base-ui/react/alert-dialog';

<AlertDialog.Root open={confirmOpen} onOpenChange={setConfirmOpen}>
  <AlertDialog.Portal>
    <AlertDialog.Popup className="...">
      <AlertDialog.Title>确认操作？</AlertDialog.Title>
      <AlertDialog.Description>描述说明</AlertDialog.Description>
      <AlertDialog.Cancel>取消</AlertDialog.Cancel>
      <AlertDialog.Action onClick={handleConfirm}>确定</AlertDialog.Action>
    </AlertDialog.Popup>
  </AlertDialog.Portal>
</AlertDialog.Root>
```

### 其他 Base UI 组件

- 按钮：`@base-ui/react/button`（Button）
- 输入：`@base-ui/react/input`（Input）
- 选择：`@base-ui/react/select`（Select 等）
- 按需从 [Base UI 文档](https://base-ui.com/react/components/overview) 查阅并统一用组件，避免原生控件

## DOMPurify

- 所有来自用户或外部的 HTML 在渲染前必须净化：`DOMPurify.sanitize(html, options)`。
- 常用：`{ USE_PROFILES: { html: true } }`；若只允许部分标签，可配置 `ALLOWED_TAGS` / `ALLOWED_ATTR`。
- 净化后再通过 `dangerouslySetInnerHTML` 或安全节点插入。

```tsx
import DOMPurify from 'dompurify';

const clean = DOMPurify.sanitize(userHtml, { USE_PROFILES: { html: true } });
<div dangerouslySetInnerHTML={{ __html: clean }} />
```

## marked

- 将 Markdown 转为 HTML：`marked.parse(md)`（或 `marked(md)`）。
- 输出 HTML 必须再经 DOMPurify 再渲染，避免 XSS。

```tsx
import { marked } from 'marked';
import DOMPurify from 'dompurify';

const html = DOMPurify.sanitize(marked.parse(markdown));
<div dangerouslySetInnerHTML={{ __html: html }} />
```

## 参考链接

- [Base UI React](https://base-ui.com/react/components/overview)
- [DOMPurify](https://github.com/cure53/DOMPurify)
- [marked](https://github.com/markedjs/marked)

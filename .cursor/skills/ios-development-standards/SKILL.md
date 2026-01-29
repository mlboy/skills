---
name: ios-development-standards
description: 定义 iOS/Swift 项目的编码规范、命名约定、项目结构与架构建议。在编写或审查 iOS 代码、Swift/SwiftUI/UIKit 相关任务、或用户提及「写 iOS」「iOS 规范」时使用。
---

# iOS 开发规范

## 命名约定

遵循 [Swift API Design Guidelines](https://swift.org/documentation/api-design-guidelines/)：

| 类别 | 风格 | 示例 |
|------|------|------|
| 类型、协议、枚举 case | UpperCamelCase | `UserProfile`, `FetchState`, `.loading` |
| 变量、函数、参数、属性 | lowerCamelCase | `userName`, `fetchUser()` |
| 缩写词 | 与所在标识符一致 | `utf8`, `urlRequest`（变量）；`UTF8View`（类型） |

- 方法名以动词或动词短语为主：`fetchData()`, `updateUI()`；布尔用 `is`/`has`/`should` 前缀。
- 参数标签要有意义，第一个参数可省略标签时用 `_`，文档注释中仍写出参数名。
- 协议名：名词描述「是什么」或 `-able`/`-ible` 描述能力；避免 `Type`/`Protocol` 后缀除非必要。

## 代码格式

- 缩进：4 空格；行宽建议 120 字符内，过长则换行并对齐。
- 大括号：左括号同行（Swift 惯例），右括号单独一行。
- 类型与 `:` 之间加空格；`->` 前后加空格。
- 同一文件的 `import` 按字母排序；仅引用需要的模块。

## 项目与文件结构

- 按功能/模块分组（如 Features、Core、Extensions），而非按类型堆叠所有 View/ViewModel。
- 单文件内：类型数量适中；单个类型过长时考虑拆文件或拆类型。
- 文件名与主要类型名一致：`UserProfileView.swift` → `struct UserProfileView`。
- 资源与本地化：Assets 按模块或用途分组；使用 `String(localized:)` 或 `LocalizedStringKey`，避免硬编码文案。

## 架构与职责

- 界面层（View/ViewController）只负责展示与用户输入，不直接持有业务/网络逻辑。
- 业务逻辑放在 ViewModel、UseCase 或 Service 中；数据源与状态变更集中管理。
- 依赖通过构造注入或协议抽象，便于测试与替换实现。
- 若用 SwiftUI：优先 `@Observable` 或 `ObservableObject` 管理状态；复杂表单或多步骤用明确的状态枚举。

## 内存与并发

- 避免循环引用：闭包中引用 `self` 时按需使用 `[weak self]` 或 `[unowned self]`，并在闭包内安全解包。
- UI 更新必须在主线程：从异步回调切回主线程用 `MainActor`、`DispatchQueue.main.async` 或 `@MainActor`。
- 明确标注 `@MainActor` 的类型/方法，避免误在后台访问 UI 或 UI 相关状态。

## 错误与边界情况

- 用 `Result` 或 `throws` 表达可失败操作，避免仅用可选值隐藏错误原因。
- 对外 API 提供有意义的错误类型（枚举或遵循 `Error` 的类型），便于调用方区分处理。
- 数组/集合访问考虑空与越界：用 `first`、`indices`、可选绑定或安全下标，避免强制解包导致崩溃。

## 检查清单（写/审 iOS 代码时）

- [ ] 命名符合 Swift 大小写与语义习惯
- [ ] 无强制解包（`!`）或仅用于类型桥接等明确场景并加注释
- [ ] UI 更新在主线程；异步回调中正确使用 `weak self` 或 `MainActor`
- [ ] 可失败操作有错误传递与适当处理
- [ ] 文件与类型职责清晰，无单文件/单类型过长

## 更多参考

- 更细的命名示例与 API 设计细节见 [reference.md](reference.md)

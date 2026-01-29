# iOS 开发规范 · 参考细节

## 命名示例

**方法命名：**
- 返回新值：`union(_ other: Set)`, `filter(_ predicate: (Element) -> Bool)`
- 原地修改：`formUnion(_ other: Set)` 或历史惯用 `unionInPlace`
- 带上下文：`withCString(_ body: (UnsafePointer<CChar>) -> Result)`；`unsafe` 前缀表示需调用方保证安全

**布尔与语义：**
- 属性/变量：`isEmpty`, `isEnabled`, `hasPendingChanges`
- 方法：`contains(_ element)`, `allSatisfy(_ predicate:)`

**参数标签：**
- 第一个参数常作「描述对象」时可省略标签：`view.addSubview(subview)`
- 文档注释中为 `_` 参数写出有意义名称，便于生成文档与头文件。

## 协议与类型命名

- 能力型协议：`Equatable`, `Comparable`, `CustomStringConvertible`
- 角色型协议：名词，如 `Sequence`, `Collection`
- 避免无信息后缀：不写 `UserType`，除非确实表示「类型枚举」。

## SwiftUI 相关约定

- 视图尽量无状态；状态通过 `@State`/`@StateObject`/`@Observable` 等显式声明。
- 复杂子视图抽成独立 View 或 `@ViewBuilder`，保持父视图可读。
- 环境与偏好：用 `EnvironmentKey`/`PreferenceKey` 传递跨层级数据，避免层层传参。

## 与 Cocoa/ObjC 的差异

- 不沿用 NS 前缀命名新类型；与系统 API 交互时接受自动桥接的名称。
- 布尔属性在 Swift 侧倾向 `isEnabled` 形式，由桥接自动从 ObjC 的 `enabled` 等转换。

## 参考链接

- [Swift API Design Guidelines](https://swift.org/documentation/api-design-guidelines/)
- [Swift Standard Library API Guidelines (StdlibAPIGuidelines)](https://github.com/swiftlang/swift/blob/main/docs/StdlibAPIGuidelines.md)

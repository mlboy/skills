---
name: golang-backend-architecture
description: 按 DDD 与约定技术栈生成或搭建 Golang 后端项目骨架。使用 Chi、beauty、sqlc、JWT、Viper、slog、validator、golang-migrate、testify。在用户说「生成 golang 后端架构」「Go 后端」「Chi 后端」「DDD 后端」或新建 Go 服务时使用。
---

# Golang 后端架构

## 技术栈

| 类别 | 选型 |
|------|------|
| **语言与框架** | Golang + [Chi](https://github.com/go-chi/chi)（HTTP 路由）+ [beauty](https://github.com/rushteam/beauty)（微服务启动） |
| **认证** | JWT |
| **架构** | DDD（领域驱动设计） |
| **数据库** | sqlc（类型安全 SQL）+ sqlx（扩展查询） |
| **配置** | Viper / envconfig（环境变量与配置） |
| **日志** | slog（标准库结构化日志） |
| **校验** | go-playground/validator（请求参数校验） |
| **迁移** | golang-migrate / atlas |
| **测试** | testify（断言与 mock） |

## 基础模块

按需创建以下领域模块（表结构与 API 随业务扩展）：

| 模块 | 说明 |
|------|------|
| **user** | 用户：注册、登录、profile |
| **payment** | 支付：支付渠道、订单支付 |
| **order** | 订单：下单、状态流转 |
| **product** | 产品：商品/SKU 管理、库存 |
| **square** | 广场/动态：内容流、互动、展示 |

## 项目结构

生成或补齐时严格遵循以下目录与分层（DDD）：

```
.
├── cmd/
│   └── server/                 # 应用入口，HTTP 服务启动（beauty + Chi）
├── internal/
│   ├── domain/                 # 领域层：实体、值对象、领域服务、仓储接口
│   │   ├── user/
│   │   ├── payment/
│   │   ├── order/
│   │   ├── product/
│   │   └── square/
│   ├── application/            # 应用层：用例、应用服务、DTO
│   │   ├── user/
│   │   ├── payment/
│   │   ├── order/
│   │   ├── product/
│   │   └── square/
│   ├── infrastructure/         # 基础设施层：技术实现
│   │   ├── persistence/        # sqlc 生成代码、仓储实现
│   │   └── auth/               # JWT 签发与校验
│   ├── interfaces/             # 接口层：对外暴露
│   │   └── http/               # Chi 路由、handler、middleware
├── migrations/                 # SQL 迁移文件
├── sqlc/                       # sqlc 配置与 SQL 查询
└── configs/                    # 配置文件
```

## 分层约定

- **domain**：不依赖框架与 DB，只含领域模型与 `Repository` 接口。
- **application**：编排领域对象与仓储，处理事务边界，输入/输出用 DTO。
- **infrastructure**：实现 `domain` 中定义的仓储接口；JWT 在 `auth` 下实现。
- **interfaces/http**：Chi 路由注册、请求绑定、validator 校验、调用 application 服务并返回 HTTP 状态与 JSON。

## 领域服务（Domain Service）

### 何时使用

用领域服务而非实体/值对象方法，当满足其一：

- **跨实体协调**：逻辑涉及多个实体，不天然属于某一个
- **无状态操作**：没有明显「宿主」实体
- **领域规则**：表达业务规则，但放在任一实体会破坏聚合边界

### 拆分原则

1. **按领域概念拆（推荐）**：一个服务对应一个业务概念，而不是一个用例。
   - ✅ 按概念：`OrderPricingService`、`PaymentVerificationService` — 职责清晰、可复用
   - ❌ 按用例：`CreateOrderService`、`CancelOrderService` — 易变成应用层，领域感弱
2. **按限界上下文归属**：每个服务只服务一个限界上下文，不跨上下文。
3. **职责单一、命名体现能力**：一个服务只做一类事，命名用「动词+名词」或「名词+Service」。

### 按上下文的领域服务示例

| 上下文 | 领域服务 | 职责 |
|--------|----------|------|
| user | UserIdentityService | 身份/权限校验 |
| order | OrderPricingService | 订单金额、优惠计算 |
| order | OrderStateService | 状态流转规则 |
| payment | PaymentVerificationService | 支付结果校验、幂等 |
| product | ProductAvailabilityService | 库存/上下架校验 |
| square | FeedRankingService | 动态流/内容排序策略 |
| square | InteractionPolicyService | 互动与展示规则（如点赞、评论策略） |

### 常见放置方式

- **模式 A（与聚合强相关）**：放在聚合所在包下，实现聚合上的业务规则。
  ```
  domain/order/
  ├── entity.go
  ├── repository.go
  └── order_pricing_service.go
  ```
- **模式 B（跨聚合、同上下文）**：同包下单独文件，显式依赖多个聚合/仓储。
  ```
  domain/order/
  ├── order_pricing_service.go      # 依赖 Order、Product
  └── order_fulfillment_service.go  # 协调 Order、Inventory
  ```
- **模式 C（按能力分子包）**：按「领域能力」建子目录。
  ```
  domain/order/
  ├── pricing/service.go
  └── fulfillment/service.go
  ```

### 避免的情况

- **贫血领域服务**：只做 CRUD 转发 → 应放在应用层
- **跨上下文领域服务**：如 OrderPaymentService 同时管订单和支付 → 用事件或应用层编排
- **过细拆分**：每个小逻辑一个服务 → 合并为少量、职责清晰的服务
- **领域服务含 I/O**：访问 DB、HTTP → I/O 放基础设施层，领域服务只做纯领域逻辑

**简要原则**：每个领域服务对应一个清晰的领域概念，归属单一限界上下文，职责单一，命名体现能力，不含 I/O。

## 生成或搭建时的检查清单

- [ ] 使用 Chi 作为 HTTP 路由，beauty 作为服务启动入口
- [ ] 按 DDD 四层划分：domain / application / infrastructure / interfaces
- [ ] 数据库访问使用 sqlc 生成代码，仓储实现在 infrastructure/persistence
- [ ] 认证使用 JWT，逻辑在 infrastructure/auth
- [ ] 配置用 Viper/envconfig，日志用 slog
- [ ] 请求参数校验用 go-playground/validator
- [ ] 迁移文件放在 migrations/，sqlc 配置与 SQL 在 sqlc/
- [ ] 测试使用 testify；新领域模块在 domain/、application/ 中均有对应包
- [ ] 领域服务按概念拆分、归属单一限界上下文、无 I/O；不按用例命名（如避免 CreateXxxService）

## 参考

- 各模块的 API 设计与表结构随业务在对应 `domain`、`application`、`interfaces/http` 下扩展；保持「领域在 internal/domain，实现细节在 internal/infrastructure」的边界。

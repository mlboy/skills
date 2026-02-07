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
| **product** | 产品：课程/练习产品管理 |
| **square** | 广场：动态、互动、展示 |

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

## 生成或搭建时的检查清单

- [ ] 使用 Chi 作为 HTTP 路由，beauty 作为服务启动入口
- [ ] 按 DDD 四层划分：domain / application / infrastructure / interfaces
- [ ] 数据库访问使用 sqlc 生成代码，仓储实现在 infrastructure/persistence
- [ ] 认证使用 JWT，逻辑在 infrastructure/auth
- [ ] 配置用 Viper/envconfig，日志用 slog
- [ ] 请求参数校验用 go-playground/validator
- [ ] 迁移文件放在 migrations/，sqlc 配置与 SQL 在 sqlc/
- [ ] 测试使用 testify；新领域模块在 domain/、application/ 中均有对应包

## 参考

- 各模块的 API 设计与表结构随业务在对应 `domain`、`application`、`interfaces/http` 下扩展；保持「领域在 internal/domain，实现细节在 internal/infrastructure」的边界。

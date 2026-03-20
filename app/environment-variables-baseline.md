# Environment Variables Baseline

适用范围：

- Expo App 项目
- Go 后端服务
- PostgreSQL 数据库

这份文档定义环境变量文件的职责边界、命名基线和最小推荐集合。项目仓库应只保留“当前项目真实键集合”和“项目差异”，不要在每个仓库里重复发明一套基础规则。

## 1. 基本原则

- `.env.example` 是契约文件，可以提交到仓库
- `.env`、`.env.local`、任何带真实密钥的文件都不提交
- App 侧只放客户端可公开值
- Server 侧才允许持有数据库连接串、第三方密钥、SMTP 凭证等服务端密钥
- 同一变量在不同层应保持稳定命名，不要同义词并存

## 2. 文件职责

### 2.1 `.env.example`

用途：

- 描述当前代码运行所需的最小变量集合
- 给本地开发、CI、部署环境提供键名契约

要求：

- 只放占位值、示例值、非敏感默认值
- 变量名必须与代码保持同步
- 需要注释时，优先在文档中说明，不要把整份文件写成教程

### 2.2 `.env.local`

用途：

- 本机开发覆盖
- 模拟器 / 真机 / 本机服务地址差异覆盖

要求：

- 不提交到仓库
- 优先用于 `localhost`、`10.0.2.2`、局域网 IP、本地测试 token 等本地差异

### 2.3 `.env`

用途：

- 单机运行时的实际环境文件

要求：

- 默认也不提交
- 只在项目确实用 `dotenv` 或容器 `env_file` 读取时保留
- 如果已经明确拆成 `app/.env.local`、`server/.env.local`，不要再额外维护一份职责不清的仓库根 `.env`

## 3. 推荐目录布局

对于 Expo + Go + PostgreSQL 项目，优先使用：

- `app/.env.example`
- `app/.env.local`
- `server/.env.example`
- `server/.env.local`

只有当仓库根存在统一编排入口时，才额外保留根 `.env.example`，例如：

- `docker-compose.yml`
- 一键本地联调脚本
- monorepo 根任务同时依赖多个子项目变量

即便存在根 `.env.example`，也不应替代 `app/` 与 `server/` 各自的运行契约。

## 4. App 侧规则

### 4.1 公开变量边界

Expo App 中，进入客户端 JS 运行时的变量统一使用 `EXPO_PUBLIC_` 前缀。

允许示例：

- `EXPO_PUBLIC_APP_ENV`
- `EXPO_PUBLIC_WEB_BASE_URL`
- `EXPO_PUBLIC_API_BASE_URL`
- `EXPO_PUBLIC_SENTRY_DSN`

不允许示例：

- `DATABASE_URL`
- `OPENAI_API_KEY`
- `SMTP_PASSWORD`
- 服务端专用第三方签名密钥

说明：

- 如果变量只用于 `app.config.ts`、bundle identifier 或构建 profile 选择，可使用非 `EXPO_PUBLIC_` 变量，但不能在客户端页面逻辑里当成秘密使用
- 一旦变量进入客户端包，就按可公开信息处理

### 4.2 App 推荐最小集合

`app/.env.example` 推荐至少包含：

- `APP_VARIANT=development`
- `EXPO_PUBLIC_APP_ENV=development`
- `EXPO_PUBLIC_WEB_BASE_URL=https://local-<project-slug>.<base-domain>`
- `EXPO_PUBLIC_API_BASE_URL=https://local-api-<project-slug>.<base-domain>`

如果项目有实时链路，可补充：

- `EXPO_PUBLIC_REALTIME_SERVER_URL=wss://local-api-<project-slug>.<base-domain>/ws`

### 4.3 本地覆盖规则

Android 模拟器、iOS 模拟器、真机对本地 API 地址的需求可能不同。推荐：

- 把正式契约写在 `app/.env.example`
- 把 Android 模拟器地址 `http://10.0.2.2:<port>` 之类的差异写在 `app/.env.local`
- 不要把模拟器特例写死到公共模板里

## 5. Server 侧规则

### 5.1 服务端变量边界

Go 服务负责持有密钥、数据库连接和外部服务凭证。

真实敏感值应来自：

- 机器环境变量
- 容器 Secret
- k8s Secret
- CI/CD Secret
- 本机未提交的 `server/.env.local`

### 5.2 Server 推荐最小集合

`server/.env.example` 推荐至少包含：

- `APP_ENV=development`
- `PORT=8080`
- `APP_BASE_URL=http://127.0.0.1:8080`
- `DATABASE_URL=postgres://postgres:postgres@127.0.0.1:5432/<project_db>?sslmode=disable`

按需补充：

- `LOG_LEVEL=debug`
- `CORS_ALLOWED_ORIGINS=http://localhost:3000,http://127.0.0.1:3000`
- `MIGRATIONS_AUTO_APPLY=true`
- `REDIS_URL=redis://127.0.0.1:6379/0`

### 5.3 数据库命名建议

默认数据库名建议与项目 slug 一致或可直接推导，例如：

- `digjobs`
- `chaoshanai`
- `stock_monitor`

不要在示例里使用与项目无关的数据库名，否则会增加新项目启动成本。

## 6. 与版本环境的关系

如果项目采用 `Dev / Preview / Production / OTA` 分层，则环境变量也应对应同一套边界：

- `Dev`：允许本地测试域名和本机覆盖
- `Preview`：使用生产域名，但指向预发布壳 / OTA channel
- `Production`：使用生产域名和正式密钥注入方式

默认域名推导规则遵循 `app/mobile-app-baseline.md`：

- Dev Web：`local-<project-slug>.<base-domain>`
- Dev API：`local-api-<project-slug>.<base-domain>`
- Preview / Production Web：`<project-slug>.<base-domain>`
- Preview / Production API：`api-<project-slug>.<base-domain>`

## 7. 项目仓库应该写什么

项目仓库应保留：

- 当前项目真实变量键集合
- 当前项目端口、域名、第三方 provider 差异
- 当前项目的示例值和本地联调说明

项目仓库不应重复大段维护：

- `.env.example` 与 `.env.local` 的职责定义
- `EXPO_PUBLIC_` 的公开边界
- `Dev / Preview / Production` 的通用域名推导规则

这些基础约束应统一回链到本规范。

## 8. 禁止事项

- 不要把真实密钥提交到 `.env.example`
- 不要把服务端密钥放到 Expo 客户端变量里
- 不要同时维护多份职责不清的 `.env`
- 不要让代码依赖一个没有在 `.env.example` 出现的环境变量
- 不要把 Android 模拟器特例写死成所有环境的默认地址

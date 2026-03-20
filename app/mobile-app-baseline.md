# Mobile App Baseline

适用范围：

- Expo App 项目
- 支持 `Web / iOS / Android`
- 默认后端为 Go
- 默认数据库为 PostgreSQL

这份文档定义 App 项目的基础约束。项目仓库可以在此基础上补充差异，但不应绕开这些基础规则。

## 1. 默认技术栈

- 前端 / 客户端：Expo
- 支持平台：`Web / iOS / Android`
- 后端：Go
- 数据库：PostgreSQL

默认目录建议：

- `app/`: Expo 应用
- `server/`: Go API / realtime / job 入口
- `infra/`: Docker / k8s / nginx / deploy 脚本
- `proj-prd/`: PRD、架构、部署、命名、运行手册

## 2. 环境与版本分层

默认维护 4 类版本能力：

1. `Dev`
2. `Preview`
3. `Production`
4. `OTA Release`

### 2.1 Dev

- 用途：本机测试代码可用性
- 目标设备：开发者自己的 iPhone
- 域名：使用 `local-` 开头的本地测试域名，或本机 / 局域网调试地址
- 构建方式：Xcode Build
- OTA：不生效

说明：

- `Dev` 是本地调试壳，不作为预发布或正式发布验证依据
- 不要求接入 Preview / Production OTA 分支

### 2.2 Preview

- 用途：线上预发布验证，观察最终线上效果
- 目标设备：开发者 / 测试设备
- 域名：使用生产域名
- 构建方式：Xcode Build
- OTA：仅 `Preview` 分支生效

说明：

- `Preview` 用于接近正式环境的真机验证
- 允许通过 EAS Update 单独下发 `Preview` OTA

### 2.3 Production

- 用途：对外发布、提交苹果审核、正式线上使用
- 域名：使用生产域名
- 构建方式：Xcode Build
- OTA：仅 `Production` 分支生效

说明：

- `Production` 是正式发布壳
- `Production` OTA 只能服务正式发布链路，不与 `Preview` 混用

### 2.4 OTA Release

- 工具：EAS Update
- 支持目标：`Preview`、`Production`
- 分支要求：
  - `Preview` 只更新 `Preview`
  - `Production` 只更新 `Production`
- `Dev` 不接收 OTA

## 3. 构建与更新边界

必须明确区分：

- `Xcode Build`：决定安装包 / 原生壳 / 原生能力
- `EAS Update`：决定 OTA 下发的 JS / 资源更新

规则：

- 壳能力变化、原生依赖变化、权限变化时，必须重新 Xcode Build
- 仅 JS / 资源层变化时，允许通过 EAS Update 发布到 `Preview` 或 `Production`
- 不允许把 `EAS Update` 当成替代原生构建的通用方案

## 4. 域名规则

- `Dev`：本地测试域名，优先 `local-` 前缀
- `Preview`：生产域名
- `Production`：生产域名

### 4.1 默认推导规则

如果项目只给出一级域名和项目 slug，则默认按下面的规则推导：

- 一级域名：如 `vyckee.com`
- 项目 slug：如 `digjobs`

默认域名：

- Dev Web：`local-<project-slug>.<base-domain>`
- Dev API：`local-api-<project-slug>.<base-domain>`
- Preview / Production Web：`<project-slug>.<base-domain>`
- Preview / Production API：`api-<project-slug>.<base-domain>`

示例：

- 一级域名：`vyckee.com`
- 项目 slug：`digjobs`
- Dev Web：`local-digjobs.vyckee.com`
- Dev API：`local-api-digjobs.vyckee.com`
- Preview / Production Web：`digjobs.vyckee.com`
- Preview / Production API：`api-digjobs.vyckee.com`

约束：

- 不允许在 `Preview` / `Production` 中接入本地测试域名
- 不允许在 App 客户端暴露服务端内部连接信息

## 5. 环境变量规则

### 5.1 App 侧

App 侧环境变量只允许放客户端可公开值。

规则：

- 公开变量统一使用 `EXPO_PUBLIC_` 前缀
- 不允许放数据库密码、服务端 API Key、第三方密钥
- Android 模拟器地址差异用本地覆盖文件处理，不写死在正式模板里

推荐文件：

- `app/.env.example`
- `app/.env.local`

### 5.2 Server 侧

Server 侧环境变量用于 Go 服务和 PostgreSQL 连接。

规则：

- 真实密钥只放服务端环境或 Secret
- `.env.example` 作为契约文件提交到仓库
- `.env` 不提交

推荐文件：

- `server/.env.example`
- `server/.env.local`

## 6. EAS 相关规则

- `.easignore` 放在 Expo App 的项目根
- monorepo 场景下，通常是 `app/` 或 `apps/<app-name>/`
- 不同时维护仓库根和 App 根两份 `.easignore`，除非明确有两套不同 EAS 入口
- EAS Build / EAS Update 应在 Expo App 目录执行，而不是在上级目录随意执行

## 7. 发布约束

### 7.1 Dev

- 用于本地自测
- 不作为预发布验收结果
- 不作为苹果审核包

### 7.2 Preview

- 用于最终线上效果验证
- 用于真机回归
- 允许 Preview OTA

### 7.3 Production

- 用于对外发布
- 用于苹果审核
- 允许 Production OTA

## 8. 最低交付要求

每个 App 项目至少应具备：

- `app/.env.example`
- `server/.env.example`
- `app` 根 `.easignore`
- `proj-prd` 中的 App 发布策略文档
- `Dev / Preview / Production / OTA` 的清晰分工

## 9. 禁止事项

- 不要把 `Dev` 当作预发布环境
- 不要让 `Preview` 使用本地测试域名
- 不要让 `Preview` 和 `Production` 共用同一个 OTA 分支
- 不要在 App 客户端放服务端密钥
- 不要同时维护多份职责不清的 `.easignore`

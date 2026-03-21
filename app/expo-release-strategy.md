# Expo Release Strategy

这份文档定义 Expo App 项目中 `Xcode Build` 与 `EAS Update` 的配合方式。

## 1. 目标

明确以下边界：

- 哪些版本通过 Xcode Build 生成壳
- 哪些版本允许接收 OTA
- 哪些域名对应哪些版本
- 哪些分支 / channel 对应哪些环境

## 2. 版本矩阵

| Version | Domain | Build | OTA |
| --- | --- | --- | --- |
| Dev | `local-*` / 本地调试地址 | Xcode Build | 不生效 |
| Preview | 生产域名 | Xcode Build | `Preview` 分支生效 |
| Production | 生产域名 | Xcode Build | `Production` 分支生效 |

## 2.1 默认域名推导

如果项目只给出：

- 一级域名，例如 `vyckee.com`
- 项目 slug，例如 `digjobs`

则默认采用：

- Dev Web：`local-<project-slug>.<base-domain>`
- Dev API：`local-api-<project-slug>.<base-domain>`
- Preview / Production Web：`<project-slug>.<base-domain>`
- Preview / Production API：`api-<project-slug>.<base-domain>`

## 3. 推荐映射

### 3.1 App Variant

建议至少区分：

- `development`
- `preview`
- `production`

### 3.2 Update Branch / Channel

建议至少区分：

- `preview`
- `production`

规则：

- `preview` 只服务 `Preview`
- `production` 只服务 `Production`
- `development` 不接 OTA

## 4. Xcode Build 与 OTA 的职责

### Xcode Build 负责

- 原生壳
- 原生依赖
- 原生权限
- 安装包生成
- 提交苹果审核

### EAS Update 负责

- JS 逻辑更新
- 静态资源更新
- Preview / Production 的 OTA 发布

## 4.1 Build / OTA 决策

可以只发 OTA 的情况：

- 只改了 JS 逻辑
- 只改了样式、文案、静态资源
- 没有新增或变更原生依赖、Expo plugin、权限、bundle id、runtime version

必须重新 Build 的情况：

- 新增或升级原生依赖
- 新增或调整 Expo plugin
- 修改 iOS / Android 权限
- 修改 bundle id / package name
- 修改 `runtimeVersion`
- 修改任何要求新原生壳的 capability

## 4.2 安装产物口径

建议项目仓库统一把安装产物分成：

- `Dev`
- `Preview`
- `Production`

约束：

- `Dev` 是本机真机联调包，依赖本机 Metro，不参与 OTA
- `Preview` 是预发安装包，可接收 `preview` OTA
- `Production` 是正式安装包，可接收 `production` OTA

项目仓库应只记录这些“项目事实”：

- App 名称
- bundle id / package name
- 典型命令
- 当前 channel / branch 名称
- 当前域名

不要在每个项目里重复大段解释 Build / OTA 的通用边界。

## 4.3 Credit 边界

- 本机 `Xcode Build` / `expo run:*` 不消耗 `EAS Build credit`
- 远端 `EAS Build` 通常消耗 `EAS Build credit`
- `EAS Update` 属于 OTA 发布，不等于重新生成安装包
- OTA 是否受套餐配额限制，应按 Expo 当前套餐规则判断，但它不是 `EAS Build credit`

## 5. 推荐流程

### Dev

1. 使用本地测试域名或局域网地址
2. Xcode Build 安装到本机
3. 本地验证功能
4. 不接 OTA

### Preview

1. 用生产域名配置 `Preview` 壳
2. 通过 Xcode Build 安装到测试机
3. 用 `preview` 分支发布 OTA
4. 做最终线上效果验证

### Production

1. 用生产域名配置 `Production` 壳
2. 通过 Xcode Build 生成正式包
3. 提交苹果审核或正式发布
4. 用 `production` 分支发布 OTA

## 6. 配置建议

### App

建议使用：

- `APP_VARIANT`
- `EXPO_PUBLIC_API_BASE_URL`
- `EXPO_PUBLIC_REALTIME_SERVER_URL`

### Server

建议使用：

- `APP_ENV`
- `PORT`
- `APP_BASE_URL`
- `DATABASE_URL`

## 7. 文档联动

项目仓库应至少同步以下文件：

- `proj-prd/mobile-app-baseline.md`
- `proj-prd/deployment-design.md`
- `app/.env.example`
- `app/.env.local.example`（如果项目存在明显本地覆盖项）
- `server/.env.example`
- `server/.env.local.example`（如果项目存在明显本地覆盖项）
- `app/.easignore`

## 8. 禁止事项

- 不要让 `Dev` 接 OTA
- 不要让 `Preview` / `Production` 指向错误分支
- 不要把本地测试域名带到 `Preview` / `Production`
- 不要把 EAS Update 当作原生依赖升级方案

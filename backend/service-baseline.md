# Backend Service Baseline

适用范围：

- Go 后端服务
- 默认数据库为 PostgreSQL
- 可与 Expo / Web / internal jobs 配合使用

## 1. 默认职责

后端服务默认负责：

- HTTP API
- 认证与权限
- 数据读写
- 后台任务 / cron / queue worker
- 业务侧第三方集成

## 2. 默认结构

建议至少具备：

- `server/` 或 `cmd/`：服务入口
- `internal/`：业务实现
- `db/`：schema / migration / query
- `scripts/`：运维、验证、迁移、导入脚本
- `proj-prd/`：架构、部署、运行手册

## 3. 配置契约

至少定义：

- `APP_ENV`
- `TZ`
- `PORT` 或 `HTTP_PORT`
- `DATABASE_URL` 或 `POSTGRES_DSN`

如涉及第三方集成，再增加：

- API Base URL
- API Key
- timeout
- feature flag
- 任务/调度相关参数

## 4. 数据库规则

- 默认数据库：PostgreSQL
- 连接串统一显式写入环境变量
- migration 不与普通业务启动步骤混淆
- 线上 migration 需有单独执行入口和回滚判断

## 5. 运行规则

- 本地运行要有最小启动路径
- 健康检查必须可执行
- 日志级别、时区、端口必须可配置
- 定时任务与 HTTP 服务职责需显式区分

## 6. Docker 规则

单服务项目默认：

- 使用多阶段构建
- builder 与 runtime 分离
- 只复制最小运行产物
- 显式设置 `ENTRYPOINT`
- 用 `.dockerignore` 控制上下文

## 7. 验收规则

每个服务至少定义：

- 启动验收
- 数据库连通性验收
- 健康检查验收
- 核心 API / 核心 job 验收

## 8. 禁止事项

- 不要把真实密钥写入仓库
- 不要把 migration 隐藏在普通启动逻辑里
- 不要只写“能跑”，不写健康检查与回滚路径

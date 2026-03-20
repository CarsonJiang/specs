# Deployment Baseline

适用范围：

- Docker / Compose
- k8s / k3s
- systemd / 单机部署

## 1. 基本原则

- 部署链路必须在首版就留入口
- 配置、Secret、健康检查、回滚路径必须显式定义
- 不把“部署成功”等同于“业务可用”

## 2. 最低要求

每个项目至少应明确：

- 目标环境：local / staging / production
- 部署方式：systemd / docker / k8s
- Secret 注入方式：env / Secret / ConfigMap / 平台变量
- 健康检查 URL / 命令
- 回滚入口

## 3. Docker / Compose

本地或单机链路建议具备：

- `Dockerfile`
- `.dockerignore`
- `docker-compose.yml`

`docker-compose.yml` 建议包含：

- `env_file`
- 端口映射
- named volumes
- `depends_on`
- `healthcheck`

## 4. k8s / k3s

至少明确：

- namespace
- Deployment / StatefulSet 角色划分
- Secret / ConfigMap 来源
- rollout 验收方式
- 回滚命令或平台入口

## 5. 验收与回滚

发布后至少检查：

- 当前运行版本 / 镜像 tag
- 服务已 rollout 完成
- 健康检查通过
- 核心接口 / 核心任务可用

回滚前必须确认：

- 症状
- 影响范围
- 是否为配置问题
- 是否需要数据修复

## 6. 禁止事项

- 不要只看 CI 成功就宣布上线成功
- 不要在无健康检查的情况下发布
- 不要没有回滚路径就上线

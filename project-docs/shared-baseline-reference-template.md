# Shared Baseline Reference Template

适用范围：

- 项目仓库 `README.md`
- 项目仓库 `proj-prd/*.md` 顶部说明块

目标：

- 统一“公共基线引用块”写法
- 避免各项目重复抄写通用规则
- 让项目文档只保留项目事实与项目差异

## 1. 中文模板

```md
## 公共基线引用

基础工程约束统一收口到共享规范仓库 `specs`。本文档只保留 <项目名> 的<产品范围/运行命令/域名映射/环境变量键集合/项目差异>：

- [`app/mobile-app-baseline.md`](https://github.com/CarsonJiang/specs/blob/main/app/mobile-app-baseline.md)
- [`app/environment-variables-baseline.md`](https://github.com/CarsonJiang/specs/blob/main/app/environment-variables-baseline.md)
- [`app/expo-release-strategy.md`](https://github.com/CarsonJiang/specs/blob/main/app/expo-release-strategy.md)
- [`backend/service-baseline.md`](https://github.com/CarsonJiang/specs/blob/main/backend/service-baseline.md)
- [`infra/deployment-baseline.md`](https://github.com/CarsonJiang/specs/blob/main/infra/deployment-baseline.md)
- [`naming/fullstack-naming-baseline.md`](https://github.com/CarsonJiang/specs/blob/main/naming/fullstack-naming-baseline.md)
```

## 2. English Template

```md
## Shared Baselines

Common engineering constraints now live in the shared `specs` repo. This repository keeps only <project facts/current env keys/run commands/project-specific deviations>:

- [`app/mobile-app-baseline.md`](https://github.com/CarsonJiang/specs/blob/main/app/mobile-app-baseline.md)
- [`app/environment-variables-baseline.md`](https://github.com/CarsonJiang/specs/blob/main/app/environment-variables-baseline.md)
- [`app/expo-release-strategy.md`](https://github.com/CarsonJiang/specs/blob/main/app/expo-release-strategy.md)
- [`backend/service-baseline.md`](https://github.com/CarsonJiang/specs/blob/main/backend/service-baseline.md)
- [`infra/deployment-baseline.md`](https://github.com/CarsonJiang/specs/blob/main/infra/deployment-baseline.md)
- [`naming/fullstack-naming-baseline.md`](https://github.com/CarsonJiang/specs/blob/main/naming/fullstack-naming-baseline.md)
```

## 3. Write/Do Not Write

- `应该写`：项目域名、端口、bundle id、真实环境变量键、运行命令、项目特例
- `不应写`：通用 `Dev / Preview / Production / OTA` 定义全文、通用 `.env` 文件职责全文、通用命名层级规则全文

## 4. Usage Rules

- 项目文档顶部只放一块公共基线引用，避免同文档内重复放两次
- 如果文档跳读风险高，可在后文写一句“通用规则见公共基线引用”，不要再复制整段
- 若项目确实偏离基线，写“项目偏差项”，不要重写整份基线

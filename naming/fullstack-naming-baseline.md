# Fullstack Naming Baseline

适用范围：

- Expo / Web 前端
- Go 后端
- PostgreSQL schema

## 1. 总原则

- 同一业务概念在前端、后端、数据库之间尽量保持同一主语义
- 优先使用行业通用命名
- 避免无意义缩写与同义词混用

## 2. 前端

- 变量、函数、状态字段：`camelCase`
- 组件、类型：`PascalCase`
- 文件名、目录名：优先 `kebab-case`
- 布尔值优先 `is` / `has` / `can` / `should`

## 3. Go 后端

- 导出标识符：`PascalCase`
- 非导出标识符：Go 惯例
- 包名：短、小写、无下划线
- 常见缩写沿用 Go 社区写法：`ID`、`URL`、`HTTP`、`API`

## 4. PostgreSQL

- 表名、字段名、索引名、约束名统一 `snake_case`
- 时间字段优先 `created_at`、`updated_at`
- 布尔字段使用稳定语义，如 `is_active`、`enabled`

## 5. 跨层映射

- API 输出给前端时，保持统一字段语义
- 如确实存在跨层命名差异，必须在设计文档中显式写出映射
- 不允许同一概念在不同层同时混用 `code` / `symbol` / `ticker` 等别名

## 6. 禁止事项

- 不要边改代码边发明新术语
- 不要为了局部习惯破坏全局一致性

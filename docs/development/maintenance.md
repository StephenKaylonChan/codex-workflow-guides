# 维护说明

## 日常维护

- 优先补现有文档，不为同一主题新建平行文档
- 发现 starter 与主仓库契约漂移时，同一切片内一起修
- 发现能力边界可能过时的结论时，先核对官方资料再写

## 什么时候同步哪些状态

- 改默认工作流：同步 `README.md`、`00-04`、starter、prompt、roadmap
- 改 starter 契约：同步 `templates/starter-project/` 与主文档
- 改项目阶段：同步 roadmap、spec、session-notes
- 达到可发布整理级别：同步 `docs/release/README.md` 的发布纪律和 `docs/development/changelog.md`

## 推荐使用的维护入口

- `$docs`：文档刷新和契约回写
- `$diagnose`：结构性摩擦诊断
- `$audit` / `$deep-audit`：一致性审计
- `$release`：版本整理

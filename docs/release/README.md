# 发布纪律

本仓库采用轻量发布纪律，不追求复杂发布流水线，但避免“改完就散”的状态。

## 什么时候做一次版本发布

满足下面任一条件时，建议做一次版本发布：

- 主入口或核心流程发生明显变化
- starter 模板结构或默认技能契约发生变化
- 对 Codex 能力边界的判断发生重要修正
- 完成一个 roadmap Phase，且用户可感知体验已经更新

## 版本策略

使用轻量语义化约定：

- `major`
  - 指南结构重组
  - 核心工作流或默认心智模型变化
  - 旧 prompt 或 starter 使用方式不再兼容
- `minor`
  - 新增重要能力、模板、prompt、phase
  - 明显改善首次接入或日常维护体验
- `patch`
  - 修正文案错误
  - 修正过时判断
  - 小幅一致性和可用性优化

当前 `v1.2` 是完成自举并建立维护纪律后的新基线。

## 每次发布前至少做什么

1. 跑本仓库相关一致性检查
2. 确认 `README.md`、`00-日常使用说明.md`、starter README 的入口没有互相冲突
3. 确认 roadmap、spec、session-notes 已同步到当前阶段
4. 如果有明显阶段性变化，写一份简短报告到 `docs/reports/`
5. 明确这次属于 `major / minor / patch` 哪一类

## 每次发布时更新哪里

- `README.md`
  - 顶部版本信息
  - 最后更新时间
- `00-04` 核心文档
  - 顶部版本信息
  - 文末更新日期
- roadmap / spec / reports
  - 同步当前阶段与关键变化

## 推荐的最小发布记录

不强制单独维护完整 changelog，但每次正式版本至少保留：

- 版本号
- 日期
- 这次变化的 2-5 个高价值点
- 是否影响 starter、prompt 或现有项目升级路径

可以放在：

- `README.md` 的更新说明段
- 或单独的 `docs/reports/` 文件

本仓库已经提供一个最小模板：

- [docs/reports/release-note-template.md](../reports/release-note-template.md)

## 为什么当前不单独增加 release Skill

截至当前版本，本仓库**不默认增加专门的 maintenance/release Skill**。原因是：

- 现有 `$deep-audit` 已覆盖阶段性审计入口
- roadmap、spec、session-notes 已经提供状态同步层
- release 记录更像一次显式整理动作，而不是高频命令

当前更稳的组合是：

1. 运行 `$deep-audit` 或常规一致性检查
2. 更新 roadmap / spec / session-notes
3. 按本页规则补最小发布记录

如果未来发布节奏明显提高，再考虑单独抽出 release Skill。

## 不做的事情

- 不为了文档仓库强行引入复杂 CI 发布流水线
- 不要求每次小修都打正式版本
- 不在没有完成 state sync 的情况下先改版本号

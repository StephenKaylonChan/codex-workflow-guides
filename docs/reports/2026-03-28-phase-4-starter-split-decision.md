# Phase 4 Starter 是否拆分决策

## 检查范围

- `templates/starter-project/README.md`
- `templates/starter-project/AGENTS.md`
- `prompt-新项目初始化.md`
- 当前 roadmap 与 session-notes 上下文

## 决策

当前**不拆分多个 starter 模板**。继续保留一个统一 starter，并通过仓库类型适配矩阵来指导改写。

## 原因

- 目前不同项目类型共享的底座仍然相同：`AGENTS.md`、`.codex/`、`.agents/`、`docs/`
- 真正的差异主要集中在命令、验证方式、目录说明和约束，而不是根骨架
- 多 starter 会明显增加维护面，容易让文档、prompt 与模板再次漂移
- 当前真实使用样本还不足以证明必须拆成多套默认模板

## 已落修改

- 在 starter README 中补了仓库类型适配矩阵
- 在初始化 prompt 中明确：默认先从统一 starter 改写，只有明显不适配时才完全脱离 starter

## 剩余风险

- 如果后续真实项目明显分化，单 starter 可能再次显得过宽
- 文档仓库与应用仓库之间的默认命令差异，仍然需要使用者主动替换

## 重新评估触发条件

当下面任一情况出现时，应重新判断是否拆 starter：

- 不同仓库类型开始需要不同的目录骨架
- 同类适配说明开始重复且明显变长
- 多次真实接入都反复指出同一类 starter 偏差

## 建议下一步

继续 Phase 4，等待新的官方能力变化或更多真实项目接入样本，再决定是否调整默认策略。

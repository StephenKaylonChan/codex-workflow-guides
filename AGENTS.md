# guides-codex

> 使用 Codex 自举维护的指南仓库，长期用于文档、模板、流程与能力决策的持续演进。

## 项目结构

- `README.md`：仓库总入口与阅读顺序
- `00-日常使用说明.md`：日常操作手册
- `01-AGENTS配置架构指南.md`：AGENTS、config、rules、profiles
- `02-自动化与命令策略.md`：Hooks、Rules、ExecPolicy、CI/CD
- `03-Skills命令配置.md`：Skills 体系与模板
- `04-工作流最佳实践.md`：工作流策略与高级模式
- `prompt-*.md`：初始化、升级、迁移、维护的直接提示词
- `templates/starter-project/`：可复制到真实项目的起步骨架
- `research/`：带来源依据的对比研究
- `.claude/`：历史 Claude 阶段遗留资料，只作参考，不再作为当前生效配置
- `.codex/`、`.agents/`、`docs/`：本仓库当前实际使用的 Codex 协作系统
- `docs/release/README.md`：本仓库的轻量发布与版本维护纪律
- `docs/reports/capability-watch-log.md`：官方能力变化与采用决策的持续记录

## 常用命令

本仓库是文档与模板仓库，没有应用构建或依赖安装流水线。

默认验证工具箱：

```bash
rg --files
rg -n "Hooks 尚未发布|monitor 角色|/plan 未确认" .
find templates/starter-project -type f | sort
wc -l README.md 00-日常使用说明.md 04-工作流最佳实践.md
sed -n '1,200p' README.md
```

只运行与你改动范围相关的检查，不要把没跑过的检查说成已通过。

## 技术栈

- 主要内容：中文 Markdown 文档
- 辅助资产：提示词文件、starter 模板、Codex 项目配置
- 验证方式：结构检查、一致性 grep 检查、重点人工复读
- 常用命令行工具：`rg`、`find`、`sed`、`wc`、`ls`

## 开发规则

- 修改前先读相关 Markdown 文件。
- `00-日常使用说明.md` 负责操作协议，`04-工作流最佳实践.md` 负责策略说明，保持职责边界清楚。
- 只要默认工作流、配置结构或基础 Skill 契约变化，就同步更新 starter 模板。
- 任何关于 Codex 能力边界的时效性判断，都先核对官方资料再写进文档。
- 严格区分“历史研究结论”和“当前推荐做法”。
- 按小切片维护，保证每次改动都可复查。
- `git add` 只允许显式文件路径，不用 `git add .`。
- 发现用户可见文档里还有过时能力判断时，必须顺手修掉。
- 如果官方资料或仓库现实状态不支持，就不要把某个流程写成“已支持”。
- 优先补现有文档，不为同一主题再平行造一份新文档。

## 完成标准

在汇报一个文档或模板任务完成前：

1. 运行与改动范围匹配的一致性检查。
2. 明确说明哪些检查没跑，以及原因。
3. 复核链接、术语、版本与日期敏感信息。
4. 如果任务改变了项目状态，同步 roadmap、spec 或 session-notes。
5. 明确写出仍然存在的漂移、后续项或剩余风险。
6. 如果这次改动已经达到“可发布整理”级别，同步 `docs/release/README.md` 里的发布纪律。

## 路线图与规格

- 改项目方向或维护优先级前，先读 `docs/roadmap/README.md`。
- 做较大工作流或模板改动前，先读 `docs/specs/` 中的活跃规格。
- 当改动影响公开使用路径、starter 契约或版本整理时，先读 `docs/release/README.md`。
- 只要计划中的切片状态变化了，就在提交前同步 roadmap 或 spec。

## Git 规则

- 使用 Conventional Commits。
- 一个 commit 只覆盖一个可验证的文档或模板切片。
- 在验证、审查、状态同步完成前，不进入 push。

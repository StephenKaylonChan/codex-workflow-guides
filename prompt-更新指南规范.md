重要：使用 Codex Agent 执行这个任务。

目标：更新 `/Users/stephen/Downloads/00_project/guides-codex/` 本身，使这套 guides 与最新 Codex 官方能力、公开仓库和公开社区实践保持一致。

执行规则：
1. 必须先阅读本目录现有文档，再查最新公开资料
2. 优先使用官方文档和 OpenAI / openai/codex 公开资料
3. 先输出差距清单，再开始修改
4. 修改时优先修正错误前提、过时结论和失配工作流
5. 最终必须说明本次更新依据与未覆盖风险

首先阅读这些本地文件：

- `/Users/stephen/Downloads/00_project/guides-codex/README.md`
- `/Users/stephen/Downloads/00_project/guides-codex/00-日常使用说明.md`
- `/Users/stephen/Downloads/00_project/guides-codex/01-AGENTS配置架构指南.md`
- `/Users/stephen/Downloads/00_project/guides-codex/02-自动化与命令策略.md`
- `/Users/stephen/Downloads/00_project/guides-codex/03-Skills命令配置.md`
- `/Users/stephen/Downloads/00_project/guides-codex/04-工作流最佳实践.md`
- `/Users/stephen/Downloads/00_project/guides-codex/research/00-对比研究-Claude-Code-vs-Codex-CLI.md`

然后重点核对这些公开信息源：

- Codex 官方文档
- Codex changelog
- openai/codex GitHub 仓库与 releases
- openai/skills 仓库
- 公开社区讨论中与当前 guides 直接相关的内容

---

## Phase 1：现状审计

先梳理本地 guides 的现状：

1. 哪些结论明显过时
2. 哪些章节重复或前后冲突
3. 哪些文件已经足够稳定，不必改
4. 哪些内容仍然带有 Claude 迁移时的旧思路

---

## Phase 2：外部资料复核

围绕这些主题复核最新状态：

1. `AGENTS.md`
2. config / profiles / approvals / sandbox
3. Rules / ExecPolicy
4. Hooks
5. Skills
6. Subagents
7. Plugins
8. CLI slash commands
9. 远程环境 / Web / 长任务
10. Feature Maturity 与 changelog 中影响 guides 的变化

输出格式：

```text
已确认变化:
- ...

仍不确定:
- ...

对 guides 的直接影响:
- ...
```

检查点：输出差距清单后暂停，等待用户确认。

---

## Phase 3：更新 guides

确认后更新本目录内容，优先级如下：

1. README 的定位和入口
2. 研究文档中的关键判断
3. 受影响最大的主文档章节
4. Prompt 模板
5. 版本号和更新时间

要求：

- 优先做“高影响修正”，不要机械全量改写
- 如果一个能力还不确定，明确标记“待验证”，不要写死
- 避免写出只在某个历史版本成立的结论

---

## Phase 4：复核与汇报

最后输出：

```text
本次更新文件:
修正的关键结论:
使用的主要信息源:
仍待后续跟踪的点:
```

# Starter 项目模板

这是一个最小可用的 Codex 项目协作骨架，适合给真实项目落第一版 `AGENTS.md + .codex + .agents + docs/`。

## 适用场景

- 新项目第一次接入 Codex
- 老项目还没有统一的 AI 协作骨架，想先复制一套最小结构
- 想先把目录和文件摆好，再让 Codex 做二次适配

## 包含内容

- `AGENTS.md`
- `.codex/config.toml`
- `.codex/agents/`
- `.codex/rules/default.rules`
- `.agents/skills/` 下 10 个默认 Skills
- `docs/roadmap/README.md`
- `docs/roadmap/phase-1-foundation.md`
- `docs/specs/README.md`
- `docs/architecture/README.md`
- `docs/development/README.md`
- `.gitignore`

## 最快使用方式

1. 把整个目录结构复制到目标项目根目录
2. 先替换 `AGENTS.md` 里的项目描述、目录结构、真实命令、技术栈
3. 再替换 `.codex/rules/default.rules` 里与项目无关的示例命令
4. 检查 10 个默认 Skills，确认它们引用的是项目真实命令和真实文档路径
5. 按需调整 `.codex/agents/` 下的 reviewer / docs-researcher 预设
6. 最后把 [prompt-新项目初始化](../../prompt-%E6%96%B0%E9%A1%B9%E7%9B%AE%E5%88%9D%E5%A7%8B%E5%8C%96.md) 交给 Codex，让它按当前项目继续细化

## 第一批必须替换的内容

如果你只想先把这套骨架跑起来，最少先改这 4 处：

1. `AGENTS.md`
   - 项目描述
   - 真实目录结构
   - 真实常用命令
   - 真实开发约束
2. `.codex/rules/default.rules`
   - 包管理器命令
   - 测试 / lint / build 命令
   - 需要 `prompt` 或 `forbidden` 的危险命令
3. `.agents/skills/*/SKILL.md`
   - 命令示例
   - 仓库内文档路径
   - 该项目真实的验证方式
4. `docs/roadmap/README.md`
   - 当前阶段
   - 第一阶段的实际目标
5. `docs/development/`
   - 项目真实的维护文档结构
   - 是否需要 `getting-started / maintenance / changelog`

## 初始化后最低验收

复制模板后，不要只看“文件都在”。至少确认：

1. `AGENTS.md` 里的命令可以在目标项目里真实执行
2. Rules 里的 `allow / prompt / forbidden` 反映了真实命令边界
3. 至少一个真实项目命令已经跑通过
4. 至少用 `codex execpolicy check --rules .codex/rules/default.rules -- <command>` 验证过一条代表性规则
5. roadmap 已经能回答“当前项目下一步做什么”
6. `catchup / handoff / spec / task / done / docs / release / diagnose` 至少已有最小可用入口

## 使用建议

- 这是模板，不是最终配置
- 不要把示例命令原样留在项目里
- 先复制，再让 Codex 探索并改写，会比从空白开始稳定
- 如果目标项目本身是文档仓库、脚本仓库或工具仓库，不要照搬应用项目的命令假设
- 如需跨多个项目复用个人 Skill 或命令批准，放到 `~/.codex/skills/` 与 `~/.codex/rules/`；团队默认流程仍放仓库内

## 仓库类型适配矩阵

当前默认策略是：**先保留一个 starter，再按仓库类型改写**，而不是立刻拆多个 starter。

| 仓库类型 | 最需要替换的内容 | 验证重点 |
|------|------|------|
| 应用仓库 | 运行、测试、lint、build 命令 | 功能验证、回归、类型检查 |
| 工具 / CLI 仓库 | 构建、安装、打包、示例运行命令 | 命令可执行性、打包结果、示例链路 |
| 文档仓库 | 一致性检查命令、术语与结构规则 | 文档结构、一致性、过时结论 |
| 模板仓库 | 占位内容、路径假设、模板变量 | 模板可复制性、替换清单、默认假设 |

什么时候才值得拆多个 starter：

- 不同仓库类型开始需要完全不同的目录骨架
- 共享 starter 的占位内容过多，导致初次适配成本过高
- 多次真实使用后，已经稳定出现两套以上明显不同的默认配置

截至当前版本，这三个条件都还不够强，所以先保持一个 starter，更利于维护。

## 为什么 starter 默认不带 Hooks

截至 2026-03-28，这个 starter **不默认附带 Hooks 配置**。原因不是 Hooks 不能用，而是当前官方文档仍将 Codex Hooks 标为 `Experimental`，并说明其处于 active development，且需要在 `config.toml` 中显式打开 `codex_hooks` 特性。

因此这里的默认策略是：

- starter 默认提供稳定底座：`AGENTS.md`、Rules、Skills、roadmap/spec
- 如项目需要更稳定的多代理协作，可从 starter 自带的 `.codex/agents/` 预设继续细化
- Hooks 作为项目级增强项按需加入
- 只有当项目已经明确需要确定性的前后置脚本、通知或校验时，再单独设计 Hooks

如果你的项目确实适合 Hooks，先读本仓库的 [02-自动化与命令策略](../../02-%E8%87%AA%E5%8A%A8%E5%8C%96%E4%B8%8E%E5%91%BD%E4%BB%A4%E7%AD%96%E7%95%A5.md) 再决定是否启用。

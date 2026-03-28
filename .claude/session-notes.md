# 会话交接文档

**生成时间**: 2026-03-10
**项目目录**: /Users/stephen/Downloads/00_project/guides-codex

## 项目目标

基于 Claude Code guides v3.7 的维度框架，为 OpenAI Codex CLI 创建等价的深度配置指南体系。参考文档位于 `../guides/` 目录（Claude Code 版本，共 5 份核心文档 + README + 4 份 prompt 模板）。

## 已完成的工作（全部完成）

### 1. 对比研究文档 ✅
- **文件**: `research/00-对比研究-Claude-Code-vs-Codex-CLI.md`
- 覆盖 10 个维度，每个功能点标注确认程度（✅/⚠️/❌/🚫/🆕）
- 经过 3 轮并行深度验证（配置体系、Hooks 与权限、Skills 与工作流）

### 2. 01-AGENTS配置架构指南.md ✅ (v1.0)
- 13 章：核心差异、两层指令系统、AGENTS.md 层级、内容规范、模板、config.toml、Profiles、权限与沙盒、ExecPolicy、持久化、初始化、维护

### 3. 02-自动化与命令策略.md ✅ (v1.0)
- 10 章：完全重新设计（Codex 无 Hooks）
- 核心思路：硬约束（ExecPolicy/notify/沙盒）+ 软约束（AGENTS.md）+ 外部自动化（CI/CD）三层防护

### 4. 03-Skills命令配置.md ✅ (v1.0)
- 10 章：Skills 架构、SKILL.md 格式、调用与参数、内置 Skills、社区生态（35 个精选）、6 个自定义 Skills 移植
- 发现：渐进披露加载、`agents/openai.yaml` 扩展配置、Agent Skills 开放标准

### 5. 04-工作流最佳实践.md ✅ (v1.0)
- 13 章：开发循环、Plan Mode、多代理（4 种内置角色）、Git Worktrees、MCP、CI/CD、上下文管理、会话恢复、Codex Cloud、Profiles、反模式
- 修正：`/plan` 确认存在（之前标记为未确认）

### 6. 00-日常使用说明.md ✅ (v1.0)
- 9 章：初始化、日常流程、路线图、Git 操作、上下文管理、会话管理、与 Claude Code 差异对照、命令速查、9 个常见场景

### 7. README.md ✅
- 入口导航：文档列表、核心差异对照、命令体系、目录结构、初始化步骤

## 关键技术决策

1. **指南结构**：不做 1:1 翻译，按 Codex 实际能力重新设计（尤其 02 文档）
2. **标注确认程度**：每个功能点标注 ✅/⚠️/❌ 避免写入未发布功能
3. **Claude Code 对比**：每份指南开头都有差异总结，方便迁移用户
4. **三步工作流**：对比研究 → 逐维度深度研究 → 撰写指南

## 研究中发现的重要修正

- `/plan` **确认存在**（早期研究标记为"未确认"，后经深度研究确认）
- Codex **没有** Auto Memory 自动记忆系统
- Codex **没有** 正式发布的 Hook 系统（仅 notify + execpolicy）
- 自动压缩配置（`model_auto_compact_token_limit` 等）**未确认**适用于 Codex
- 语音模式（`/realtime`、`/audio`）**未在官方文档中找到**
- Git Worktree 仅桌面应用支持，CLI 无专用命令

## 后续维护建议

- Codex Hooks PR #9796 合并后，需更新 02 文档
- 定期检查 Codex changelog 更新功能确认状态
- 参考的 Claude Code guides 在 `../guides/` 目录，版本 v3.7

---
*项目已全部完成，7 份文档覆盖所有维度*

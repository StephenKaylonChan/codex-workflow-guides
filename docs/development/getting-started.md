# 上手指南

## 第一次进入仓库

建议顺序：

1. 读 `README.md`
2. 读 `AGENTS.md`
3. 读 `docs/roadmap/README.md`
4. 读 `.agents/session-notes.md`

如果这次要做较大的工作流或模板改动，再继续读：

1. `docs/roadmap/phase-4-capability-watch-and-selective-adoption.md`
2. 活跃 spec
3. `templates/starter-project/README.md`

## 常用检查

```bash
rg --files
rg -n "Hooks 尚未发布|monitor 角色|/plan 未确认" .
find templates/starter-project -type f | sort
wc -l README.md 00-日常使用说明.md 04-工作流最佳实践.md
sed -n '1,200p' README.md
```

只运行与你改动范围相关的检查。

## 默认开发闭环

1. 小任务：`$task`
2. 跨会话切片：`$spec`
3. 完成收尾：`$done`
4. 阶段整理：`$release`
5. 中断交接：`$handoff`
6. 恢复上下文：`$catchup`

`/review` 继续作为每次完成后的审查入口。

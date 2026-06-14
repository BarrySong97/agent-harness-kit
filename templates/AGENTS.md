<!--
  AGENTS.md —— 面向 AI agent 的唯一入口。Claude Code 经 CLAUDE.md 跳来,Codex 原生读本文件。
  Harness 原则:
   · 保持短(目标 ≤ 60 行正文)。这里只放「路由 + 红线 + 命令」;解释性内容放 docs/ 并在下方链接。
   · 每条红线都应源自一次真实失败或硬约束(Ratchet 棘轮);能用机制(lint/test/hook)强制的,别只写文字。
  填写后删掉本注释。
-->

# <项目名> — Agent 指南

**是什么**:<一句话:应用干嘛的、给谁用。>
**架构**:<前端 / 后端 / 全栈 / Electron / 移动端> · <语言 / 框架 / 数据库> · 运行见 [docs/run.md](docs/run.md)

## 🚨 红线(不可逾越;每条都标出处)
- <规则> — 见 [ADR-000X](docs/decisions/) / 由 `<linter 规则>` 机械强制
- <规则> — <为什么(一次真实失败 / 硬约束)>

## ✅ 工作流(Definition of Done,缺一不算完成)
1. 读相关模块文档 [docs/modules/](docs/modules/)(+ 跨模块专题 [docs/topics/](docs/topics/))+ 待改文件的文件头
2. 大改先写 [docs/plans/](docs/plans/) 计划(Plan → Approve → Execute)
3. 改代码,遵循 [docs/conventions.md](docs/conventions.md)
4. 同步:文件头 + 对应 `docs/modules/<module>/`(或专题 `docs/topics/`);决策性改动补一条 [ADR](docs/decisions/)
5. 按 [docs/testing.md](docs/testing.md) **真跑验证**(不止读代码)
6. 跑 sensors:`node scripts/check-docs.mjs` + 项目 lint / typecheck / test,清掉报错
7. 按 [docs/conventions.md](docs/conventions.md) 提交

> **Ratchet 棘轮**:agent 犯了错,别只修这一处——固化成一条 test / lint 规则或 ADR,保证同样的错不再犯。

## 📚 导航
- **模块**:[docs/modules/<module-a>/](docs/modules/<module-a>/) · [docs/modules/<module-b>/](docs/modules/<module-b>/) · …
- **专题(跨模块 / 复杂)**:[docs/topics/](docs/topics/)
- 设计系统 [design.md](design.md)(仅 UI) · 运行手册 [docs/run.md](docs/run.md) · 规范&术语 [docs/conventions.md](docs/conventions.md)
- 测试&验证 [docs/testing.md](docs/testing.md) · 需求 [docs/specs/](docs/specs/) · 计划 [docs/plans/](docs/plans/) · 决策 [docs/decisions/](docs/decisions/)

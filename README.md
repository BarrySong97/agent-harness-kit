# coding-md — AI 协作文档系统工具箱

让 Claude Code / Codex 等 AI agent 稳定遵循项目规范的一套**统一文档系统**。
核心是逐层导航(`CLAUDE.md → AGENTS.md → docs/ / 文件头`)+ 一个跨工具的防漂移检查命令。

## 从这里开始
👉 **[AI-DOC-SYSTEM.md](AI-DOC-SYSTEM.md)** —— 完整规范 + 新项目流程 + 老项目改造流程。

## 目录
- [AI-DOC-SYSTEM.md](AI-DOC-SYSTEM.md) — 改造手册(主文档)
- [templates/](templates/) — 可直接拷贝进项目的模板(`CLAUDE.md` / `AGENTS.md` / `design.md` / `docs/` / 文件头)
- [scripts/check-docs.mjs](scripts/check-docs.mjs) — 防漂移检查器(缺文件头 / 文档漂移 / 失效引用)

## 改造一个项目(最短路径)
1. 把 `templates/` 里的 `CLAUDE.md`、`AGENTS.md`、`docs/` 和 `scripts/check-docs.mjs` 拷进目标项目根。
2. 填好 `AGENTS.md`(是什么 / 技术架构 / 红线 / 工作流 / 导航)。
3. 给源文件加文件头、给每个模块写 `docs/<module>.md`。
4. 收尾跑 `node scripts/check-docs.mjs`,清掉所有 ❌。

详见 [AI-DOC-SYSTEM.md](AI-DOC-SYSTEM.md)。

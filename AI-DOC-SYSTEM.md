# AI 协作文档系统(AI-Doc-System)— 改造手册

> 一套**所有项目统一采用**的文档结构,让 Claude Code / Codex 等 agent 顺着
> 「入口 → 细节」逐层导航就能拿到准确、就近的上下文与约束,并用一个**跨工具的
> `check-docs` 命令**把「文档与代码同步」变成 agent 收尾时必过的关卡。
>
> 本目录(`coding-md/`)就是工具箱:改造任何新/老项目时,来这里抄
> [templates/](templates/) 的模板、拿 [scripts/check-docs.mjs](scripts/check-docs.mjs) 的脚本。

---

## 一、为什么需要它

用 AI agent 写项目时,agent 常常不遵循既定规范。根因不是 agent 不听话,而是
**文档没及时更新 → agent 缺少准确、就近的上下文**。本系统用两条主线解决:

1. **逐层导航**:`CLAUDE.md → AGENTS.md →(模块文档 / 文件头 / 各类规范 / specs / plans)`。
   agent 永远有唯一入口,且能就近找到该模块的细节。
2. **强制同步**:一个 agent 自己就能调用的 `check-docs` 命令,把「改了代码必须同步文档」
   从「靠自觉」变成「流程里必跑的一步」(Claude / Codex 通用)。

> **三层 harness 视角**(借鉴 harness engineering:`Agent = Model + Harness`):
> ① **知识层**(本系统主体:AGENTS.md / docs / 文件头 / specs / plans)给 agent 上下文;
> ② **强制层**(hooks + sensors,见第七节)用机制而非 prompt 锁住规范;
> ③ **验证层**(测试 / E2E / 完成闸门,见 docs/testing.md)用「真跑」确认结果。
> `check-docs` 是强制层的第一个 sensor。光有知识层 agent 不一定 follow,三层合起来才稳。

---

## 二、目标结构

改造后,每个项目长这样(类型差异只体现在 `AGENTS.md` 第 2 节和模块文档里):

```
<project>/
├── CLAUDE.md          # 仅一句:先读并遵循 AGENTS.md
├── AGENTS.md          # ⭐ 唯一入口:是什么 / 技术架构 / 红线 / 工作流 / 导航索引
├── design.md          # 仅 UI 项目:设计系统
├── docs/
│   ├── modules/<module>/README.md  # 每模块一个文件夹(总览)+ 可加子页 <topic>.md
│   ├── topics/<topic>.md           # 跨模块 / 复杂的独立专题
│   ├── run.md         # 运行手册:装/跑/构建/测/lint
│   ├── conventions.md # 编码规范 + 术语表 + 评审自查清单
│   ├── testing.md     # 测试策略
│   ├── specs/         # 需求/spec(Spark 产物落在这里)
│   ├── plans/         # 实现计划/任务拆解
│   └── decisions/     # ADR 决策日志
├── scripts/
│   ├── check-docs.mjs # 防漂移检查器(sensor)
│   └── hooks/         # 跨工具共享 hook 脚本(guard / format-lint / pre-commit)
├── .claude/{settings.json, commands/sync-docs.md}        # Claude hooks + /sync-docs 命令
├── .codex/{hooks.json, config.toml, prompts/sync-docs.md}  # Codex hooks + prompt(prompt 需软链到 ~/.codex/prompts/)
└── src/…              # 每个源文件顶部有轻量 AI 文件头
```

**三条铁律**
- 一切**入口**收敛到 `AGENTS.md` 的导航索引。
- 一切**细节文档**收敛到 `docs/`。
- **文件级**信息只放在文件头,不在 `docs/` 重复(避免双重过期)。

---

## 三、各组件速览(详见对应模板)

| 组件 | 模板 | 要点 |
|---|---|---|
| 入口跳转 | [templates/CLAUDE.md](templates/CLAUDE.md) | 一句话,把 agent 引到 AGENTS.md |
| 入口枢纽 | [templates/AGENTS.md](templates/AGENTS.md) | 是什么 / 技术架构 / 🚨红线 / 工作流(DoD) / 📚导航 |
| 文件头 | [templates/file-headers/](templates/file-headers/) | 轻量四字段 `@purpose/@role/@deps/@gotcha`,**不写函数签名** |
| 模块文档 | [templates/docs/modules/_module/](templates/docs/modules/_module/) | 每模块一个文件夹:README 总览 + 子页;职责/文件关系/数据流/接口/坑 |
| 专题文档 | [templates/docs/topics/_topic.md](templates/docs/topics/_topic.md) | 跨模块 / 复杂、不属于单一模块的独立说明 |
| 运行手册 | [templates/docs/run.md](templates/docs/run.md) | 可直接复制的命令 |
| 规范+术语 | [templates/docs/conventions.md](templates/docs/conventions.md) | 命名/结构/错误/提交 + 术语表 + 评审清单 |
| 测试策略 | [templates/docs/testing.md](templates/docs/testing.md) | 测什么/放哪/怎么跑/怎么验证真生效 |
| 需求 | [templates/docs/specs/_spec.md](templates/docs/specs/_spec.md) | 为什么做/做什么/成功标准 |
| 计划 | [templates/docs/plans/_plan.md](templates/docs/plans/_plan.md) | 方案/涉及文件/任务拆解 |
| 决策日志 | [templates/docs/decisions/](templates/docs/decisions/) | 每条决策一文件:背景/决策/理由/后果 |
| 设计系统 | [templates/design.md](templates/design.md) | 仅 UI:token/组件/交互/无障碍 |
| 检查器 | [scripts/check-docs.mjs](scripts/check-docs.mjs) | 缺头 / 漂移 / 失效引用 |
| 共享 hook 脚本 | [templates/scripts/hooks/](templates/scripts/hooks/) | guard(拦危险命令)/ format-lint(回灌)/ pre-commit |
| Claude hooks | [templates/.claude/settings.json](templates/.claude/settings.json) | PreToolUse / PostToolUse / Stop 接到共享脚本 |
| Codex hooks | [templates/.codex/](templates/.codex/) | hooks.json + config.toml,指向同一批脚本 |
| 手动命令 /sync-docs | [templates/.claude/commands/sync-docs.md](templates/.claude/commands/sync-docs.md) · [templates/.codex/prompts/sync-docs.md](templates/.codex/prompts/sync-docs.md) | 主动发给 agent:检查并**修复**文档漂移(Claude slash / Codex prompt) |

---

## 四、新项目改造流程

1. **拷骨架**:把 [templates/](templates/) 里的 `CLAUDE.md`、`AGENTS.md`、`docs/`、
   `scripts/`(含 `check-docs.mjs` 与 `hooks/`)、`.claude/`、`.codex/` 拷进项目根;
   UI 项目再拷 `design.md`,否则删掉它并去掉 `AGENTS.md` 导航里对应条目。
2. **填入口**:补全 `AGENTS.md` 第 1–3 节(是什么 / 技术架构 / 🚨红线)。
3. **边写边记**:每建一个源文件就加文件头;每形成一个模块就写 `docs/modules/<module>/README.md`
   (跨模块 / 复杂的写 `docs/topics/<topic>.md`),并登记到 `AGENTS.md` 导航索引。
4. **填规范**:补 `docs/run.md`、`docs/conventions.md`、`docs/testing.md`。
5. **装强制层**:见第七节,给 Claude(`.claude/settings.json`)和 Codex(`.codex/` + `/hooks` 信任)接上
   共享 hook 脚本;按技术栈填 `scripts/hooks/format-lint.mjs` 的 lint 命令;装 git `pre-commit`。
6. **装 /sync-docs 命令**:Claude 的 `.claude/commands/sync-docs.md` 随仓库即用;Codex 把
   `.codex/prompts/sync-docs.md` 软链到 `~/.codex/prompts/`(`ln -sf "$PWD/.codex/prompts/sync-docs.md" ~/.codex/prompts/`)。

## 五、老项目改造流程(可分模块增量做,不必一次到位)

1. **摸现状**:放入 [scripts/check-docs.mjs](scripts/check-docs.mjs),先跑一次
   `node scripts/check-docs.mjs` 看缺头清单,对项目规模心里有数。
2. **逐模块梳理**:从顶层目录识别模块,逐个写 `docs/modules/<module>/README.md`,同时给该模块源文件补文件头;跨模块的复杂逻辑写成 `docs/topics/<topic>.md`。
3. **写入口**:写 `AGENTS.md`(是什么/技术架构/红线/工作流/导航索引)+ 一句话 `CLAUDE.md`。
4. **补规范**:`docs/run.md` / `conventions.md` / `testing.md`;有 UI 补 `design.md`。
5. **回填决策**:把历史上重要的技术决策补成 `docs/decisions/` 里的 ADR。
6. **清零**:反复跑 `check-docs` 到没有 ❌,之后纳入日常 DoD。

> 改造顺序建议:先 `AGENTS.md` 骨架 + `check-docs.mjs`,再按「最常被改动的模块」优先补文档。

---

## 六、日常协作循环(Definition of Done)

agent 每次任务都走 `AGENTS.md` 第 4 节的 DoD,核心是:
**读相关模块文档 → (大改先写 plan) → 改代码 → 同步文件头+模块文档+(必要时)ADR →
写/跑测试 → 评审自查 → 跑 `check-docs` 清掉 ❌ → 按规范提交。**

`check-docs` 是收尾闸门,有四种触发,覆盖两种工具:
- **DoD 自跑**(通用主力):agent 收尾前自己 `node scripts/check-docs.mjs`,Claude 和 Codex 都适用。
- **Stop hook**(自动拦截):Claude / Codex 收尾自动跑 `check-docs --hook`,有 ❌ 则 `exit 2` 阻止收尾,见第七节。
- **手动命令 `/sync-docs`**(主动修复):做完一段工作发给 agent,让它**检查并主动修好**漂移/缺头/失效引用。Claude 用 `.claude/commands/`(随仓库共享);Codex 把 `.codex/prompts/sync-docs.md` 软链到 `~/.codex/prompts/`,用 `/prompts:sync-docs`。
- **人工审计**:随时手动跑,尤其适合老项目改造和定期体检。

---

## 七、强制层:sensors & hooks(Claude + Codex)

光靠文档(prompt)agent 不一定 follow;**真正起作用的是「用机制强制」**。把检查按速度分层,越快越好:

> PostToolUse(毫秒)→ pre-commit(秒)→ CI(分钟)→ 人审(小时)

**两个工具的 hook 现在 schema 基本一致**(相同事件名、stdin JSON、deny 输出),所以 hook
逻辑只写一份在 `scripts/hooks/`,两边各用一个薄配置指过去:

| 事件 | 干什么 | 脚本 | Claude | Codex |
|---|---|---|---|---|
| PreToolUse(Bash) | 拦危险命令(rm -rf / --no-verify / 强推 / 破坏性 SQL / 动 .env) | `scripts/hooks/guard.mjs` | ✅ | ✅(Bash) |
| PreToolUse(改文件) | 拦改 .env / 密钥 / lint 配置 | `scripts/hooks/guard-files.mjs` | ✅(Write\|Edit) | ⚠️(apply_patch best-effort) |
| PostToolUse(改文件) | 自动格式化/lint,报错回灌 agent 自纠 | `scripts/hooks/format-lint.mjs` | ✅(Write/Edit) | ✅(apply_patch=Edit\|Write) |
| Stop | 收尾跑文档检查,**有 ❌ 则 exit 2 拦截收尾** | `node scripts/check-docs.mjs --hook` | ✅ | ✅ |
| pre-commit(git) | 与 AI 工具无关的最后兜底 | `scripts/hooks/pre-commit` | ✅ | ✅ |

- **Claude Code**:配置在 `.claude/settings.json`(模板已给)。
- **Codex**:配置在 `.codex/hooks.json` + `.codex/config.toml` 开 `codex_hooks`(实验特性);
  首次用 `/hooks` 审阅并**信任**脚本。Codex hook 是「护栏非安全边界」,只覆盖 Bash/apply_patch/MCP。
- **DoD 自跑仍是通用底座**:即便 hook 没装或被绕过,agent 收尾也要手动跑
  `node scripts/check-docs.mjs` + 项目 lint/test。`--strict` 让 ⚠️ 也算失败。
- **Stop 才真拦**:Stop 跑 `check-docs --hook`,有 ❌ 时 `exit 2`,Claude/Codex 才会阻止收尾并把原因反馈给 agent(普通 `exit 1` 不拦)。所以平时要把 ❌ 保持为 0。
- **CI / 审分支**:`node scripts/check-docs.mjs --base <主干>`(用 `git diff <主干>...HEAD`)才能查"已提交但代码改了文档没改";不带 `--base` 只看未提交工作区,**提交后或 CI 里看不到漂移**。

> 落地原则:能交给确定性工具(linter/typecheck/test)的强制,就别只写进文档(文档会腐烂)。
> 每次 agent 犯错,顺手加一条规则/测试(**Ratchet 棘轮**),让同样的错不再犯。

---

## 八、check-docs 检查什么

| 级别 | 检查项 | 说明 |
|---|---|---|
| ❌ 错误 | 缺 AI 文件头 | 源文件顶部没有 `@purpose` 标记 |
| ❌ 错误 | 失效引用 | 文档/文件头里引用的本地路径不存在 |
| ⚠️ 警告 | 疑似文档漂移 | 改了某模块代码,但 `docs/modules/<module>/` 下文档没跟着改(需 git) |

- 默认有 ❌ 才 `exit 1`;`--strict` 让 ⚠️ 也算失败。
- `--hook`:Stop hook 模式,有 ❌ 时 `exit 2`(只有 exit 2 才能真正拦截 Claude/Codex 收尾)。
- `--base <ref>`:漂移检测改对比 `git diff <ref>...HEAD` 的已提交差异(供 CI / 审分支);否则只看未提交工作区。
- 非 git 仓库自动跳过漂移检查。
- 可在项目根放 `check-docs.config.json` 覆盖默认(源码后缀、源码根目录、忽略目录、docs 目录等)。

---

## 九、设计取舍备忘
- **文件头轻量**:只写职责级信息,不写函数签名——签名最易过期,看代码即可。
- **跨工具优先**:防漂移以「agent 自跑的命令」为底座(两工具通用);hook 逻辑只写一份在
  `scripts/hooks/`,Claude 与 Codex 各用一个薄配置指过去(Codex hook 仍是实验特性)。
- **机制 > prompt(Ratchet)**:能用 linter/typecheck/test 强制的就别只写文档;agent 每犯一次错,固化一条守卫。
- **Design for rot**:agent 把任何能 grep 到的文本都当权威 → 过期 prose 比缺失更有害;
  约束尽量用可执行制品(test / schema / 文件头 + check-docs)承载,文档保持精简高信号。
- **统一内核 + 按需附加**:所有项目共用同一套结构,类型差异收敛在 `AGENTS.md` 技术架构节;有 UI 才加 `design.md`。
- **随模型变强做减法**:harness 组件都编码了「模型还做不到的假设」,模型进步后该剪的就剪。
- **刻意未含**:项目现状/路线图、部署/环境 runbook。结构开放,需要时按同样方式加一个 `docs/` 文档 + 一条导航条目即可。

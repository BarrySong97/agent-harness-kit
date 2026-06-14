<!-- 编码规范 + 术语表 + 评审自查清单。填写后删掉本注释。 -->

# 编码规范

## 命名
- 文件 / 目录:`<kebab-case / snake_case …>`
- 变量 / 函数:`<camelCase …>`
- 类型 / 类:`<PascalCase …>`
- 常量:`<UPPER_SNAKE …>`

## 目录与文件结构
- <一个文件只做一件事;过大就拆。模块怎么分层。>
- <每个源文件顶部必须有 AI 文件头(职责/角色/依赖/坑),见 file-headers 规范。>

## 架构边界(尽量机械强制)
- <模块/层之间的依赖方向。例:UI → Service → Repo → Config → Types,不得反向或跨层。>
- 重要边界**配可执行规则**(linter 规则 / 结构测试),让 CI 失败 = 强制,而不只是文字约定。
- 边界规则与对应 [ADR](decisions/) 关联(archgate:一条 ADR 配一条可执行规则)。

## Ratchet(棘轮原则)
- agent 犯了错,别只修这一处:**固化成一条 test / lint 规则 / ADR**,保证同样的错不再犯。
- 能用确定性工具(linter / typecheck / test)强制的,就别只写进文档——文档会腐烂,检查不会。

## 错误处理
- <抛错还是返回 Result?错误怎么分类?边界在哪捕获?>

## 日志
- <日志级别、格式、敏感信息脱敏。>

## 提交规范
- 格式:`<type(scope): subject>`(type: feat/fix/docs/refactor/test/chore)
- <一次提交一件事;信息说清「为什么」。>

---

# 术语表(领域词汇,中英对照)
> 统一命名,避免 agent 用错词。
| 术语 | 英文 / 标识符 | 含义 |
|---|---|---|
| <下单> | `<placeOrder>` | <…> |
| <…> | `<…>` | <…> |

---

# 评审自查清单(收尾前对照)
- [ ] 改动小而内聚,没有夹带无关重构
- [ ] 命名、风格与周边代码一致
- [ ] 没有违反 AGENTS.md 的红线规则
- [ ] 涉及文件的 AI 文件头已更新
- [ ] 对应 `docs/modules/<module>/`(或跨模块 `docs/topics/`)已同步;决策性改动已补 ADR
- [ ] 测试已写/更新并跑通
- [ ] `node scripts/check-docs.mjs` 无 ❌

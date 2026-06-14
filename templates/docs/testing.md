<!-- 测试 & 验证策略。Harness 原则:验证靠「真跑」,不靠「读代码」。填写后删掉本注释。 -->

# 测试 & 验证策略

## 原则
- **真跑验证**:不要只看测试通过 / 只读代码就收工——把受影响路径真跑一遍。
- **返回结构化文本**:E2E 用无障碍树 / JSON / stdout 做判定,**别用截图**(确定性、可进 CI)。
- **生成与执行分离**:让 agent 写测试,但在 CI 里确定性地跑。
- **完成闸门**:把测试命令加进 Stop hook 或 pre-commit 才算真强制(要拦截须 `exit 2`,参考 `check-docs --hook` 写法;见 .claude/settings.json / .codex/hooks.json)。
- **聚焦子集**:别每次全量(几分钟的全量会淹没 agent 上下文),先跑受影响的最小集。

## 测什么
- 单元:<纯逻辑 / 工具 / 算法>
- 集成:<模块协作 / DB / 外部服务>
- E2E:<关键用户路径>

## 按应用类型选 E2E 工具
| 类型 | 工具 | 要点 |
|---|---|---|
| Web | Playwright CLI / agent-browser | 用无障碍树交互,确定性 |
| 移动 iOS | XcodeBuildMCP | 生成与运行分离 |
| CLI | bats-core / pexpect | 逻辑包成可 source 的函数再测 |
| API | Hurl / Pact | 纯文本 HTTP 定义,agent 易读写 |
| Desktop / Electron | Playwright / WebdriverIO | 经 Chromium DevTools Protocol 接入 |
| 基础设施 | terraform test / conftest+OPA | policy-as-code,确定性护栏 |

## 怎么跑
见 [run.md](run.md) 的「测试」节。

## 覆盖期望
<核心模块覆盖率门槛 / 必须有测试的部分>

## 验证某个改动是否真生效
- 把应用真跑起来,走一遍受影响路径;
- UI:用 agent-browser 打开页面 / 读无障碍树确认;
- 接口:实际请求一次看返回。

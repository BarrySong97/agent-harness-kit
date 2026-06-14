<!-- 运行手册 —— 让 agent 不靠猜就能跑起项目。命令必须可直接复制执行。填写后删掉本注释。 -->

# 运行手册

## 环境要求
- 运行时:`<node 20 / python 3.12 / go 1.22 …>`
- 包管理:`<pnpm / npm / poetry / go mod …>`
- 其他依赖:`<数据库 / Redis / 环境变量文件 .env …>`

## 安装
```bash
<pnpm install>
```

## 本地启动
```bash
<pnpm dev>
```
- 访问地址:`<http://localhost:xxxx>`
- 需要的环境变量 / 配置:`<见 .env.example>`

## 构建
```bash
<pnpm build>
```

## 测试
```bash
<pnpm test>            # 全部
<pnpm test path/to>    # 单个
```

## Lint / 格式化 / 类型检查
```bash
<pnpm lint>
<pnpm typecheck>
```

## 文档同步检查(收尾必跑)
```bash
node scripts/check-docs.mjs
```

## 常用脚本
- `<脚本/命令>` — <干嘛的>

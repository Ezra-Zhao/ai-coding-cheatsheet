# AI 编程助手速查表（中文）

> 核实日期 2026-10-06：所有命令、快捷键、配置项逐条对照官方文档核实，核实不了的已删除。
> 官方文档：Claude Code <https://docs.anthropic.com/en/docs/claude-code> · Codex <https://developers.openai.com/codex> · Cursor <https://docs.cursor.com>
> 版本迭代快，存疑处以官方文档为准。

---

## 1. Claude Code（Anthropic 终端 AI 编程助手）

### 1.1 启动与会话

| 命令 | 说明 | 示例 |
|---|---|---|
| `claude` | 启动交互式会话 | `claude` |
| `claude "问题"` | 带初始提示启动 | `claude "解释这个项目的结构"` |
| `claude -p "问题"` | 非交互：问完即退出（`--print` 同义） | `claude -p "这个函数是做什么的"` |
| `cat f.log \| claude -p "分析"` | 管道输入做分析 | `cat err.log \| claude -p "找出报错原因"` |
| `claude -c` | 继续当前目录最近一次会话 | `claude -c` |
| `claude -r "名" "继续"` | 按名称/ID 恢复指定会话 | `claude -r "auth-refactor" "继续改"` |
| `claude update` | 升级到最新版本 | `claude update` |
| `claude doctor` | 诊断安装与配置问题 | `claude doctor` |
| `claude auth login` | 登录账号 | `claude auth login` |
| `claude --version` | 查看版本号 | `claude --version` |

### 1.2 常用启动参数

| 参数 | 说明 | 示例 |
|---|---|---|
| `--model <名>` | 指定模型 | `claude --model sonnet` |
| `--permission-mode <模式>` | 权限模式：`plan` / `acceptEdits` / `auto` / `manual` / `dontAsk` / `bypassPermissions` | `claude --permission-mode plan` |
| `--dangerously-skip-permissions` | 跳过全部权限确认（仅可信环境用） | — |
| `--allowedTools "Bash(git *)" "Read"` | 这些工具免确认直接执行 | `claude --allowedTools "Bash(git *)" "Read"` |
| `--add-dir <路径>` | 额外可读写的工作目录 | `claude --add-dir ../shared-lib` |
| `--agent <名>` | 用指定 subagent 启动会话 | `claude --agent reviewer` |

### 1.3 会话内快捷键与 slash 命令

| 输入 | 说明 |
|---|---|
| `Shift+Tab` | 循环切换权限模式（含 plan 模式） |
| `Esc` | 中断当前执行 |
| `/help` | 查看全部可用命令（最权威的列表） |
| `/init` | 为当前项目生成 CLAUDE.md 初稿 |
| `/model` | 切换模型 |
| `/context` | 查看上下文占用与已加载的记忆文件 |
| `/doctor` | 会话内诊断，可自动修复配置问题 |
| `/cd <目录>` | 切换会话工作目录 |
| `/usage`（别名 `/cost`） | 查看用量 |
| `/review` | 代码评审（内置 skill） |
| `/debug` `/batch` `/loop` `/verify` `/run` | 内置 skills：调试、批量任务、循环执行、验证运行 |

### 1.4 CLAUDE.md 项目记忆

| 位置 | 作用域 |
|---|---|
| `./CLAUDE.md` 或 `./.claude/CLAUDE.md` | 项目级，随仓库提交，团队共享 |
| `~/.claude/CLAUDE.md` | 个人全局，所有项目生效 |
| `./CLAUDE.local.md` | 个人本地偏好，记得加进 `.gitignore` |
| `.claude/rules/*.md` | 按文件路径分作用域的规则（frontmatter 写 `paths`） |
| `AGENTS.md` | 也会被读取，可与 CLAUDE.md 并存 |

要点：

- 写"可验证的具体指令"（如"提交前必须跑 `npm test`"），不写空话
- 单个文件控制在 200 行以内；长流程拆成 skill，别全塞进 CLAUDE.md
- 用 `@路径` 在 CLAUDE.md 里导入其他文件
- `/init` 可自动生成初稿，再人工精修

### 1.5 Skills 与 MCP

- Skill 就是一个 `SKILL.md` 文件：个人放 `~/.claude/skills/<名>/SKILL.md`，项目放 `.claude/skills/<名>/SKILL.md`
- 会话内用 `/<技能名>` 直接调用；相关场景下 Claude 会自动触发
- 内置 skills：`/doctor` `/code-review` `/batch` `/debug` `/loop` `/claude-api` `/verify` `/run`
- `claude mcp`：管理 MCP 服务器（查看、添加、登录），把外部工具接进来
- `claude plugin`：管理插件

### 1.6 工作流技巧

1. 大改动先 plan：`--permission-mode plan` 启动，或会话内 `Shift+Tab` 切到 plan 模式，让 AI 先出计划、你点头再动手
2. 任务拆小步，每步 `git diff` 看一眼再继续
3. 动手前让 AI 先复述理解："先说说你打算改哪几个文件"
4. 重复流程写成 skill，别每次重新讲一遍
5. 一个任务开一个新会话，别在一个会话里堆太多事

---

## 2. Codex CLI（OpenAI 终端智能体）

安装：`npm install -g @openai/codex`

### 2.1 命令

| 命令 | 说明 | 示例 |
|---|---|---|
| `codex` | 启动交互式会话 | `codex` |
| `codex exec "任务"` | 非交互执行，适合脚本和 CI | `codex exec "修好失败的测试"` |
| `codex resume` | 恢复之前的会话 | `codex resume` |
| `codex mcp` | 管理 MCP 服务器 | `codex mcp` |
| `codex cloud` | 把任务交到云端执行 | `codex cloud` |
| `codex --image 图.png` | 首提示附带图片（如报错截图） | `codex --image err.png "分析这个报错"` |
| `codex --search` | 本次运行启用联网搜索 | `codex --search "查最新 API 用法"` |
| `codex completion` | 生成 shell 命令补全 | `codex completion` |

### 2.2 审批与沙盒（Codex 的特色："能碰什么"和"何时问你"分开管）

| 参数 | 说明 | 示例 |
|---|---|---|
| `-a, --ask-for-approval <策略>` | 何时停下来问你：`on-request`（默认）/ `never` | `codex -a never exec "跑测试并修复"` |
| `-s, --sandbox <模式>` | 能碰什么：`read-only` / `workspace-write` / `danger-full-access` | `codex -s read-only "解释这段代码"` |
| `--dangerously-bypass-approvals-and-sandbox` | 审批和沙盒全放开（仅可信隔离环境用） | — |
| `--profile <名>` | 使用指定的配置 profile | `codex --profile work` |

会话内输入 `/permissions` 可查看并调整本次运行的权限边界。

### 2.3 AGENTS.md 与配置

- 项目根目录放 `AGENTS.md`：构建命令、代码规范、禁止事项，Codex 每次自动读取
- 用户级配置 `~/.codex/config.toml`；项目级覆盖 `.codex/config.toml`（需先信任该项目）
- 常用配置项：`model`、`approval_policy = "on-request"`、`sandbox_mode`

### 2.4 IDE 插件要点

- 有 VS Code 插件：在编辑器里直接对话、看 diff、分享选中代码
- 插件与 CLI 共用同一套 `AGENTS.md`、配置与 MCP 服务器

---

## 3. Cursor（AI 原生 IDE）

### 3.1 核心快捷键（Mac 用 Cmd，Windows/Linux 用 Ctrl）

| 快捷键 | 说明 |
|---|---|
| `Cmd/Ctrl+K` | 行内编辑：选中代码，直接说怎么改 |
| `Cmd/Ctrl+L` | 打开对话：选中的代码会自动带入上下文 |
| `Cmd/Ctrl+I` | 打开 Agent/Composer：多文件自动改写 |
| `Cmd/Ctrl+Shift+L` | 把选中内容追加到当前对话 |
| `Cmd/Ctrl+Enter` | 发送；Agent 忙时可排队追问 |
| `Tab` | 接受 AI 补全（Cursor Tab，需在设置中开启） |
| `Esc` | 关闭补全建议 / 中断 AI |

### 3.2 @ 引用（给 AI 指定上下文）

| 写法 | 说明 |
|---|---|
| `@Files` / `@Folders` | 引用指定文件 / 文件夹 |
| `@Codebase` | 整个代码库语义搜索 |
| `@Docs` | 引用文档 |
| `@Web` | 联网搜索 |
| `@Git` | 引用 git 历史与 diff |
| `@<文件名>` | 直接 @ 文件名也行 |

### 3.3 Project Rules（`.cursor/rules/`）

- 一个规则一个 `.mdc` 文件：`description` + `globs`（匹配哪些文件）+ 正文指令
- 四种生效方式：Always（总生效）/ Auto Attached（按 globs 自动带入）/ Agent Requested（AI 自行决定用不用）/ Manual（@ 引用才用）
- 个人全局规则在 Cursor 设置 → Rules 里写
- 团队共享的规则放仓库里提交；只给自己用的别提交

### 3.4 Agent / Plan 模式

- Ask：只问不改，适合先搞懂代码
- Agent：自动改文件、跑命令，diff 逐个确认后再接受
- Plan：先出实施计划，你点头后再执行
- 模式在输入框上方切换；界面更新快，以官方文档为准

### 3.5 Tab 补全设置

- 设置里搜 "Cursor Tab" 开启；`Tab` 接受、`Esc` 拒绝
- 觉得干扰就关掉，或只在特定语言下开启

---

## 4. 通用：.gitignore / prompt 技巧 / 安全

### 4.1 .gitignore 要点

```gitignore
.env*             # 密钥、token
*.pem *.key       # 私钥、证书
node_modules/     # 依赖目录
dist/ build/      # 构建产物
.DS_Store
CLAUDE.local.md   # 个人本地 AI 记忆，不提交
```

Cursor 另有 `.cursorignore`：不想被 AI 索引的文件写进去。

### 4.2 prompt 技巧

1. 先讲目标再讲做法："我要给登录加限流，改这 3 个文件，你先说说方案"
2. 要求可验证："改完跑一遍测试，把结果贴出来"
3. 小步快跑：一次只让 AI 做一件事
4. 给负面清单："别动数据库 schema，别装新依赖"
5. 复杂任务让 AI 先说计划，你点头再动手

### 4.3 安全红线

- 密钥、token、私钥、客户数据绝不贴进对话
- `--dangerously-skip-permissions` / `--dangerously-bypass-approvals-and-sandbox` 只在可信隔离环境用
- API key 放环境变量（如 `ANTHROPIC_API_KEY`），别写进代码和 prompt
- AI 要执行的删库、发版类命令，先看一眼再回车
- 公开仓库不提交 `.env`、内网地址、真实用户信息

---

MIT License · Copyright (c) 2026 Guangyi Zhao

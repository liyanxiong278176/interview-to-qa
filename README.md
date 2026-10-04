# interview-to-qa

一个用于技术面试演练与知识复盘的 Agent Skill：对技术讲解评分，根据内容确定有限追问次数，逐题检验理解，最后将讲解、问答和纠错整理成连贯准确的学习 QA。

**讲解 → 初评 → 有限追问 → 最终评分 → 学习 QA**

## 作用与适用场景

- **讲解评分**：评估技术准确性、边界与方案完整性、工程落地和表达，指出主要优点与实质性缺口。
- **动态确定追问次数**：根据核心主题、复杂度、关键缺口和岗位深度确定本轮追问预算，不固定为 3 次；证据充分时提前结束。
- **逐题追问**：每次只问一题，等待回答后评分和纠错，再继续下一题，避免重复追问或无限加题。
- **知识沉淀**：把原讲解、追问回答和必要修正融合为 QA，按概念依赖组织，保留关键术语与前提，消除重复和矛盾。
- **直接复盘**：已有完整对话时，可以只整理 QA，不重新面试。

适合技术面试准备、学习后的口述检验、技术分享复盘。支持不同技术主题；附带 Agent 安全、Function calling、MCP 和幂等重试的可选参考案例。

默认按中级应用开发岗位练习，评分为百分制：技术准确性 40、边界与方案完整性 30、工程落地 20、表达 10。可以指定其他岗位或标准。评分是模型提供的练习反馈，不是招聘结论。

这是一个以 Markdown 指令为主的 skill，不需要运行项目代码、启动 MCP Server 或安装额外 Python/Node.js 依赖。使用它需要支持技能或能读取指令文件的 AI 工具；Git 仅用于下面的下载和更新命令。

## 下载与安装

仓库地址：<https://github.com/liyanxiong278176/interview-to-qa>

本仓库根目录就是技能目录，`SKILL.md` 直接位于根目录。安装后的正确结构是 `<技能目录>/interview-to-qa/SKILL.md`，避免解压后多嵌套一层。

### 选择安装位置

| 工具 | 用户级目录（跨本机项目） | 项目级目录（当前项目） |
| --- | --- | --- |
| Codex | `~/.agents/skills/interview-to-qa` | `.agents/skills/interview-to-qa` |
| Claude Code | `~/.claude/skills/interview-to-qa` | `.claude/skills/interview-to-qa` |
| Cursor | `~/.cursor/skills/interview-to-qa` | `.cursor/skills/interview-to-qa` |
| Gemini CLI | `~/.gemini/skills/interview-to-qa` | `.gemini/skills/interview-to-qa` |

`~` 表示用户主目录，例如 Windows 的 `C:\Users\你的用户名`。项目级路径相对于项目根目录。以上为本地安装位置，云端会话需要按相应工具的规则单独配置。

Codex 路径依据 [官方本地技能文档](https://learn.chatgpt.com/docs/build-skills)。本技能最初在 Codex 桌面环境的 `~/.codex/skills/interview-to-qa` 中创建并使用；新安装优先使用上表的目录。同一工具不要重复安装同名 skill，以免出现多个选择项。

Claude Code 与 Cursor 的目录分别见 [Claude Code 官方文档](https://code.claude.com/docs/en/skills) 和 [Cursor 官方文档](https://cursor.com/docs/skills)。下述 Cursor 方法是手动安装本地技能，不是 marketplace 插件导入。

### Windows：PowerShell 下载

以下以 Codex 用户级安装为例。使用其他工具时，将 `.agents/skills` 替换为上表对应目录；用户已有同名目录时，按后面的更新说明处理。

```powershell
$skillRoot = Join-Path $env:USERPROFILE '.agents/skills'
$skillTarget = Join-Path $skillRoot 'interview-to-qa'
New-Item -ItemType Directory -Path $skillRoot -Force | Out-Null
git clone https://github.com/liyanxiong278176/interview-to-qa.git $skillTarget
```

如果仅安装到当前项目，在项目根目录执行，并把 `$skillRoot` 改成对应的相对路径，例如 `'.agents/skills'`。

### macOS / Linux：终端下载

同样以 Codex 用户级安装为例；Claude Code 替换成 `.claude/skills`，Cursor 替换成 `.cursor/skills`。

```bash
mkdir -p ~/.agents/skills
git clone https://github.com/liyanxiong278176/interview-to-qa.git ~/.agents/skills/interview-to-qa
```

仅安装到当前项目时，在项目根目录执行：

```bash
mkdir -p .agents/skills
git clone https://github.com/liyanxiong278176/interview-to-qa.git .agents/skills/interview-to-qa
```

### 不使用 Git：下载 ZIP

1. 打开仓库页面，选择 **Code → Download ZIP**。
2. 解压后，将包含 `SKILL.md`、`agents/` 和 `references/` 的文件夹命名为 `interview-to-qa`。
3. 将整个文件夹放入对应工具的技能目录，不要只复制 `SKILL.md`。
4. 重新打开会话；如未发现技能，重启工具或使用其技能刷新入口。

### Gemini CLI：使用内置安装命令

```bash
gemini skills install https://github.com/liyanxiong278176/interview-to-qa.git --scope user
gemini skills list
```

仅安装到当前项目时，将 `--scope user` 改为 `--scope workspace`。在已打开的 Gemini CLI 会话中，可以执行 `/skills reload` 刷新、`/skills list` 查看。安装参数与目录见 [Gemini CLI 官方文档](https://geminicli.com/docs/cli/skills/)。

## 如何调用

技能名称统一为 **`interview-to-qa`**。选择入口由 AI 工具决定，`/interview-to-qa` 和 `$interview-to-qa` 不是所有工具通用的命令。

| 工具 | 调用方式 |
| --- | --- |
| Codex 桌面端 | 在提供 `/` 技能选择菜单的版本中，输入 `/interview-to-qa` 并选择同名 skill，再附上讲解。 |
| Codex CLI / IDE 扩展 | 使用 `$interview-to-qa` 引用；也可通过 `/skills` 选择。 |
| Claude Code | 输入 `/interview-to-qa`，后接任务或讲解。 |
| Cursor | 在 Agent 聊天中输入 `/`，搜索并选择 `interview-to-qa`。 |
| Gemini CLI | 安装后用自然语言明确要求使用 `interview-to-qa`；使用 `/skills list` 检查是否已发现。 |

Codex CLI / IDE 的 `$` 和 `/skills` 入口见 [官方调用说明](https://learn.chatgpt.com/docs/build-skills)；Claude Code 和 Cursor 的 `/` 入口见各自上面的官方文档。桌面端菜单随版本变化，以实际可见的技能选择器为准。只输入技能名时，模型会先请你提供讲解。

### 示例 1：完整面试与 QA 复盘

以下示例可用于支持 `$` 引用的 Codex 环境：

```text
$interview-to-qa

请作为中级 Agent 开发岗位面试官，对下面的讲解评分，
根据内容确定有限追问次数，每次一题，
结束后整理成逻辑连贯、准确的学习 QA。

我的讲解：
……
```

使用桌面端 `/` 选择入口或 Claude Code 时，将首行换为 `/interview-to-qa`，其余内容直接使用。Cursor 先选择该 skill，再发送任务。Gemini CLI 将首行换为“请使用 interview-to-qa 技能”。

模型先给初评与本轮追问预算，再逐题提问。你回答当前题后继续；追问结束后，模型给最终评分并整理 QA。可随时说“结束追问，直接整理 QA”。

### 示例 2：直接整理已有会话

```text
$interview-to-qa

将本会话的技术讲解、追问回答和纠错整理成学习 QA，不再追问。
要求逻辑连贯、无矛盾，以已有内容为主，不捏造项目经历或关键实体。
```

### 示例 3：指定练习范围

```text
$interview-to-qa

按高级后端开发岗位标准评估下面的讲解，重点检查并发与故障恢复。
追问最多 5 次，每次一题；结束后整理学习 QA。

我的讲解：
……
```

指定的题数或上限优先于模型自行判断。也可以明确要求“只评分，不追问、不整理 QA”。

### 示例 4：保存学习文档

```text
$interview-to-qa

将本会话整理为学习 QA，保存到当前项目的 docs/agent-safety-qa.md。
```

文件保存依赖当前工具的文件写入能力；没有该能力时，可以请求在聊天中输出 Markdown 后自行保存。

## 输出与边界

- **初评**：总分、各维度得分、主要优点和扣分依据。
- **追问反馈**：每题 10 分制反馈、正确点与关键修正；题目分数不直接累加到综合分。
- **最终评分**：按同一维度重新评估，并指出尚未验证或需要学习的内容。
- **学习 QA**：以用户讲解与回答为主，融入必要纠正，按概念依赖组织，不保留已知错误或捏造事实。

对版本相关或不确定的事实，skill 会要求优先核对官方资料。实际核验能力取决于宿主工具；无法核验时应说明不确定性。仓库提供技能文件和参考案例，不保证所有工具版本都具有相同入口、浏览或文件能力。

## 更新

通过 Git 安装后，在实际安装目录执行：

```bash
git pull --ff-only
```

如果有本地修改，先检查并保留，再处理更新；不要为了更新直接覆盖。ZIP 安装可重新下载并手动合并。更新后重新打开会话，必要时刷新技能或重启工具。

## 仓库结构

```text
interview-to-qa/
├── SKILL.md                         # 工作流与评分、追问、整理规则
├── README.md                        # 下载、安装与使用说明
├── agents/
│   └── openai.yaml                   # Codex 界面元数据
└── references/
    └── agent-safety-cases.md          # Agent 安全与工具调用参考案例
```

其他工具主要读取 `SKILL.md` 和按需引用的参考文件；`agents/openai.yaml` 是 Codex 的界面元数据。无需先安装 grill-with-docs、grilling 或 domain-modeling，本技能的工作流已自包含。

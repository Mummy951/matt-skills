---
name: setup-matt-pocock-skills
description: "为本仓库配置工程技能所需的环境：搭建 issue 追踪器、分诊标签词汇表以及领域文档布局。在首次使用其他工程技能之前先运行一次。"
disable-model-invocation: true
---

# 配置 Matt Pocock 的技能

为工程技能所假定的、按仓库拆分的配置搭建脚手架：

- **issue 追踪器**：issue 存放的位置（默认为 GitHub；开箱即用也支持本地 markdown）
- **分诊标签**：五个规范分诊角色所使用的字符串
- **领域文档**：`GLOSSARY.md` 与 ADR 存放的位置，以及阅读它们时的消费方规则

这是一个由提示词驱动的技能，而不是确定性脚本。先探索、呈现发现的情况、与用户确认，然后再写入。

## 流程

### 1. 探索

查看当前仓库，了解它的初始状态。读取一切已存在的内容，不要凭空假设：

- `git remote -v` 与 `.git/config`：这是一个 GitHub 仓库吗？是哪一个？
- 仓库根目录下的 `AGENTS.md` 与 `CLAUDE.md`：两者是否存在？其中是否已有 `## Agent skills` 小节？
- 仓库根目录下的 `GLOSSARY.md` 与 `GLOSSARY-MAP.md`，以及遗留的 `CONTEXT.md` / `CONTEXT-MAP.md`。如果存在遗留文件，可提议用 `git mv` 将其改名为对应的 `GLOSSARY` 文件，并在 `docs/agents/domain.md` 中改写这些名称。
- `docs/adr/` 以及任意 `src/*/docs/adr/` 目录
- `docs/agents/`：本技能之前的产物是否已经存在？
- `.scratch/`：表明本地 markdown issue 追踪器约定已经在使用中
- `triage` 技能是否已安装？（与本技能同目录下是否有 `triage` 技能文件夹，或在你可用的技能中是否有 `triage`）。这决定了 B 节是否需要运行。
- Monorepo 信号：`pnpm-workspace.yaml`、`package.json` 中的 `workspaces` 字段，或一个带有独立 `src/`、内容充实的 `packages/*`。这些只出现在真正大型的多包仓库中；它们缺失即意味着单限界上下文，而几乎所有仓库都属于这种情况。

### 2. 呈现发现并询问

汇总哪些已存在、哪些缺失。然后按顺序处理各小节。一次一个小节、一个回答，再进入下一个。

每个小节先给出推荐答案，以便用户一句话即可采纳。仅当选择确实会产生分支时，才附一行解释；如果探索阶段已经确定了答案，则整节跳过（未安装 `triage` 时跳过 B 节，非 monorepo 时跳过 C 节）。

**A 节：issue 追踪器。**

> 解释：「issue 追踪器」是本仓库 issue 存放的位置。`to-tickets`、`triage`、`to-spec` 等技能会从中读取并向其中写入。它们需要知道应当调用 `gh issue create`、在 `.scratch/` 下写入一个 markdown 文件，还是遵循你描述的其他工作流。选择你实际为本仓库跟踪工作的位置。

默认倾向：这些技能是为 GitHub 设计的。如果 `git remote` 指向 GitHub，就推荐 GitHub。如果 `git remote` 指向 GitLab（`gitlab.com` 或自托管主机），就推荐 GitLab。否则（或用户有偏好时），提供以下选项：

- **GitHub**：issue 存放在仓库的 GitHub Issues 中（使用 `gh` CLI）
- **GitLab**：issue 存放在仓库的 GitLab Issues 中（使用 [`glab`](https://gitlab.com/gitlab-org/cli) CLI）
- **本地 markdown**：issue 以文件形式存放在本仓库的 `.scratch/<feature>/` 下（适合个人项目或没有远程仓库的仓库）
- **其他**（Jira、Linear 等）：请用户用一段话描述工作流；技能将其记录为自由文本

将选择记录在 `docs/agents/issue-tracker.md` 中。GitHub 与 GitLab 模板带有一个「PR 作为诉求入口」开关，默认为**关闭**。保持关闭且不要主动提起：希望外部 PR 进入分诊队列的用户，之后可以自行在文件中翻转该开关。

GitHub 或 GitLab 追踪器需要一个可访问的仓库。当用户选择其中之一、但没有任何 `git remote` 指向该主机时，先向用户获取仓库 URL 并完成接入（必要时执行 `git init`，再 `git remote add origin <url>`），然后再继续。判断完成的标准是：在本目录内运行 `gh repo view` / `glab repo view` 能打印出该项目；如果做不到，停下来告诉用户缺少什么。

**B 节：分诊标签词汇表。** 如果 `triage` 技能未安装（探索阶段已告知），整节跳过，因为未安装的技能不需要标签。

如果已安装，只问一个问题：

> 你是否希望保留默认的分诊标签？（推荐：**是**）

默认值是五个规范角色，每个标签字符串与其名称相同：`needs-triage`、`needs-info`、`ready-for-agent`、`ready-for-human`、`wontfix`。选择**是**则原样写入。仅当用户选择否时（通常是因为其追踪器已使用其他名称，例如用 `bug:triage` 表示 `needs-triage`），才收集覆盖映射，以便 `triage` 应用已有标签，而不是创建重复标签。

**C 节：领域文档。** 默认为**单限界上下文**（仓库根目录下一个 `GLOSSARY.md` + `docs/adr/`）。这几乎适合所有仓库，直接写入，无需询问。

仅当探索发现了 monorepo 信号时，才提供**多限界上下文**方案（根目录 `GLOSSARY-MAP.md` 指向各上下文各自的 `GLOSSARY.md` 文件）。随后确认他们想要哪种布局。

### 3. 确认并编辑

向用户展示以下内容的草稿：

- 将被添加到 `CLAUDE.md` / `AGENTS.md` 中待编辑那一个文件里的 `## Agent skills` 区块（选择规则见第 4 步）
- `docs/agents/issue-tracker.md`、`docs/agents/domain.md` 与 `docs/agents/triage-labels.md` 的内容（最后一个仅在安装了 `triage` 时生成）

在写入之前允许他们先编辑。

### 4. 写入

**选择要编辑的文件：**

- 如果 `CLAUDE.md` 存在，编辑它。
- 否则如果 `AGENTS.md` 存在，编辑它。
- 两者都不存在，则询问用户要创建哪一个；不要替他们选择。

当 `CLAUDE.md` 已存在时，绝不创建 `AGENTS.md`（反之亦然）；始终编辑已存在的那一个。

如果所选文件中已存在 `## Agent skills` 区块，原地更新其内容，而不是追加一个重复区块。不要覆盖用户对周边小节所做的编辑。

区块内容：

```markdown
## Agent skills

### Issue tracker

[one-line summary of where issues are tracked]. See `docs/agents/issue-tracker.md`.

### Triage labels

[one-line summary of the label vocabulary]. See `docs/agents/triage-labels.md`.

### Domain docs

[one-line summary of layout: "single-context" or "multi-context"]. See `docs/agents/domain.md`.
```

仅当 `triage` 已安装且 B 节运行过时，才包含 `### Triage labels` 子区块并写入 `docs/agents/triage-labels.md`。未安装时，两者均省略。

当 B 节针对 GitHub 或 GitLab 运行过时，为追踪器缺少的每个已配置标签创建标签（`gh label create` / `glab label create`）。

随后以本技能文件夹中的种子模板为起点，写入各文档文件：

- [issue-tracker-github.md](./issue-tracker-github.md)：GitHub issue 追踪器
- [issue-tracker-gitlab.md](./issue-tracker-gitlab.md)：GitLab issue 追踪器
- [issue-tracker-local.md](./issue-tracker-local.md)：本地 markdown issue 追踪器
- [triage-labels.md](./triage-labels.md)：标签映射（仅在安装了 `triage` 时生成）
- [domain.md](./domain.md)：领域文档消费方规则 + 布局

对于「其他」issue 追踪器，根据用户的描述从头编写 `docs/agents/issue-tracker.md`。

### 5. 完成

告知用户配置已完成，以及哪些工程技能此后将从这些文件读取内容。提醒他们之后可以直接编辑 `docs/agents/*.md`；只有在希望切换 issue 追踪器或从头重新开始时，才需要重新运行本技能。

# Issue 跟踪器：GitLab

本仓库的 issue 与规格以 GitLab issue 形式存放。所有操作使用 [`glab`](https://gitlab.com/gitlab-org/cli) CLI 完成。

## 约定

- **创建 issue**：`glab issue create --title "..." --description "..."`。多行描述使用 heredoc。传入 `--description -` 可打开编辑器。
- **读取 issue**：`glab issue view <number> --comments`。使用 `-F json` 获取机器可读的输出。
- **列出 issue**：`glab issue list -O json`，并配合合适的 `--label` 过滤。
- **在 issue 下评论**：`glab issue note <number> --message "..."`。GitLab 把评论称为 "notes"。
- **添加 / 移除标签**：`glab issue update <number> --label "..."` / `--unlabel "..."`。多个标签可用逗号分隔，或重复使用该标志。
- **关闭**：`glab issue close <number>`。`glab issue close` 不接受关闭评论，因此先用 `glab issue note <number> --message "..."` 发布说明，再关闭。
- **合并请求**：GitLab 把 PR 称为 "merge requests"。使用 `glab mr create`、`glab mr view`、`glab mr note` 等，形式与 `gh pr ...` 相同，只是用 `mr` 替代 `pr`，用 `note`/`--message` 替代 `comment`/`--body`。

通过 `git remote -v` 推断仓库；在克隆目录内运行时，`glab` 会自动完成这一步。

## 以合并请求作为分诊入口

**MR 作为需求入口：否。** _（如果本仓库把外部合并请求视为功能需求，则设为 `yes`；`/triage` 会读取此标志。）_

设为 `yes` 时，MR 与 issue 走相同的标签和状态流程，使用对应的 `glab mr` 命令：

- **读取 MR**：`glab mr view <number> --comments`，差异用 `glab mr diff <number>` 查看。
- **列出待分诊的外部 MR**：`glab mr list -O json`，然后只保留作者不是项目成员/所有者的 MR（即贡献者提交的 MR，而非维护者进行中的工作）。
- **评论 / 打标签 / 关闭**：`glab mr note`、`glab mr update --label`/`--unlabel`、`glab mr close`。

与 GitHub 不同，GitLab 的 issue 与 MR 分别编号，因此一旦明确维护者指的是哪一类入口，`#42` 就不会产生歧义。

## 当技能说「发布到 issue 跟踪器」时

创建一个 GitLab issue。

## 当技能说「获取相关工单」时

运行 `glab issue view <number> --comments`。

## Wayfinding 操作

供 `/wayfinder` 使用。**map** 是单个 issue，**子** issue 作为工单。

- **Map**：一个打了 `wayfinder:map` 标签的 issue，承载 Notes / Decisions-so-far / Fog 正文。`glab issue create --label wayfinder:map`。（在支持原生 epic 的 GitLab 版本上，也可以用 epic 来承载 map；带标签的 issue 在所有版本上都可行。）
- **子工单**：在描述顶部带有 `Part of #<map>`、链接到 map（`glab issue create --linked-issues <map> --link-type relates_to`）的 issue，并打上 `wayfinder:<type>` 标签（`research`/`prototype`/`grilling`/`task`）。认领后，工单分配给负责推进的开发者。
- **阻塞**：GitLab 的**原生阻塞链接**，即规范的、在 UI 上可见的表示方式。通过 `/blocked_by #<n>` 快捷操作添加，以 note 形式发布（`glab issue note <child> --message "/blocked_by #<blocker>"`）。原生阻塞链接是 Premium/Ultimate 功能；在免费版本（或不可用的环境）上，回退为在描述顶部写一行 `Blocked by: #<n>, #<n>`。当所有阻塞项都被关闭时，工单即解除阻塞。
- **Frontier 查询**：用 `glab api projects/:id/issues/<map>/links` 列出 map 的子项，保留带有 `wayfinder:<type>` 标签的未关闭 issue；剔除任何存在未关闭阻塞项的条目：指向未关闭 issue 的原生 `blocked_by` 链接（`glab api projects/:id/issues/<child-iid>/links`）、`Blocked by` 行中的未关闭 issue，或已有指派人；按 map 顺序取第一个。
- **认领**：`glab issue update <n> --assignee @me`，这是会话中的第一次写入。
- **解决**：`glab issue note <n> --message "<answer>"`，然后 `glab issue close <n>`，再向 map 的 Decisions-so-far 追加一条上下文指针（gist + 链接）。

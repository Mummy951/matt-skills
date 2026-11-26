# Issue 跟踪器：GitHub

本仓库的 issue（议题）与规格以 GitHub issue 形式存放。所有操作使用 `gh` CLI 完成。

## 约定

- **创建 issue**：`gh issue create --title "..." --body "..."`。多行正文使用 heredoc。
- **读取 issue**：`gh issue view <number> --json number,title,body,labels,comments`。
- **列出 issue**：`gh issue list --state open --json number,title,body,labels,comments --jq '[.[] | {number, title, body, labels: [.labels[].name], comments: [.comments[].body]}]'`，配合适当的 `--label` 与 `--state` 过滤条件。
- **将 issue 设为父 issue 的子 issue**：`gh issue create --parent <parent> ...`，或事后执行 `gh issue edit <parent> --add-sub-issue <child>`（`gh` 2.94+）。旧版 `gh`：`gh api --method POST repos/<owner>/<repo>/issues/<parent>/sub_issues -F sub_issue_id=<child-db-id>`（数据库 id，见下文 **Blocking**）。若不支持子 issue，在子 issue 正文顶部标注 `Part of #<parent>`。
- **阻断（Blocking）**：GitHub 的**原生 issue 依赖**，即规范的、在 UI 上可见的呈现方式。用 `gh issue create --blocked-by <n>,<n> ...` 或 `gh issue edit <child> --add-blocked-by <blocker>`（`gh` 2.94+）添加一条依赖边。旧版 `gh`：`gh api --method POST repos/<owner>/<repo>/issues/<child>/dependencies/blocked_by -F issue_id=<blocker-db-id>`，其中 `<blocker-db-id>` 是阻断项的数字**数据库 id**（`gh api repos/<owner>/<repo>/issues/<n> --jq .id`，**不是** `#number` 或 `node_id`）。若依赖功能不可用，则回退为在子 issue 正文顶部写一行 `Blocked by: #<n>, #<n>`。当所有阻断项都已关闭时，工单即解除阻断。
- **统计未关闭的阻断项**：`gh api repos/<owner>/<repo>/issues/<n> --jq .issue_dependencies_summary.blocked_by`（仅统计未关闭阻断项；为 0 表示可以开工）。`gh issue view <n> --json blockedBy` 也会列出已关闭的阻断项，因此需过滤：`--jq '[.blockedBy.nodes[] | select(.state == "OPEN")] | length'`。
- **评论 issue**：`gh issue comment <number> --body "..."`
- **添加 / 移除标签**：`gh issue edit <number> --add-label "..."` / `--remove-label "..."`
- **关闭**：先用 `gh issue comment <number> --body "..."` 留下说明，再执行 `gh issue close <number>`。对于已关闭的 issue，`gh issue close --comment` 会丢弃评论且仍以退出码 0 结束。

仓库依据 `git remote -v` 推断；在克隆仓库内运行时 `gh` 会自动识别。

只把 issue 提交到用户自己的仓库。除非用户拥有 `mattpocock/skills`（这些技能的来源仓库），否则绝不要提交到该仓库。在 fork 中，`gh` 可能默认指向上游父仓库，因此在一次会话的首个 `gh issue create` 之前，先用 `gh repo view --json nameWithOwner` 检查目标仓库；若不是用户自己的仓库，需与用户确认。

## 以 PR 作为分诊入口

**PR 是否作为需求入口：否。** _（若本仓库将外部 PR 视为功能请求，则设为 `yes`；`/triage` 会读取此标志。）_

设为 `yes` 时，PR 与 issue 走相同的标签与状态流程，使用对应的 `gh pr` 命令：

- **读取 PR**：`gh pr view <number>`，再用 `gh pr view <number> --comments`（在非 TTY 环境下，`--comments` 只输出评论，不含正文），并用 `gh pr diff <number>` 查看差异。
- **列出待分诊的外部 PR**：`gh api --paginate 'repos/{owner}/{repo}/pulls?state=open' --jq '.[] | select(.author_association | IN("OWNER","MEMBER","COLLABORATOR") | not) | {number, title, author: .user.login, author_association, labels: [.labels[].name]}'`。
- **评论 / 打标签 / 关闭**：`gh pr comment`、`gh pr edit --add-label`/`--remove-label`、`gh pr close`。

GitHub 在 issue 与 PR 之间共享同一编号空间，因此裸写的 `#42` 可能是二者之一：先用 `gh pr view 42` 解析，失败则回退到 `gh issue view 42`。

## 当技能说「发布到 issue 跟踪器」时

创建一个 GitHub issue。

## 当技能说「拉取相关工单」时

按上文 **读取 issue** 的方式阅读它。

## Wayfinder 操作

供 `/wayfinder` 使用。**地图（map）** 是单个 issue，其 **子 issue** 作为工单。

- **地图**：贴有 `wayfinder:map` 标签的单个 issue，承载 Notes / Decisions-so-far / Fog 正文。`gh issue create --label wayfinder:map`。
- **子工单**：作为 GitHub 子 issue 链接到地图的 issue（见 **将 issue 设为父 issue 的子 issue**）。若未启用子 issue，则将子 issue 加入地图正文中的任务列表，并在子 issue 正文顶部标注 `Part of #<map>`。标签：`wayfinder:<type>`（`research`/`prototype`/`grilling`/`task`）。认领后，工单指派给负责推进的开发者。
- **阻断**：见 **约定** 下的 **Blocking**。
- **前沿查询**：列出地图中未关闭的子项（`gh issue list --state open`，范围限定为地图的子 issue / 任务列表），剔除任何存在未关闭阻断项（见 **统计未关闭阻断项**，或 `Blocked by` 行中仍未关闭的 issue）或已有受理人的条目；按地图顺序取第一个。
- **认领**：`gh issue edit <n> --add-assignee @me`，为本会话的首次写入。
- **解决**：`gh issue comment <n> --body "<answer>"`，然后 `gh issue close <n>`，再向地图的 Decisions-so-far 追加一条上下文指针（gist + 链接）。

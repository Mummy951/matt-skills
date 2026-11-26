# Issue 追踪器：本地 Markdown

本仓库的 issue（议题）与规格以 markdown 文件形式存放于 `.scratch/`。

## 约定

- 每个功能一个目录：`.scratch/<feature-slug>/`
- 规格文件为 `.scratch/<feature-slug>/spec.md`
- 实现类 issue 每个工单一个文件，位于 `.scratch/<feature-slug>/issues/<NN>-<slug>.md`，从 `01` 开始编号，绝不合并成单个工单文件
- 分诊状态记录在每个 issue 文件顶部附近的一行 `Status:` 中（角色字符串见 `triage-labels.md`）
- 评论与对话历史追加到文件底部 `## Comments` 标题之下

## 当技能提示「发布到 issue 追踪器」时

在 `.scratch/<feature-slug>/` 下新建文件（如需要则先创建目录）。

## 当技能提示「获取相关工单」时

读取所引用路径对应的文件。用户通常会直接给出路径或 issue 编号。

## 寻路操作

供 `/wayfinder` 使用。**map**（地图）是一个文件，每个工单对应一个 **child**（子工单）文件。

- **Map**：`.scratch/<effort>/map.md`（Notes / 已形成的决策 / Fog 正文）。
- **子工单**：`.scratch/<effort>/issues/NN-<slug>.md`，从 `01` 开始编号，问题写在正文中。一行 `Type:` 记录工单类型（`research`/`prototype`/`grilling`/`task`）；一行 `Status:` 记录 `claimed`/`resolved`。
- **阻塞**：顶部附近一行 `Blocked by: NN, NN`。当所列文件全部 `resolved` 时，该工单解除阻塞。
- **前沿**：扫描 `.scratch/<effort>/issues/`，找出处于打开、未阻塞、未认领状态的文件；编号最小者优先。
- **认领**：开始任何工作之前，先将 `Status:` 设为 `claimed` 并保存。
- **解决**：在 `## Answer` 标题下追加答案，将 `Status:` 设为 `resolved`，然后在 `map.md` 的 Decisions-so-far 中追加一条上下文指引（gist + 链接）。

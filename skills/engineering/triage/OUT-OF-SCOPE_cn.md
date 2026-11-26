# 超出范围知识库

仓库中的 `.out-of-scope/` 目录用于持久化记录被拒绝的功能请求。它有两个作用：

1. **组织记忆**：记录某个功能为何被拒绝，以便 issue 关闭后，当时的决策理由不致丢失
2. **去重**：当新进来的 issue 与此前某次拒绝相匹配时，技能可以直接呈现之前的决定，而不必重新争论一遍

## 目录结构

```
.out-of-scope/
├── dark-mode.md
├── plugin-system.md
└── graphql-api.md
```

每个**概念**对应一个文件，而不是每个 issue 一个文件。请求同一事项的多个 issue 归并到同一个文件下。

## 文件格式

文件应以轻松、易读的风格撰写，更像一篇简短的设计文档，而不是数据库条目。使用段落、代码示例和范例，让决策理由清晰易懂，方便第一次接触的人理解和使用。

```markdown
# Dark Mode

This project does not support dark mode or user-facing theming.

## Why this is out of scope

The rendering pipeline assumes a single color palette defined in
`ThemeConfig`. Supporting multiple themes would require:

- A theme context provider wrapping the entire component tree
- Per-component theme-aware style resolution
- A persistence layer for user theme preferences

This is a significant architectural change that doesn't align with the
project's focus on content authoring. Theming is a concern for downstream
consumers who embed or redistribute the output.

```ts
// The current ThemeConfig interface is not designed for runtime switching:
interface ThemeConfig {
  colors: ColorPalette; // single palette, resolved at build time
  fonts: FontStack;
}
```

## Prior requests

- #42: "Add dark mode support"
- #87: "Night theme for accessibility"
- #134: "Dark theme option"
```

### 文件命名

为概念取一个简短、描述性的 kebab-case 名称：`dark-mode.md`、`plugin-system.md`、`graphql-api.md`。名称应当足够直观，让人浏览目录时无需打开文件就能知道被拒绝的是什么。

### 撰写理由

理由应当有实质内容：不是「我们不想要这个」，而是说明为什么。好的理由会引用：

- 项目范围或理念（「本项目聚焦于 X；主题化属于下游关切」）
- 技术约束（「支持这一点需要 Y，而这与我们的 Z 架构相冲突」）
- 战略决策（「我们选择用 A 而非 B，因为……」）

理由应当是长期成立的。避免引用临时性的处境（「我们现在太忙了」）；那不是真正的拒绝，只是推迟。

## 何时检查 `.out-of-scope/`

分诊过程中（第 1 步：收集上下文），读取 `.out-of-scope/` 下的所有文件。评估新 issue 时：

- 检查该请求是否与某个已有的超出范围概念相匹配
- 匹配依据是概念相似性，而非关键词：「night theme」会匹配到 `dark-mode.md`
- 如果匹配上，向维护者呈现：「这与 `.out-of-scope/dark-mode.md` 类似。我们此前因[理由]拒绝过它。你现在的看法是否仍然一致？」

维护者可能：

- **确认**：将新 issue 追加到现有文件的「Prior requests」列表中，然后关闭
- **重新考虑**：删除或更新该超出范围文件，该 issue 走正常分诊流程
- **不认同**：两个 issue 相关但并不相同，按正常分诊流程处理

## 何时写入 `.out-of-scope/`

仅当一个**增强需求**（而非 bug）被以 `wontfix` 拒绝时才写入。这一规则对增强 PR 与对 issue 同样适用：被拒绝的 PR 也记录在此，以免同一请求以全新代码的形式再次出现。

当某事项因**已经实现**而被标记为 `wontfix` 关闭时，**不要**写入此处。那是已建成的功能，而非被拒绝的功能；记录它会让去重检查混入虚假的拒绝记录。相反，关闭评论应指向该功能已经存在的位置。

流程如下：

1. 维护者判定某个功能请求超出范围
2. 检查是否已存在匹配的 `.out-of-scope/` 文件
3. 若存在：将新 issue 追加到「Prior requests」列表
4. 若不存在：新建文件，写入概念名称、决定、理由以及第一条历史请求
5. 在 issue 上发表评论，说明决定并提及对应的 `.out-of-scope/` 文件
6. 用 `wontfix` 标签关闭该 issue

## 更新或移除超出范围文件

如果维护者改变了对某个此前被拒绝概念的看法：

- 删除对应的 `.out-of-scope/` 文件
- 技能无需重新打开旧 issue；它们属于历史记录
- 触发重新考虑的新 issue 走正常分诊流程

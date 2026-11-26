# 领域文档

工程类技能在探索代码库时，应如何使用本仓库的领域文档。

## 探索之前，先阅读以下内容

- 仓库根目录下的 **`GLOSSARY.md`**，或
- 若根目录存在 **`GLOSSARY-MAP.md`**：它会为每个上下文指向对应的一个 `GLOSSARY.md`。阅读与当前主题相关的每一份术语表。
- **`docs/adr/`**：阅读与你即将着手的领域相关的 ADR。在多上下文仓库中，还要检查 `src/<context>/docs/adr/` 中按上下文划分的决策记录。

如果上述任何文件不存在，**静默继续即可**。不要指出它们缺失，也不要建议预先创建它们。`/domain-modeling` 技能（通过 `/grill-with-docs` 和 `/improve-codebase-architecture` 进入）会在术语或决策实际确定时再按需创建这些文件。

## 文件结构

单上下文仓库（大多数仓库）：

```
/
├── GLOSSARY.md
├── docs/adr/
│   ├── 0001-event-sourced-orders.md
│   └── 0002-postgres-for-write-model.md
└── src/
```

多上下文仓库（根目录存在 `GLOSSARY-MAP.md`）：

```
/
├── GLOSSARY-MAP.md
├── docs/adr/                          ← system-wide decisions
└── src/
    ├── ordering/
    │   ├── GLOSSARY.md
    │   └── docs/adr/                  ← context-specific decisions
    └── billing/
        ├── GLOSSARY.md
        └── docs/adr/
```

## 使用术语表中的词汇

当你的产出需要命名某个领域概念时（例如 issue 标题、重构提案、假设、测试命名），应使用 `GLOSSARY.md` 中定义的术语。不要改用术语表明确规避的同义词。

如果你需要的概念尚未出现在术语表中，这是一个信号：要么是你在发明项目并不使用的说法（请重新考虑），要么是确实存在缺口（记录下来，留给 `/domain-modeling` 处理）。

## 标记 ADR 冲突

如果你的产出与已有 ADR 相矛盾，应明确指出，而不是默默覆盖：

> _与 ADR-0007（事件溯源订单）相抵触，但值得重新讨论，因为……_

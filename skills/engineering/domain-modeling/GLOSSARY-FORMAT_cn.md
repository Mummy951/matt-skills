# GLOSSARY.md 格式

## 结构

```md
# {Context Name}

{One or two sentence description of what this context is and why it exists.}

## Language

**Order**:
{A one or two sentence description of the term}
_Avoid_: Purchase, transaction

**Invoice**:
A request for payment sent to a customer after delivery.
_Avoid_: Bill, payment request

**Customer**:
A person or organization that places orders.
_Avoid_: Client, buyer, account
```

## 规则

- **要有明确主张。** 当同一概念存在多个词时，选出最合适的一个，把其他词列在 `_Avoid_` 之下。
- **定义保持精炼。** 最多一两句话。定义它是什么，而不是它做什么。
- **只收录本项目上下文中特有的术语。** 通用编程概念（超时、错误类型、工具模式）即使项目大量使用，也不应收录。添加术语前先问：这是本上下文独有的概念，还是通用编程概念？只有前者才属于术语表。
- **当出现自然的聚类时，将术语分组到小标题之下。** 如果所有术语都属于同一个内聚领域，平铺列表即可。

## 单上下文仓库与多上下文仓库

**单上下文（大多数仓库）：** 在仓库根目录放一个 `GLOSSARY.md`。

**多上下文：** 在仓库根目录放一个 `GLOSSARY-MAP.md`，列出各上下文、它们所在的位置以及彼此之间的关系：

```md
# Glossary Map

## Contexts

- [Ordering](./src/ordering/GLOSSARY.md): receives and tracks customer orders
- [Billing](./src/billing/GLOSSARY.md): generates invoices and processes payments
- [Fulfillment](./src/fulfillment/GLOSSARY.md): manages warehouse picking and shipping

## Relationships

- **Ordering → Fulfillment**: Ordering emits `OrderPlaced` events; Fulfillment consumes them to start picking
- **Fulfillment → Billing**: Fulfillment emits `ShipmentDispatched` events; Billing consumes them to generate invoices
- **Ordering ↔ Billing**: Shared types for `CustomerId` and `Money`
```

该技能会推断适用哪种结构：

- 如果存在 `GLOSSARY-MAP.md`，读取它以找到各上下文
- 如果只存在根目录的 `GLOSSARY.md`，则为单上下文
- 如果两者都不存在，则在解析出第一个术语时再惰性创建根目录的 `GLOSSARY.md`

当存在多个上下文时，推断当前主题与哪一个相关。如果不清楚，就询问。

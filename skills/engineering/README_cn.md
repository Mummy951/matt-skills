# 工程

我日常用于代码工作的技能。

## 用户触发

仅在你手动输入时可调用（Claude Code：`disable-model-invocation: true`；Codex：`agents/openai.yaml` 中的 `policy.allow_implicit_invocation: false`）。

- **[ask-matt](./ask-matt/SKILL.md)**：询问哪种技能或流程适合你的处境。本仓库内用户触发技能的路由。
- **[grill-with-docs](./grill-with-docs/SKILL.md)**：深入质询会话，同时构建项目的领域模型，打磨术语，并就地更新 `GLOSSARY.md` 与 ADR。
- **[triage](./triage/SKILL.md)**：在由分诊角色构成的状态机中流转 issue。
- **[improve-codebase-architecture](./improve-codebase-architecture/SKILL.md)**：扫描代码库，寻找深化机会，以可视化 HTML 报告呈现，然后针对你选定的任意一项深入质询。
- **[setup-matt-pocock-skills](./setup-matt-pocock-skills/SKILL.md)**：为本仓库配置工程技能（issue 追踪器、分诊标签、领域文档布局）。每个仓库运行一次。
- **[to-spec](./to-spec/SKILL.md)**：将当前对话整理为一份规格，并发布到 issue 追踪器。
- **[to-tickets](./to-tickets/SKILL.md)**：把任意计划、规格或对话拆成一组曳光弹工单，每张工单声明其阻塞边界，既可以是本地文件中的文字，也可以是真实追踪器上的原生阻塞链接。
- **[implement](./implement/SKILL.md)**：构建规格或一组工单所描述的工作，在预先约定的接缝处驱动 `/tdd`，提交前以 `/code-review` 收尾。
- **[implement-spec](./implement-spec/SKILL.md)**：在一个集成分支上实现完整规格。将工单作为任务图处理，在就绪前沿跨实现者子代理以最大化并发，最后以 `/code-review` 收尾。
- **[wayfinder](./wayfinder/SKILL.md)**：把一大批工作（超出单个智能体会话所能承载的量）规划为 issue 追踪器上一组决策工单构成的共享地图，逐张解决，直到通往目的地的路径清晰为止。
- **[retro](./retro/SKILL.md)**：在会话结束后，对编码智能体的环境（导航、自动检查、编码规范、引导文件、工具链）提出改进建议，按严重程度从高到低排列。

## 模型触发

模型或用户均可调用（触发措辞丰富，便于模型主动选取）。

- **[prototype](./prototype/SKILL.md)**：构建一次性原型来回答某个设计问题：一个可分享的 HTML 文件承载状态/逻辑，或若干可切换的 UI 变体。

- **[diagnosing-bugs](./diagnosing-bugs/SKILL.md)**：针对疑难 bug 与性能回归的有纪律诊断循环：建立一条反馈循环，先让该 bug 复现变红 → 最小化 → 提出假设 → 插桩 → 修复 → 回归测试。
- **[research](./research/SKILL.md)**：基于高可信度的一手资料调研某个问题，并将结论以带引用的 Markdown 文件形式存入仓库，作为后台智能体运行。
- **[tdd](./tdd/SKILL.md)**：采用红-绿-重构循环的测试驱动开发。一次完成一个纵向切片，构建功能或修复 bug。
- **[domain-modeling](./domain-modeling/SKILL.md)**：通过质疑术语、用场景施压检验、并就地更新 `GLOSSARY.md` 与 ADR，主动构建并打磨项目的领域模型。
- **[codebase-design](./codebase-design/SKILL.md)**：设计深层模块时共享的纪律与词汇表：小接口、干净的接缝、可通过接口测试。
- **[code-review](./code-review/SKILL.md)**：针对自某个固定点以来差异的双轴评审：**规范**（是否遵循仓库的编码规范，并对照 Fowler 坏味道基线？）与**规格**（是否忠实实现了原始 issue/规格？），以并行子代理运行。
- **[pr](./pr/SKILL.md)**：PR 正文应有的形态：以最小可视化方式呈现摘要、让变更一目了然，附上改动前后可证明其有效的证据，以及合并风险提示（单向门或双向门，外加影响范围）。
- **[wizard](./wizard/SKILL.md)**：生成一个交互式 bash wizard，引导人类逐步完成只有他们本人才能执行的操作：开通基础设施、配置凭证或 CI 密钥、操作陌生的第三方后台面板，或运行一次性迁移或切换。

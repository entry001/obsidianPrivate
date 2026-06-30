# AI 工程化使用手册

> 面向团队日常落地的操作手册。目标不是解释理念本身，而是回答：**这套 AI 工程化体系到底怎么用、谁在什么阶段做什么、产出什么、什么时候可以进入下一步。**

---

## 一、先看这 4 份总入口文档

### 1. 方案总纲
- [[AI 工程化方案]]
- [[AI 工程化方案（管理层汇报版）]]

### 2. Skill 总索引
- [[.ai/skills/README.md|AI 工程化 Skill 索引]]

### 3. Spec 模板索引
- [[.ai/templates/README.md|Spec 模板索引]]

### 4. Skill 运行化约定
- [[.ai/skills/_runtime/README.md|Skill 运行化使用指南]]
- [[.ai/skills/_runtime/skill-runtime-contract.md|Skill Runtime Contract]]
- [[.ai/skills/_runtime/skill-capability-map.md|Skill 与现有能力映射表]]

如果你是第一次接触这套体系，建议阅读顺序：

**方案总纲 → 本手册 → Skill 索引 → 模板索引 → 示例任务**

---

## 二、这套体系到底在解决什么问题

一句话：

> **用 OpenSpec 把需求写成真理源，用 Superpowers 让 AI 默认按工程纪律执行，再用 Skill 集合把每个阶段固化为可复用的工程动作。**

它主要解决 5 个问题：

1. **需求描述容易失真** → 用 Product / Change / Contract / Execution / Hotfix Spec 固化。
2. **AI 产出快但容易失控** → 用 Skill、规则、CI/CD 门禁和审计来约束。
3. **多仓库协作易踩边界** → 用 Change Spec、Architecture Impact Brief、Contract Audit 先看影响面再编码。
4. **发布与回滚常靠经验** → 用 Execution Spec、Release Gate、Runbook 固化上线动作。
5. **事故经验难沉淀** → 用 Incident to Rule、Diff to Context、Skill Evolver 把经验回流成资产。

---

## 三、目录怎么理解

### 1. 方案层
- [[AI 工程化方案]]：完整方法论
- [[AI 工程化方案（管理层汇报版）]]：汇报版、管理视角

### 2. 模板层
- [[.ai/templates/product-spec-template.md|product-spec-template]]
- [[.ai/templates/change-spec-template.md|change-spec-template]]
- [[.ai/templates/contract-spec-template.md|contract-spec-template]]
- [[.ai/templates/execution-spec-template.md|execution-spec-template]]
- [[.ai/templates/hotfix-spec-template.md|hotfix-spec-template]]

模板层解决的是：**每次从哪里起手、最少写什么。**

### 3. Skill 层
位于 [[.ai/skills/README.md|.ai/skills/README]]。

Skill 层解决的是：**不同阶段该调用什么工程动作。**

### 4. 运行化层
位于 [[.ai/skills/_runtime/README.md|.ai/skills/_runtime/README]]。

运行化层解决的是：**如何把 Skill 变成可编排、可审计、可复用的标准化单元。**

### 5. 实例层
位于：
- [[.ai/specs/product/DISPATCH-2026-RETRY-VIEW-001.md|Product Spec 示例]]
- [[.ai/specs/change/CHG-2026-RETRY-VIEW-001.md|Change Spec 示例]]
- [[.ai/specs/contract/CONTRACT-2026-RETRY-VIEW-001.md|Contract Spec 示例]]
- [[.ai/specs/execution/EXEC-2026-RETRY-VIEW-001.md|Execution Spec 示例]]
- [[.ai/runbooks/DISPATCH-2026-RETRY-VIEW-001-acceptance-matrix.md|Acceptance Matrix 示例]]
- [[.ai/runbooks/DISPATCH-2026-RETRY-VIEW-001-skill-chain-demo.md|Skill 调用链示例]]

实例层解决的是：**新人如何照着一个样板跑通全流程。**

---

## 四、核心原则：任何任务都遵守这 6 条

1. **先写 Spec，再让 AI 编码。**
2. **先看影响面，再动跨仓库。**
3. **先审契约，再开始联调。**
4. **先有验收矩阵，再进入提测和发布。**
5. **热修复可以收缩流程，但不能裸改裸发。**
6. **事故和高频问题必须回流成规则、Skill 或 Runbook。**

如果这 6 条没做到，说明不是体系没用，而是流程还没真正落地。

---

## 五、团队角色怎么使用这套体系

### 1. 架构师 / SE
关注：边界、契约、状态机、发布拓扑、兼容策略。

重点使用：
- [[.ai/skills/spec/spec-intake.md|spec-intake]]
- [[.ai/skills/spec/change-scope-mapper.md|change-scope-mapper]]
- [[.ai/skills/design/contract-auditor.md|contract-auditor]]
- [[.ai/skills/design/state-machine-checker.md|state-machine-checker]]
- [[.ai/skills/design/architecture-impact-brief.md|architecture-impact-brief]]
- [[.ai/skills/design/migration-planner.md|migration-planner]]

职责：
- 决定需求边界是否清楚
- 决定接口/事件/Schema 是否兼容
- 决定跨仓库发布顺序和风险点

### 2. 组长 / 平台 / 工具架构组
关注：Skill 资产、规则库、CI/CD 门禁、复盘回流。

重点使用：
- [[.ai/skills/verify/release-gate-checker.md|release-gate-checker]]
- [[.ai/skills/evolve/incident-to-rule.md|incident-to-rule]]
- [[.ai/skills/evolve/skill-evolver.md|skill-evolver]]
- [[.ai/skills/evolve/changelog-and-runbook.md|changelog-and-runbook]]

职责：
- 把经验变成标准动作
- 守住质量和发布门禁
- 管理 Skill 是否重复、是否失效、是否需要升级

### 3. 开发
关注：把需求变成可执行 Spec，把 Spec 变成测试与实现。

重点使用：
- [[.ai/skills/spec/bdd-expander.md|bdd-expander]]
- [[.ai/skills/spec/acceptance-matrix-builder.md|acceptance-matrix-builder]]
- [[.ai/skills/implementation/tdd-implementer.md|tdd-implementer]]
- [[.ai/skills/implementation/api-contract-coder.md|api-contract-coder]]
- [[.ai/skills/implementation/ui-prototyper.md|ui-prototyper]]
- [[.ai/skills/implementation/event-wiring-builder.md|event-wiring-builder]]
- [[.ai/skills/verify/spec-test-sync.md|spec-test-sync]]

职责：
- 不让 AI 从模糊需求直接跳到代码
- 不让测试落后于需求
- 不让实现溢出本次范围

### 4. QA / 审计 / 安全
关注：验收矩阵、边界、安全、发布前验证。

重点使用：
- [[.ai/skills/spec/acceptance-matrix-builder.md|acceptance-matrix-builder]]
- [[.ai/skills/verify/spec-test-sync.md|spec-test-sync]]
- [[.ai/skills/verify/security-boundary-reviewer.md|security-boundary-reviewer]]
- [[.ai/skills/verify/runtime-verifier.md|runtime-verifier]]

职责：
- 验证“通过测试”是否真的等于“符合 Spec”
- 审边界，而不是只审语法和样式

### 5. 值班 / 发布负责人
关注：发布顺序、观察指标、回滚动作、事后补账。

重点使用：
- [[.ai/templates/execution-spec-template.md|execution-spec-template]]
- [[.ai/skills/verify/release-gate-checker.md|release-gate-checker]]
- [[.ai/skills/verify/runtime-verifier.md|runtime-verifier]]
- [[.ai/skills/evolve/changelog-and-runbook.md|changelog-and-runbook]]

职责：
- 不让“代码能合并”直接等于“可以上线”
- 不让回滚步骤只存在脑子里

---

## 六、三类任务怎么走流程

## 1. 普通功能（Feature）

推荐流程：

`spec-intake → change-scope-mapper → bdd-expander → acceptance-matrix-builder → contract-auditor → architecture-impact-brief → tdd-implementer / api-contract-coder / ui-prototyper → spec-test-sync → security-boundary-reviewer → release-gate-checker → runtime-verifier → changelog-and-runbook → diff-to-context`

适用：
- 新页面
- 新接口
- 现有流程增强
- 轻量跨仓库联动

进入下一阶段的标准：
- **进入实现前**：至少有 Product Spec + Change Spec + BDD / 验收矩阵
- **进入联调前**：Contract 审计通过
- **进入发布前**：Release Gate 和 Runtime Verify 准备完成

---

## 2. 数据库迁移（Migration）

推荐流程：

`spec-intake → change-scope-mapper → migration-planner → contract-auditor → architecture-impact-brief → tdd-implementer / api-contract-coder → spec-test-sync → security-boundary-reviewer → release-gate-checker → runtime-verifier → changelog-and-runbook → incident-to-rule`

额外关注：
- 表主人是谁
- 是否需要 expand → migrate → contract
- 是否有回填
- 是否有 rollback
- 是否有兼容窗口

只要没有 rollback，就不应进入发布。

---

## 3. 紧急修复（Hotfix）

推荐流程：

`hotfix-spec-template → change-scope-mapper → bdd-expander（精简） → acceptance-matrix-builder（精简） → tdd-implementer → security-boundary-reviewer → release-gate-checker → runtime-verifier → changelog-and-runbook → incident-to-rule`

允许收缩的地方：
- 非关键覆盖率
- 非核心回归范围

不允许跳过的地方：
- 范围界定
- 风险说明
- 发布验证
- 回滚动作
- 事后补账

---

## 七、每一类 Spec 到底怎么用

## 1. Product Spec
模板：[[.ai/templates/product-spec-template.md|product-spec-template]]

什么时候写：
- 新需求进入开发前
- 需要明确目标、范围、角色、业务流程时

必须回答的问题：
- 为什么做
- 做到什么程度
- 改哪些，不改哪些
- 谁可以用，谁不能用

没有 In Scope / Out of Scope 的 Product Spec，一般都是危险信号。

## 2. Change Spec
模板：[[.ai/templates/change-spec-template.md|change-spec-template]]

什么时候写：
- 建分支之后、进入实现之前

必须回答的问题：
- 本次会动哪些仓库、模块、协议、表、事件
- 发布顺序是什么
- 回滚怎么做

没有 Blast Radius 的 Change Spec，不允许跨仓库动手。

## 3. Contract Spec
模板：[[.ai/templates/contract-spec-template.md|contract-spec-template]]

什么时候写：
- 有 API / WebSocket / IPC / Schema / Redis Key 变更时

必须回答的问题：
- 请求和响应长什么样
- 新增字段是否兼容
- 错误码是否清楚
- 谁消费，谁生产

没有 Contract Spec，不建议开始联调。

## 4. Execution Spec
模板：[[.ai/templates/execution-spec-template.md|execution-spec-template]]

什么时候写：
- 进入发布前
- 发布顺序复杂时
- 有灰度、回滚、观察窗口时

必须回答的问题：
- 怎么发
- 先发谁
- 看什么指标
- 什么时候回滚

## 5. Hotfix Spec
模板：[[.ai/templates/hotfix-spec-template.md|hotfix-spec-template]]

什么时候写：
- 生产问题紧急修复时

必须回答的问题：
- 最小修复面是什么
- 放宽了什么、为什么放宽
- 事后补什么

---

## 八、Skill 怎么选，不要乱用

一个简单原则：

- **Spec 类 Skill**：解决“要做什么”
- **Design 类 Skill**：解决“边界、契约、状态是否正确”
- **Implementation 类 Skill**：解决“如何稳定生成代码与接线”
- **Verify 类 Skill**：解决“是否真的符合 Spec、是否可以上线”
- **Evolve 类 Skill**：解决“这次经验怎么沉淀为长期资产”

如果你只做了 Implementation，没有做 Verify 和 Evolve，那只是“AI 写代码”，不是“AI 工程化”。

---

## 九、开发者的一次标准日常操作

下面是一条建议的“开发者日常工作链”：

### 上午：明确需求与边界
1. 打开 [[.ai/templates/product-spec-template.md|product-spec-template]]
2. 写 Product Spec
3. 调用 [[.ai/skills/spec/change-scope-mapper.md|change-scope-mapper]] 形成 Change Spec
4. 调用 [[.ai/skills/spec/bdd-expander.md|bdd-expander]] 展开场景
5. 调用 [[.ai/skills/spec/acceptance-matrix-builder.md|acceptance-matrix-builder]] 生成验收矩阵

### 中午前：审设计与契约
6. 如果有接口/事件变更，写 Contract Spec
7. 调用 [[.ai/skills/design/contract-auditor.md|contract-auditor]]
8. 如果涉及跨仓库，调用 [[.ai/skills/design/architecture-impact-brief.md|architecture-impact-brief]]
9. 如果涉及状态机或重试逻辑，调用 [[.ai/skills/design/state-machine-checker.md|state-machine-checker]]

### 下午：实现与自检
10. 后端逻辑使用 [[.ai/skills/implementation/tdd-implementer.md|tdd-implementer]]
11. 接口/类型同步使用 [[.ai/skills/implementation/api-contract-coder.md|api-contract-coder]]
12. 前端或 Electron 用 [[.ai/skills/implementation/ui-prototyper.md|ui-prototyper]]
13. 事件接线用 [[.ai/skills/implementation/event-wiring-builder.md|event-wiring-builder]]

### 提交前：做验证
14. 调用 [[.ai/skills/verify/spec-test-sync.md|spec-test-sync]]
15. 调用 [[.ai/skills/verify/security-boundary-reviewer.md|security-boundary-reviewer]]
16. 结合现有能力做 `code-review` / `security-review`

### 发布前：做交付闭环
17. 写 Execution Spec
18. 调用 [[.ai/skills/verify/release-gate-checker.md|release-gate-checker]]
19. 调用 [[.ai/skills/verify/runtime-verifier.md|runtime-verifier]]
20. 调用 [[.ai/skills/evolve/changelog-and-runbook.md|changelog-and-runbook]]

### 合并后：做知识沉淀
21. 调用 [[.ai/skills/evolve/diff-to-context.md|diff-to-context]]
22. 如果这次踩坑明显，再调用 [[.ai/skills/evolve/incident-to-rule.md|incident-to-rule]]
23. 如果这类动作频繁重复，调用 [[.ai/skills/evolve/skill-evolver.md|skill-evolver]]

---

## 十、把现有内置能力放到正确位置

本体系不是替换已有能力，而是把它们纳入工程化流程。

建议这样用：

- **code-review**：PR 前的正确性与清理检查
- **security-review**：安全边界、权限、敏感信息、IPC/DB 边界复核
- **verify**：验证某个改动是否真的工作
- **run**：启动应用做真实观察
- **drawio / architecture-diagram**：画跨仓库影响图、状态机图、发布拓扑图
- **simplify**：实现稳定后再做精简，不替代正确性检查

可参考：[[.ai/skills/_runtime/skill-capability-map.md|Skill 与现有能力映射表]]

---

## 十一、推荐用这个示例做团队培训

示例任务：**新增任务重试次数展示**

建议培训顺序：
1. 先看 [[.ai/specs/product/DISPATCH-2026-RETRY-VIEW-001.md|Product Spec 示例]]
2. 再看 [[.ai/specs/change/CHG-2026-RETRY-VIEW-001.md|Change Spec 示例]]
3. 再看 [[.ai/specs/contract/CONTRACT-2026-RETRY-VIEW-001.md|Contract Spec 示例]]
4. 再看 [[.ai/specs/execution/EXEC-2026-RETRY-VIEW-001.md|Execution Spec 示例]]
5. 再看 [[.ai/runbooks/DISPATCH-2026-RETRY-VIEW-001-acceptance-matrix.md|Acceptance Matrix 示例]]
6. 最后看 [[.ai/runbooks/DISPATCH-2026-RETRY-VIEW-001-skill-chain-demo.md|Skill 调用链示例]]

为什么推荐它：
- 业务简单，容易理解
- 但又覆盖 API、UI、WebSocket、跨仓库、兼容、发布等要素
- 非常适合做“第一堂演示课”

---

## 十二、最常见的 10 个错误

1. **没有 Spec 就让 AI 直接编码**
2. **有 Product Spec，没有 Change Spec**
3. **跨仓库改动不写 Blast Radius**
4. **接口改了，但不写 Contract Spec**
5. **只补 Happy Path，不补异常、权限、幂等、副作用**
6. **把“测试通过”误当成“满足业务意图”**
7. **热修复跳过回滚说明**
8. **发布说明只写“更新了几个文件”**
9. **合并后不沉淀上下文与规则**
10. **把 Skill 当 Prompt 清单，而不是工程动作单元**

如果团队常犯这些错，建议在周会或复盘时直接拿本节逐项对照。

---

## 十三、最小落地标准（团队可作为检查表）

### 任何功能需求至少应满足
- [ ] 有 Product Spec
- [ ] 有 Change Spec
- [ ] 有 BDD 场景
- [ ] 有验收矩阵
- [ ] 有实现前契约审计（如涉及接口/事件）
- [ ] 有提测前一致性检查
- [ ] 有发布前验证
- [ ] 有发布说明 / 回滚说明
- [ ] 有合并后上下文回流动作

### 任何热修复至少应满足
- [ ] 有 Hotfix Spec
- [ ] 有最小修复面说明
- [ ] 有精简验收矩阵
- [ ] 有安全边界复核
- [ ] 有运行验证
- [ ] 有回滚步骤
- [ ] 有事后补账计划

---

## 十四、FAQ

### Q1：小改动也要写 Spec 吗？
要，但可以写最小 Spec。

原则不是“写长文档”，而是“让边界和影响面可见”。

### Q2：只是展示字段，也要做 Contract 审计吗？
如果涉及 API / WebSocket / IPC payload 变更，就建议做。因为展示字段最容易被误判为“无风险”，但前后端类型、兼容性、权限都可能出问题。

### Q3：热修复能不能跳过测试？
不能完全跳过。可以收缩范围，但至少要保留核心路径、失败路径、恢复路径验证。

### Q4：什么时候该用 incident-to-rule？
只要出现事故、反复踩坑、或者某类问题连续多次发生，就应该用。否则复盘只会停留在口头层面。

### Q5：什么时候该用 skill-evolver？
当你发现一类动作在团队中被重复做了 3 次以上，而且输入/输出已经稳定，就应该考虑升级成 Skill。

---

## 十五、最后的执行建议

如果你要在团队内推广这套体系，建议按下面顺序推进：

1. **先统一模板**：先用 [[.ai/templates/README.md|Spec 模板索引]]
2. **再跑一个样板**：先用 [[.ai/runbooks/DISPATCH-2026-RETRY-VIEW-001-skill-chain-demo.md|任务重试次数展示示例]]
3. **再要求关键任务全链路执行**：至少 Spec → Audit → Implement → Verify → Runbook
4. **最后把事故回流制度化**：让 incident-to-rule 和 diff-to-context 真正成为流程的一部分

真正的 AI 工程化，不是“让 AI 写得更快”，而是：

> **让需求定义、设计审计、实现生成、发布验证、事故沉淀形成同一条连续的工程闭环。**

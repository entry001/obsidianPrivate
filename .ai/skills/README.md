# AI 工程化 Skill 索引

> 该目录用于沉淀 OpenSpec × Superpowers 体系下的工程化技能资产。每个 skill 都应是稳定、可重复、可审计的工程动作，而不是一次性 Prompt。

---

## 一、Skill 分层

### 1. Spec 阶段
- [[.ai/skills/spec/spec-intake.md|spec-intake]]：把需求整理为 Product Spec
- [[.ai/skills/spec/change-scope-mapper.md|change-scope-mapper]]：识别受影响仓库、模块、协议与发布边界
- [[.ai/skills/spec/bdd-expander.md|bdd-expander]]：把需求展开为 Given-When-Then 场景矩阵

### 2. Design 阶段
- [[.ai/skills/design/contract-auditor.md|contract-auditor]]：审计 API / WebSocket / IPC / Schema 契约
- [[.ai/skills/design/state-machine-checker.md|state-machine-checker]]：检查状态流转、补偿与幂等风险
- [[.ai/skills/design/migration-planner.md|migration-planner]]：生成迁移、兼容、回滚计划

### 3. Implementation 阶段
- [[.ai/skills/implementation/tdd-implementer.md|tdd-implementer]]：测试先行的核心业务实现
- [[.ai/skills/implementation/ui-prototyper.md|ui-prototyper]]：Vue / Electron 原型与交互骨架生成
- [[.ai/skills/implementation/event-wiring-builder.md|event-wiring-builder]]：WebSocket / IPC 事件接线实现

### 4. Verify 阶段
- [[.ai/skills/verify/spec-test-sync.md|spec-test-sync]]：校验 Spec、测试、实现是否同步
- [[.ai/skills/verify/release-gate-checker.md|release-gate-checker]]：发布前门禁决策
- [[.ai/skills/verify/runtime-verifier.md|runtime-verifier]]：真实运行验证与证据化报告

### 5. Evolve 阶段
- [[.ai/skills/evolve/incident-to-rule.md|incident-to-rule]]：把事故沉淀为规则与资产
- [[.ai/skills/evolve/diff-to-context.md|diff-to-context]]：把稳定实现回填到上下文记忆
- [[.ai/skills/evolve/skill-evolver.md|skill-evolver]]：从高频任务中演化新 Skill

---

## 二、推荐执行链

### 普通功能
`spec-intake → change-scope-mapper → bdd-expander → contract-auditor → tdd-implementer / ui-prototyper → spec-test-sync → release-gate-checker → runtime-verifier → diff-to-context`

### 数据库迁移
`spec-intake → change-scope-mapper → migration-planner → contract-auditor → tdd-implementer → release-gate-checker → runtime-verifier → incident-to-rule`

### 紧急修复
`spec-intake → change-scope-mapper → bdd-expander（精简） → tdd-implementer → release-gate-checker → runtime-verifier → incident-to-rule`

---

## 三、维护原则

- Skill 必须有清晰输入/输出。
- Skill 必须能融入现有分支、PR、CI/CD、发布流程。
- Skill 优先复用已有能力（如 code-review、security-review、verify、run）。
- Skill 数量增长要可控，避免重复与碎片化。
- 每次事故和高频重复动作都应评估是否演化成新 Skill。

# 工程化 Skill 与现有能力映射表

## 一、复用优先原则

新增 Skill 时，优先判断是否可直接复用已有能力，而不是重复造轮子。

---

## 二、映射表

| 新 Skill | 主要职责 | 优先复用 |
|---------|----------|----------|
| spec-intake | 需求整理为 Product Spec | 无 |
| change-scope-mapper | 影响面识别 | 无 |
| bdd-expander | 生成 BDD 场景矩阵 | 无 |
| contract-auditor | 契约设计审计 | code-review, security-review |
| state-machine-checker | 状态机审计 | 无 |
| migration-planner | 迁移与回滚规划 | 无 |
| tdd-implementer | 测试先行实现 | simplify（后置优化） |
| ui-prototyper | UI/Electron 原型生成 | drawio（如需流程图） |
| event-wiring-builder | 事件链路接线 | verify |
| spec-test-sync | 对齐 Spec / Test / Code | code-review |
| release-gate-checker | 发布前门禁审查 | security-review, verify |
| runtime-verifier | 真实运行验证 | run, verify |
| incident-to-rule | 事故转规则 | 无 |
| diff-to-context | 合并后回填上下文 | 无 |
| skill-evolver | 演化新 Skill | 无 |

---

## 三、组合建议

### PR 阶段推荐组合
- `contract-auditor`
- `spec-test-sync`
- `code-review`
- `security-review`

### 发布前推荐组合
- `release-gate-checker`
- `run`
- `verify`
- `runtime-verifier`

### 复盘后推荐组合
- `incident-to-rule`
- `diff-to-context`
- `skill-evolver`

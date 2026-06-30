# Skill Runtime Contract

> 用于把 Markdown Skill 进一步运行化，让后续 Claude / OMC / Cline / 其他 Agent 编排工具能够按统一协议调用。

---

## 一、目标

每个 Skill 不只是说明文档，而应具备统一的：
- 元数据（是谁、何时触发、适用范围）
- 输入契约（最少需要什么）
- 输出契约（产出文件或报告）
- 执行步骤（可编排）
- 完成标准（可审计）
- 与现有能力的映射（如 code-review / verify / run / security-review）

---

## 二、推荐 Frontmatter 规范

```yaml
name: spec-intake
stage: spec
engine: OpenSpec
version: 0.1
owner: 业务设计组
change_types: [feature, refactor, migration, hotfix]
repos_supported: [主调度服务, 主调度Web, 看门狗Web, 看门狗Electron, 调度子系统]
inputs:
  - PRD / 工单 / 缺陷描述
outputs:
  - .ai/specs/product/<ticket>.md
prerequisites: []
next:
  - change-scope-mapper
  - bdd-expander
integrates_with: []
acceptance:
  - 已生成 Product Spec
  - 已标注影响边界与未知项
```

---

## 三、标准章节

每个 Skill 文件建议至少包含以下章节：
1. 使命
2. 适用场景
3. 必需输入
4. 执行协议
5. 输出契约
6. 调用输入模板
7. 守则
8. 完成标准
9. 集成映射

---

## 四、与现有能力的统一映射

| Skill | 可集成能力 |
|------|------------|
| contract-auditor | code-review, security-review |
| spec-test-sync | code-review |
| release-gate-checker | security-review, verify |
| runtime-verifier | run, verify |
| architecture-impact-brief | architecture-diagram, drawio |
| security-boundary-reviewer | security-review |

---

## 五、运行化建议

### 1. 固定输入变量
建议所有 Skill 统一支持以下键：
- `ticket`
- `spec_id`
- `change_type`
- `repos`
- `modules`
- `risk_level`
- `compatibility`
- `release_guard`

### 2. 固定输出落点
建议输出尽量进入固定目录：
- Spec 类：`.ai/specs/`
- 报告类：`.ai/runbooks/` 或任务目录
- Skill 索引类：`.ai/skills/`

### 3. 固定状态词
- `draft`
- `ready-for-audit`
- `ready-for-implementation`
- `ready-for-release`
- `completed`

### 4. 固定判定词
- `pass`
- `warn`
- `block`
- `conditional-pass`

---

## 六、最小编排链

### 普通功能
`spec-intake -> change-scope-mapper -> bdd-expander -> contract-auditor -> tdd-implementer/ui-prototyper -> spec-test-sync -> release-gate-checker -> runtime-verifier`

### 数据库迁移
`spec-intake -> change-scope-mapper -> migration-planner -> contract-auditor -> tdd-implementer -> release-gate-checker -> runtime-verifier`

### 热修复
`spec-intake -> change-scope-mapper -> bdd-expander(minimal) -> tdd-implementer -> release-gate-checker -> runtime-verifier -> incident-to-rule`

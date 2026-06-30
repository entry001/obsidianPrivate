# Skill 对应 Command 对照表

> 目的：把工程化 Skill 和实际使用时的 command 统一起来，避免团队里出现“同一个动作有三四种叫法”。

---

## 一、先说明状态

在当前体系里，command 分成 3 种状态：

- **✅ 已可直接使用**：当前 Claude 环境已经有对应 slash command，可直接使用 `/xxx`
- **🟡 约定命令**：我们已经在 vault 中定义了对应 Skill，推荐统一使用 `/skill-name` 作为命令名；但它目前主要还是工程规范与协作约定，后续要接入真实 skill/command 系统后才能变成可直接执行的 slash command
- **🔁 组合命令**：没有单一命令，建议由多个已有命令配合完成

一句话理解：

> **现在先把命名统一，后续再把这些命名真正注册成可执行 command。**

---

## 二、命名规则

### 1. 默认规则
- command 默认与 skill 文件名一致
- 统一使用 **kebab-case**
- 推荐展示形式：`/skill-name`

例如：
- `spec-intake` → `/spec-intake`
- `contract-auditor` → `/contract-auditor`
- `runtime-verifier` → `/runtime-verifier`

### 2. 过渡期使用方式

如果该 command 还没有真正注册成 slash command，团队当前可以这样使用：

- 在沟通中统一说：`请按 /spec-intake 起草这次需求的 Product Spec`
- 在任务卡 / PR / 评论中统一写：`下一步：/contract-auditor`
- 在 Claude 对话里，按对应 Skill 文档执行，例如：
  - “请按 [[.ai/skills/spec/spec-intake.md]] 的约定，为这个需求产出 Product Spec”
  - “请按 [[.ai/skills/design/contract-auditor.md]] 审计这次接口变更”

这样做的价值是：
- 现在就能统一语言
- 以后真正接入 command 系统时，不需要重新改名和再培训一遍

---

## 三、当前可直接使用的内置 command

| Command | 状态 | 用途 | 适用阶段 |
|---------|------|------|----------|
| `/code-review` | ✅ | Review 当前改动的正确性与优化点 | Verify / PR |
| `/security-review` | ✅ | 做安全边界与风险审查 | Verify / PR / Release |
| `/verify` | ✅ | 验证改动是否真的工作 | Verify |
| `/run` | ✅ | 启动项目并观察真实行为 | Verify |
| `/drawio` | ✅ | 生成图、流程图、架构图 | Design |
| `/architecture-diagram` | ✅ | 输出架构图与影响图 | Design |
| `/simplify` | ✅ | 对已完成实现做清理和简化 | Implementation 后置优化 |

---

## 四、工程化 Skill 的推荐 command 对照表

## 1. Spec 阶段

| Skill | 推荐 command | 状态 | 当前替代使用方式 | 典型输入 |
|------|---------------|------|------------------|----------|
| spec-intake | `/spec-intake` | 🟡 | 按 [[.ai/skills/spec/spec-intake.md]] 执行 | 需求 / 工单 / 会议纪要 |
| change-scope-mapper | `/change-scope-mapper` | 🟡 | 按 [[.ai/skills/spec/change-scope-mapper.md]] 执行 | Product Spec |
| bdd-expander | `/bdd-expander` | 🟡 | 按 [[.ai/skills/spec/bdd-expander.md]] 执行 | Product Spec / Change Spec |
| acceptance-matrix-builder | `/acceptance-matrix-builder` | 🟡 | 按 [[.ai/skills/spec/acceptance-matrix-builder.md]] 执行 | BDD / 约束 / 发布要求 |

### 推荐话术示例
- “请按 `/spec-intake` 为这个需求生成 Product Spec。”
- “请按 `/change-scope-mapper` 列出这次改动的 Blast Radius。”
- “请按 `/bdd-expander` 展开正向、逆向、异常、权限、幂等场景。”
- “请按 `/acceptance-matrix-builder` 输出一张可用于提测和发布的验收矩阵。”

---

## 2. Design 阶段

| Skill | 推荐 command | 状态 | 当前替代使用方式 | 典型输入 |
|------|---------------|------|------------------|----------|
| contract-auditor | `/contract-auditor` | 🟡 | 按 [[.ai/skills/design/contract-auditor.md]] 执行 | Contract Spec |
| state-machine-checker | `/state-machine-checker` | 🟡 | 按 [[.ai/skills/design/state-machine-checker.md]] 执行 | 状态机 / BDD |
| migration-planner | `/migration-planner` | 🟡 | 按 [[.ai/skills/design/migration-planner.md]] 执行 | Schema 变更 |
| architecture-impact-brief | `/architecture-impact-brief` | 🟡 | 按 [[.ai/skills/design/architecture-impact-brief.md]] 执行 | Change Spec / Contract Spec |

### 建议联动的内置 command
- `contract-auditor` 可联动 `/code-review`、`/security-review`
- `architecture-impact-brief` 可联动 `/architecture-diagram`、`/drawio`

### 推荐话术示例
- “请按 `/contract-auditor` 审这次 API 和 WebSocket payload。”
- “请按 `/state-machine-checker` 检查这个任务流转有没有死胡同。”
- “请按 `/migration-planner` 输出迁移顺序、兼容窗口和 rollback。”
- “请按 `/architecture-impact-brief` 写一份跨仓库影响和发布顺序简报。”

---

## 3. Implementation 阶段

| Skill | 推荐 command | 状态 | 当前替代使用方式 | 典型输入 |
|------|---------------|------|------------------|----------|
| tdd-implementer | `/tdd-implementer` | 🟡 | 按 [[.ai/skills/implementation/tdd-implementer.md]] 执行 | Spec + BDD |
| api-contract-coder | `/api-contract-coder` | 🟡 | 按 [[.ai/skills/implementation/api-contract-coder.md]] 执行 | Contract Spec |
| ui-prototyper | `/ui-prototyper` | 🟡 | 按 [[.ai/skills/implementation/ui-prototyper.md]] 执行 | UI Spec / Change Spec |
| event-wiring-builder | `/event-wiring-builder` | 🟡 | 按 [[.ai/skills/implementation/event-wiring-builder.md]] 执行 | Event Contract |

### 建议联动的内置 command
- 实现稳定后可再用 `/simplify`

### 推荐话术示例
- “请按 `/tdd-implementer` 先写失败测试，再补最小实现。”
- “请按 `/api-contract-coder` 同步后端 response 和前端 types。”
- “请按 `/ui-prototyper` 生成这个页面的 UI 骨架和 store/api 结构。”
- “请按 `/event-wiring-builder` 把 WebSocket / IPC 的接线和断言补齐。”

---

## 4. Verify 阶段

| Skill | 推荐 command | 状态 | 当前替代使用方式 | 典型输入 |
|------|---------------|------|------------------|----------|
| spec-test-sync | `/spec-test-sync` | 🟡 | 按 [[.ai/skills/verify/spec-test-sync.md]] 执行 | Spec + 测试 + Diff |
| security-boundary-reviewer | `/security-boundary-reviewer` | 🟡 | 按 [[.ai/skills/verify/security-boundary-reviewer.md]] 执行 | Change Spec + Diff |
| release-gate-checker | `/release-gate-checker` | 🟡 | 按 [[.ai/skills/verify/release-gate-checker.md]] 执行 | CI 结果 + 审计报告 |
| runtime-verifier | `/runtime-verifier` | 🟡 | 按 [[.ai/skills/verify/runtime-verifier.md]] 执行 | 运行环境 + 场景清单 |

### 建议联动的内置 command
- `spec-test-sync` 可联动 `/code-review`
- `security-boundary-reviewer` 可联动 `/security-review`
- `runtime-verifier` 可联动 `/run`、`/verify`
- `release-gate-checker` 可联动 `/verify`、`/security-review`

### 推荐话术示例
- “请按 `/spec-test-sync` 检查 Spec、测试和实现是否一致。”
- “请按 `/security-boundary-reviewer` 复核这次权限、DB 和 IPC 边界。”
- “请按 `/release-gate-checker` 给出 pass / conditional-pass / block 结论。”
- “请按 `/runtime-verifier` 在测试环境跑一遍关键路径并给证据。”

---

## 5. Evolve 阶段

| Skill | 推荐 command | 状态 | 当前替代使用方式 | 典型输入 |
|------|---------------|------|------------------|----------|
| incident-to-rule | `/incident-to-rule` | 🟡 | 按 [[.ai/skills/evolve/incident-to-rule.md]] 执行 | 事故复盘 |
| diff-to-context | `/diff-to-context` | 🟡 | 按 [[.ai/skills/evolve/diff-to-context.md]] 执行 | merged diff |
| skill-evolver | `/skill-evolver` | 🟡 | 按 [[.ai/skills/evolve/skill-evolver.md]] 执行 | 高频任务 / Prompt 样本 |
| changelog-and-runbook | `/changelog-and-runbook` | 🟡 | 按 [[.ai/skills/evolve/changelog-and-runbook.md]] 执行 | Change Spec + Execution Spec |

### 推荐话术示例
- “请按 `/incident-to-rule` 把这次事故沉淀成规则建议。”
- “请按 `/diff-to-context` 提炼这次合并后应回填到 L2/L3 的上下文。”
- “请按 `/skill-evolver` 看看这类动作是否值得变成新 Skill。”
- “请按 `/changelog-and-runbook` 生成发布说明和回滚手册。”

---

## 五、推荐的命令链

## 1. 普通功能

```text
/spec-intake
/change-scope-mapper
/bdd-expander
/acceptance-matrix-builder
/contract-auditor
/architecture-impact-brief
/tdd-implementer 或 /api-contract-coder 或 /ui-prototyper
/spec-test-sync
/security-boundary-reviewer
/release-gate-checker
/runtime-verifier
/changelog-and-runbook
/diff-to-context
```

## 2. 数据库迁移

```text
/spec-intake
/change-scope-mapper
/migration-planner
/contract-auditor
/architecture-impact-brief
/tdd-implementer
/spec-test-sync
/security-boundary-reviewer
/release-gate-checker
/runtime-verifier
/changelog-and-runbook
/incident-to-rule
```

## 3. 紧急修复

```text
/spec-intake 或 hotfix-spec-template
/change-scope-mapper
/bdd-expander（精简）
/acceptance-matrix-builder（精简）
/tdd-implementer
/security-boundary-reviewer
/release-gate-checker
/runtime-verifier
/changelog-and-runbook
/incident-to-rule
```

---

## 六、建议团队统一采用的写法

### 在任务卡里
- `下一步：/spec-intake`
- `进入设计审计：/contract-auditor`
- `提测前执行：/spec-test-sync`
- `发布前执行：/release-gate-checker + /runtime-verifier`

### 在 PR 描述里
- `Specs: /spec-intake /change-scope-mapper /contract-auditor`
- `Verification: /spec-test-sync /security-boundary-reviewer /runtime-verifier`

### 在复盘里
- `Follow-up: /incident-to-rule /diff-to-context`

---

## 七、后续真正接入 command 系统时的建议

如果后面要把这些“约定命令”变成真正可执行的 slash command，建议保持以下原则：

1. **命令名不要改**，继续沿用本文的 `/skill-name`
2. **命令输入结构标准化**，围绕：
   - `ticket`
   - `spec_id`
   - `change_id`
   - `change_type`
   - `repos`
3. **命令输出路径固定**，尽量落到：
   - `.ai/specs/`
   - `.ai/runbooks/`
   - `.ai/skills/`
4. **命令返回结论统一**，例如：
   - `pass / warn / block / conditional-pass`
   - `draft / ready-for-audit / ready-for-release / completed`

---

## 八、最终建议

现阶段最重要的不是立刻把所有 command 做成自动化，而是：

> **先把 skill 名、command 名、团队语言、PR 用词和任务流程统一起来。**

当团队已经习惯把这些动作说成：
- `/spec-intake`
- `/contract-auditor`
- `/runtime-verifier`
- `/incident-to-rule`

那么后续无论接入 Claude、OMC、Cline 还是其他工具，迁移成本都会非常低。

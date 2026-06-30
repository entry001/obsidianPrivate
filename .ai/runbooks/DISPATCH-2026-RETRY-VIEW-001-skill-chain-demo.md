# 示例：新增任务重试次数展示的 Skill 调用链

## 任务概述
- 目标：让主调度服务、主调度 Web、看门狗 Web 展示任务当前 `retry_count`
- 类型：普通 feature
- 特点：跨仓库、轻契约变更、无 schema 迁移

---

## 推荐调用链

### 1. spec-intake
输入：需求“新增任务重试次数展示”
输出：[[.ai/specs/product/DISPATCH-2026-RETRY-VIEW-001.md]]

### 2. change-scope-mapper
输入：Product Spec
输出：[[.ai/specs/change/CHG-2026-RETRY-VIEW-001.md]]

### 3. bdd-expander
建议补充以下场景：
- 查询详情返回 `retry_count`
- 查询列表返回 `retry_count`
- 前端缺失字段时兼容展示
- 实时状态事件中 `retry_count` 一致
- 权限不变

### 4. acceptance-matrix-builder
输出：[[.ai/runbooks/DISPATCH-2026-RETRY-VIEW-001-acceptance-matrix.md]]

### 5. contract-auditor
输入：[[.ai/specs/contract/CONTRACT-2026-RETRY-VIEW-001.md]]
检查：
- 字段新增是否 backward-compatible
- WebSocket payload 是否一致
- 权限边界是否保持不变

### 6. architecture-impact-brief
适合输出：
- 主调度服务 → 主调度 Web / 看门狗 Web 的影响链
- 是否需要 backend-first 发布

### 7. api-contract-coder
实现：
- 后端 API response/schema 增加 `retry_count`
- 前端 `api/` 与 `types/` 同步该字段

### 8. ui-prototyper
实现：
- 主调度 Web 的任务列表 / 详情展示
- 看门狗 Web 的监控 / 告警视图展示

### 9. spec-test-sync
检查：
- Spec、Contract、实现、测试是否一致

### 10. security-boundary-reviewer
检查：
- 是否放宽了查询权限
- 是否泄露了不该暴露的内部字段

### 11. release-gate-checker
基于 [[.ai/specs/execution/EXEC-2026-RETRY-VIEW-001.md]] 做发布判断。

### 12. runtime-verifier
发布前验证：
- 详情页
- 列表页
- 看门狗视图
- 实时状态推送一致性

### 13. changelog-and-runbook
沉淀发布说明与回滚说明。

### 14. diff-to-context
如果该字段展示模式稳定，可回填到前端 / 后端上下文记忆。

---

## 本示例的工程价值

这个样例虽然业务小，但非常适合拿来做团队演示，因为它同时覆盖了：
- Product Spec
- Change Spec
- Contract Spec
- Execution Spec
- Acceptance Matrix
- 跨仓库 API + UI + WebSocket 的最小联动

它是一个很好的“轻量级样板任务”。

---
name: acceptance-matrix-builder
stage: spec
engine: OpenSpec + Superpowers
version: 0.1
owner: 业务设计组 / 测试负责人
inputs:
  - Product Spec
  - Change Spec
  - BDD 场景
outputs:
  - .ai/runbooks/<ticket>-acceptance-matrix.md
next:
  - contract-auditor
  - tdd-implementer
  - spec-test-sync
---

# acceptance-matrix-builder

## 使命

把需求、BDD 与发布门禁收敛成一张 **验收矩阵**，明确“什么算完成、谁来验、用什么证据验、在哪个阶段卡住”。

## 适用场景

- 新功能进入实现前
- 跨仓库改动需要统一验收口径
- 热修复需要缩减但不能失控的验收范围
- 发布前需要明确 Must-pass 与 Nice-to-have 项目

## 必需输入

- Product Spec
- Change Spec
- BDD 场景矩阵
- 已知的性能 / 安全 / 兼容性约束

## 执行协议

1. 先抽出验收维度：功能、契约、权限、安全、性能、可观测性、回滚、回归。
2. 将每个 BDD 场景映射为至少一种验收方式：unit / api / e2e / manual / runtime verify。
3. 标注优先级：Must Pass / Should Pass / Optional。
4. 标注责任人：开发、测试、审计、值班、架构。
5. 标注证据类型：测试报告、接口返回、截图、日志、监控指标。
6. 对 hotfix 场景允许缩减范围，但必须显式列出被豁免项与补账计划。
7. 输出一张可直接用于 PR、发布评审、验收会议的矩阵表。

## 输出契约

```markdown
# Acceptance Matrix

| Dimension | Scenario | Method | Priority | Owner | Evidence | Gate |
|-----------|----------|--------|----------|-------|----------|------|
| 功能 | 创建任务成功 | api test | Must Pass | 开发 | 测试报告 | PR |
```

## 调用输入模板

```text
spec_id：
change_id：
关键场景：
性能 / 安全 / 兼容约束：
是否 hotfix：
```

## 守则

- 没有证据类型的验收项视为不可执行。
- 必须区分 Must Pass 与可延后项。
- 任何被豁免的项都必须绑定补账动作。
- 不允许把“人工观察一下”作为唯一验证方式。

## 完成标准

- [ ] 已按维度整理验收项
- [ ] 已标记优先级、责任人、证据与门禁阶段
- [ ] 已为 hotfix/特例写明豁免与补账

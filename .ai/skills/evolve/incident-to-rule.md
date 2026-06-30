---
name: incident-to-rule
stage: evolve
engine: Superpowers
version: 0.1
owner: 架构组 / 平台组 / 审计组
inputs:
  - 事故复盘记录
  - 线上问题单
outputs:
  - rules update proposal
  - skill / test / runbook 更新建议
next:
  - skill-evolver
  - diff-to-context
---

# incident-to-rule

## 使命

把事故、线上故障、误发布、热修复教训，沉淀成 **规则、测试、runbook、skill**，避免同类问题重复发生。

## 适用场景

- 事故复盘后
- 热修复结束后
- 高频缺陷复发时
- 审计发现某类问题长期重复时

## 必需输入

- 事故描述
- 根因分析
- 影响范围
- 修复动作
- 现有规则/测试/流程是否失效

## 执行协议

1. 判断问题属于哪一层缺口：Spec、规则、测试、实现、发布、监控、回滚。
2. 提炼“如果事前存在什么规则，本问题可被阻断”。
3. 把经验映射为具体资产：
   - 新规则
   - 新测试模板
   - 新 Skill
   - 新 runbook
   - 新监控项
4. 明确资产落点：L4 / L3 / L2 / .ai/skills / runbooks。
5. 输出最小更新建议，防止只做事后总结不做系统加固。

## 输出契约

```markdown
# Incident To Rule Proposal

## Incident Summary
- 

## Root Cause Layer
- spec / test / code / release / observability

## Proposed Assets
| Type | Location | Proposal |
|------|----------|----------|
| rule | .ai/rules/security-redlines.md | 禁止 xxx |
```

## 调用输入模板

```text
事故名称：
根因：
影响范围：
已采取修复：
希望防止再次发生的方式：
```

## 守则

- 不做空泛“加强沟通”式结论。
- 必须转化成可执行资产。
- 没有落点的复盘视为未完成。

## 完成标准

- [ ] 已识别根因所属层
- [ ] 已提出可执行资产建议
- [ ] 已指明落点与维护责任人

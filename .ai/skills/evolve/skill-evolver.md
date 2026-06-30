---
name: skill-evolver
stage: evolve
engine: Superpowers
version: 0.1
owner: 平台组 / 工具架构组
inputs:
  - 高频任务记录
  - Prompt 样本
  - 审计与复盘结论
outputs:
  - new skill proposal
  - skill refactor proposal
next:
  - 无
---

# skill-evolver

## 使命

从高频任务、常见 Prompt、重复审计动作中抽象出新的 Skill，持续升级团队的工程化能力库。

## 适用场景

- 月度工程回顾
- 某类任务重复出现 3 次以上
- 某个 Prompt 被多人反复复制粘贴
- 审计、发布、验证存在高频机械动作

## 必需输入

- 高频任务样本
- 典型 Prompt / 操作步骤
- 失败案例与成功案例
- 现有 Skill 清单

## 执行协议

1. 找出重复动作与稳定输入输出结构。
2. 判断该动作是否足够独立，适合固化成 Skill。
3. 提炼 Skill 的：职责、触发时机、输入、输出、守则、完成标准。
4. 若与现有 Skill 重叠，优先做合并或重构，而非无限新增。
5. 输出推荐列表：新增、合并、废弃、升级。

## 输出契约

```markdown
# Skill Evolution Proposal

## Candidates
| Action | Recommendation | Reason |
|--------|----------------|--------|
| 发布前验证 | new skill: runtime-verifier | 高频且步骤稳定 |

## Refactors
- 

## Deprecations
- 
```

## 调用输入模板

```text
高频任务：
常见 Prompt：
重复痛点：
现有 Skill：
```

## 守则

- Skill 必须是稳定工程动作，不是一次性 Prompt。
- 先合并重叠，再新增。
- Skill 数量增长必须伴随可维护性评估。

## 完成标准

- [ ] 已识别高频稳定动作
- [ ] 已输出新增/合并/废弃建议
- [ ] 已形成标准化 Skill 草案

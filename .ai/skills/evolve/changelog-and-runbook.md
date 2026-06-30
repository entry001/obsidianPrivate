---
name: changelog-and-runbook
stage: evolve
engine: Superpowers
version: 0.1
owner: 发布负责人 / 开发负责人
inputs:
  - Change Spec
  - Execution Spec
  - 最终 Diff / 验证结果
outputs:
  - CHANGELOG 条目
  - 发布说明
  - 回滚 Runbook
next:
  - diff-to-context
---

# changelog-and-runbook

## 使命

把“代码改了什么”转成 **能发布、能交接、能回滚** 的文字资产：CHANGELOG、发布说明、操作步骤、回滚手册。

## 适用场景

- 功能发布前
- 数据库迁移或跨仓库发布前
- 热修复上线前
- 需要给值班、运维、QA、产品同步时

## 必需输入

- Change Spec
- Execution Spec
- 最终 Diff
- 发布前验证结果 / 风险说明

## 执行协议

1. 提炼本次变更的“为什么”与“用户会感知到什么”。
2. 生成面向不同对象的输出：
   - CHANGELOG：面对研发与发布记录
   - 发布说明：面对产品 / QA / 值班
   - 回滚 Runbook：面对值班 / 运维
3. 明确前置条件、发布步骤、验证步骤、回滚触发条件。
4. 如为 hotfix，额外写明临时豁免项与事后补账计划。
5. 输出应短、准、可执行，不写成空泛总结。

## 输出契约

```markdown
# Release Note / Runbook

## Summary
- 

## Release Steps
1.
2.
3.

## Validation
- 

## Rollback
- Trigger:
- Steps:
```

## 调用输入模板

```text
change_id：
发布批次：
目标环境：
是否 hotfix：
关键验证点：
回滚触发条件：
```

## 守则

- CHANGELOG 写“为什么”，不要只抄文件名。
- 回滚步骤必须具体到顺序与验证动作。
- 发布说明要区分用户可见变更与仅内部变更。
- 热修复必须写补账动作。

## 完成标准

- [ ] 已生成 CHANGELOG 条目
- [ ] 已生成发布说明
- [ ] 已生成回滚 Runbook
- [ ] 已标记风险、验证点与补账动作

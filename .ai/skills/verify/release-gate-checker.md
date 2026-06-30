---
name: release-gate-checker
stage: verify
engine: Superpowers
version: 0.1
owner: 平台组 / 审计组 / 值班负责人
inputs:
  - Change Spec
  - CI 结果
  - 审计报告
outputs:
  - release-gate-report.md
next:
  - runtime-verifier
  - changelog-and-runbook
---

# release-gate-checker

## 使命

根据变更类型自动执行 **发布前门禁判断**，输出放行、阻断或灰度建议，防止“代码合了但不该发”或“热修复没有补安全校验”。

## 适用场景

- PR 合并前
- 预发/生产发布前
- 灰度放量前
- 热修复快速上线前

## 必需输入

- Change Spec
- CI 结果（Lint/Test/Coverage/Security/Compatibility）
- Contract / State / Migration 审计结果
- 运行验证结果（如已存在）

## 执行协议

1. 根据 `change_type` 与 `blast_radius` 选择门禁项。
2. 普通 feature 默认要求三道关卡全绿。
3. migration 必须额外检查 rollback、验证清单、兼容窗口。
4. hotfix 允许缩减非关键覆盖率，但不能跳过安全与契约审计。
5. 跨仓库改动必须确认发布顺序与灰度策略。
6. 输出结果：`pass / conditional-pass / block`。

## 输出契约

```markdown
# Release Gate Report

## Decision
- Result: pass / conditional-pass / block
- Reason:

## Checks
| Check | Status | Notes |
|-------|--------|-------|
| lint | pass | |
| security | pass | |
| compatibility | block | 未声明兼容窗口 |

## Actions Before Release
- 
```

## 调用输入模板

```text
change_type：
blast_radius：
CI 结果：
是否 hotfix：
是否跨仓库：
是否有 rollback：
```

## 守则

- 热修复不是免检，只是门禁收缩。
- 没有 rollback 的 migration 不放行。
- breaking change 没有兼容窗口直接 block。
- 发布顺序不明确的跨仓库改动不放行。

## 完成标准

- [ ] 已输出放行结论
- [ ] 已列出阻断项/条件项
- [ ] 已给出发布前补充动作

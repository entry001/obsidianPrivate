---
name: spec-test-sync
stage: verify
engine: Superpowers
version: 0.1
owner: 审计组 / 开发负责人
inputs:
  - Product Spec
  - BDD Matrix
  - 测试代码
  - 实现 Diff
outputs:
  - spec-test-sync-report.md
next:
  - release-gate-checker
  - code-review
---

# spec-test-sync

## 使命

检查 **Spec、测试、实现** 三者是否同步，避免出现“Spec 改了、测试没跟；测试过了、实现偏了；代码写了、场景没覆盖”。

## 适用场景

- PR 前总检
- 核心模块改动
- 回归测试前
- 热修复补账检查

## 必需输入

- 最新 Product Spec / Change Spec
- BDD 场景矩阵
- 本次测试清单
- 本次实现 Diff

## 执行协议

1. 逐条比对 BDD 场景与测试用例映射。
2. 检查实现中是否存在 Spec 未声明的额外行为。
3. 检查 Spec 声明的副作用是否都在测试中被断言。
4. 检查热修复是否遗漏后续补测项。
5. 输出缺口分类：Spec 缺口 / Test 缺口 / Code 缺口。

## 输出契约

```markdown
# Spec Test Sync Report

## Coverage Summary
- BDD total:
- covered:
- missing:

## Gaps
| Type | Item | Problem | Action |
|------|------|---------|--------|
| Test Gap | 异常路径 | PostgreSQL 超时未断言回滚 | 补集成测试 |
```

## 调用输入模板

```text
spec_id：
变更模块：
BDD 场景数：
测试文件：
实现范围：
```

## 守则

- 测试通过 ≠ 满足 Spec。
- 没有覆盖副作用断言的场景视为未闭环。
- 发现超范围实现要明确标红。

## 完成标准

- [ ] 已对齐 Spec / Test / Code
- [ ] 已输出缺口分类
- [ ] 已给出补充动作

---
name: tdd-implementer
stage: implementation
engine: OpenSpec + Superpowers
version: 0.1
owner: 开发 / 审计员
inputs:
  - Product Spec
  - BDD Matrix
  - Contract Audit Report
outputs:
  - 功能代码
  - 单元测试 / 接口测试
next:
  - spec-test-sync
  - code-review
---

# tdd-implementer

## 使命

按 **先测试、后实现、再自检** 的顺序完成核心业务逻辑编码，让实现始终受 Spec 与测试双重约束。

## 适用场景

- FastAPI 后端接口与服务层逻辑
- 状态机、事务、重试、并发控制
- 调度子系统任务执行与回写日志
- 任何需要强正确性的业务模块

## 必需输入

- Product Spec / Change Spec
- BDD 场景矩阵
- Contract Audit 结论
- 模块上下文（L2）与仓库约束（L3）

## 执行协议

1. 先根据 BDD 写失败中的测试：单测、接口测、必要时集成测。
2. 再以最小实现让测试通过，禁止超范围顺手改造。
3. 把事务边界放在 services/，API 层只做参数与协议适配。
4. 若涉及 WebSocket、Redis、副作用，必须在测试中有断言。
5. 写完后执行自检：类型、lint、测试、复杂度、未捕获异常。
6. 输出变更说明：改了什么、没改什么、剩余风险是什么。

## 输出契约

```markdown
# TDD Implementation Summary

## Implemented
- 

## Tests Added
- unit:
- api:
- integration:

## Risks / Follow-ups
- 
```

## 调用输入模板

```text
目标仓库：
目标模块：
BDD 场景：
接口契约：
事务 / 状态机要求：
禁止触碰范围：
```

## 守则

- 未写测试，不进入实现。
- API 层禁止写业务逻辑。
- 禁止跨仓库顺手修改无关模块。
- 副作用若无断言，视为未实现完成。

## 完成标准

- [ ] 测试先行
- [ ] 实现满足 BDD
- [ ] 事务与副作用符合约束
- [ ] 已输出实现摘要与剩余风险

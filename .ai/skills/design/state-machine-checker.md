---
name: state-machine-checker
stage: design
engine: OpenSpec + Superpowers
version: 0.1
owner: 架构组 / 业务设计组
inputs:
  - Product Spec
  - BDD 场景
  - 模块状态定义
outputs:
  - state-machine-audit.md
next:
  - tdd-implementer
  - spec-test-sync
---

# state-machine-checker

## 使命

检查业务状态机是否存在 **死胡同、非法跳转、缺失补偿、重试失控、终态不清** 等问题，特别适用于调度任务与告警流转场景。

## 适用场景

- 任务状态流转
- 重试 / 超时 / 失败补偿逻辑
- 审批流、工单流、告警流、守护进程状态流
- 任何涉及多状态、异步、副作用的业务模块

## 必需输入

- 当前业务状态集合
- 状态迁移规则
- 异常 / 超时 / 重试策略
- 相关 BDD 场景

## 执行协议

1. **列状态**：起始态、中间态、终态、错误态、补偿态。
2. **列迁移**：谁触发、何时触发、是否可重入、是否需要事务。
3. **查死角**：
   - 是否有状态无法到达
   - 是否有状态进入后无法退出
   - 是否存在未声明的跳转
4. **查异常链**：超时、失败、重试、取消、回滚是否有明确路径。
5. **查副作用**：状态变更后是否需要写日志、推送 WebSocket、更新 Redis、触发告警。
6. **查幂等**：重复消息、重复点击、重复消费是否会导致状态污染。
7. **输出问题矩阵**，并给出修复建议。

## 输出契约

```markdown
# State Machine Audit

## States
- PENDING
- RUNNING
- SUCCESS
- FAILED
- CANCELED

## Allowed Transitions
| From | To | Trigger | Side Effect |
|------|----|---------|-------------|
| PENDING | RUNNING | worker start | push task_status_changed |

## Findings
| Level | Type | Problem | Suggestion |
|-------|------|---------|------------|
| block | illegal transition | RUNNING 可直接跳到 PENDING | 禁止回退或定义补偿态 |
```

## 调用输入模板

```text
业务对象：
状态集合：
起始态 / 终态：
异常与重试策略：
副作用：
是否要求幂等：
```

## 守则

- 状态机必须有起始态与终态。
- 超时、失败、取消至少要有一条可审计路径。
- 副作用不能隐式存在，必须显式声明。
- 幂等性要求不清时，一律按需要幂等处理并标记待确认。

## 完成标准

- [ ] 已列出状态与迁移矩阵
- [ ] 已识别死胡同、非法跳转、补偿缺口
- [ ] 已标明副作用与幂等风险
- [ ] 已形成审计结论

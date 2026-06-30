---
name: bdd-expander
stage: spec
engine: OpenSpec
version: 0.1
owner: 业务设计组 / 测试负责人
inputs:
  - Product Spec
  - Change Spec
outputs:
  - BDD 场景清单
  - 测试映射矩阵
next:
  - tdd-implementer
  - spec-test-sync
---

# bdd-expander

## 使命

把 Product Spec 扩展成 **Given-When-Then 场景集合**，覆盖正向、逆向、异常、并发、权限、幂等和副作用路径。

## 适用场景

- 任何需要测试驱动的功能开发
- 涉及状态流转、重试、事务、权限控制的改动
- 需要把需求转成测试资产时

## 必需输入

- Product Spec
- Change Spec
- 关键状态机或业务规则
- 接口契约 / 事件契约（如已存在）

## 执行协议

1. 先抽出 **业务主路径**。
2. 对每个主路径强制补齐以下场景：
   - 正向路径
   - 逆向路径
   - 异常路径
   - 权限路径
   - 并发 / 重试 / 超时路径
   - 幂等路径
   - 副作用验证路径（日志、事件、缓存、通知）
3. 对调度系统类场景，重点检查：
   - 任务状态是否从 `PENDING → RUNNING → SUCCESS/FAILED`
   - 重试次数是否受 `retry_count` 约束
   - WebSocket 事件是否按约推送
   - 数据库事务失败是否回滚
4. 每个 Then 必须可验证，不能写成抽象结论。
5. 输出“场景 → 测试类型”映射，给实现与验证阶段复用。

## 输出契约

```markdown
# BDD Matrix

## 场景 1：创建任务成功
- Given:
- When:
- Then:
- And Side Effects:
- Test Type: unit / api / e2e

## 场景 2：非法 taskType
- Given:
- When:
- Then:
- Error Code:
- Test Type:

## 场景 3：数据库超时回滚
- Given:
- When:
- Then:
- Recovery / Alert:
- Test Type:
```

## 调用输入模板

```text
spec_id：
关键实体：
关键状态：
接口或事件：
权限与角色：
必须覆盖的异常：
```

## 守则

- 不能只写 Happy Path。
- 不能把“系统正确处理”当成 Then，必须写清结果。
- 并发、超时、权限、回滚是调度系统默认必审项。
- 每个场景都应能映射到至少一种测试类型。

## 完成标准

- [ ] 已覆盖正向 / 逆向 / 异常路径
- [ ] 已覆盖权限、并发、幂等、副作用
- [ ] 已给出可验证的 Then
- [ ] 已形成测试映射矩阵

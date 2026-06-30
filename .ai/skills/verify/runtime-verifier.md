---
name: runtime-verifier
stage: verify
engine: Superpowers
version: 0.1
owner: 审计组 / 开发 / 值班
inputs:
  - 应用运行环境
  - 场景清单
  - 发布前报告
outputs:
  - runtime-verify-report.md
next:
  - changelog-and-runbook
---

# runtime-verifier

## 使命

联动真实运行环境做 **冒烟、关键路径、事件链路、异常恢复** 验证，证明这次改动不只是“代码看起来对”，而是“运行起来也对”。

## 适用场景

- 核心业务功能发布前
- WebSocket / IPC / Redis / Migration 相关改动
- 紧急修复上线前
- 需要给管理层或审计组提供实证时

## 必需输入

- 目标环境（本地 / 测试 / 预发）
- 场景清单
- 发布前门禁报告
- 若涉及 UI：操作步骤与预期结果

## 执行协议

1. 启动目标应用或依赖环境。
2. 按场景执行核心路径、异常路径、回滚/恢复路径。
3. 若涉及 WebSocket / IPC，验证消息是否真实触发与消费。
4. 若涉及 migration，验证迁移前后读写与兼容行为。
5. 输出观察结果：通过、失败、证据、截图/日志位置。

## 输出契约

```markdown
# Runtime Verify Report

## Environment
- 

## Scenarios
| Scenario | Result | Evidence |
|----------|--------|----------|
| 创建任务成功 | pass | 日志 / 截图 |

## Risks
- 
```

## 调用输入模板

```text
目标环境：
验证场景：
是否涉及 UI：
是否涉及事件链路：
是否涉及迁移：
```

## 守则

- 不能用“理论上应该通过”替代实际验证。
- 验证报告必须带证据。
- 关键路径至少覆盖一条成功、一条失败、一条恢复。

## 完成标准

- [ ] 已执行真实运行验证
- [ ] 已输出证据化报告
- [ ] 已识别剩余运行风险

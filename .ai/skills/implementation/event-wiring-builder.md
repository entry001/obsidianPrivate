---
name: event-wiring-builder
stage: implementation
engine: OpenSpec + Superpowers
version: 0.1
owner: 后端 / 前端 / Electron 开发
inputs:
  - Event Contract Spec
  - Change Spec
outputs:
  - WebSocket / IPC 事件接线代码与测试
next:
  - contract-auditor
  - runtime-verifier
---

# event-wiring-builder

## 使命

实现并校验 **WebSocket / IPC** 事件链路，让实时更新、桌面代理通信与副作用通知保持一致，不出现“后端发了、前端没订”“事件名不一致”“payload 漏字段”等问题。

## 适用场景

- 看门狗 Web 实时告警
- 主调度服务状态推送
- Electron 主进程与渲染进程通信
- 任何事件驱动型功能

## 必需输入

- Event Contract Spec
- Change Spec
- 订阅端/发送端模块位置

## 执行协议

1. 固定事件名、payload 结构、时间戳与版本字段。
2. 明确发送条件：谁发、何时发、失败如何处理、是否幂等。
3. 明确消费条件：谁订阅、何时解绑、异常如何兜底。
4. Electron IPC 必须明确主进程 handler 与渲染进程调用点。
5. 为事件链路写测试：发送断言、订阅断言、错误路径断言。
6. 输出事件链路摘要，便于 `runtime-verifier` 复核。

## 输出契约

```markdown
# Event Wiring Summary

## Events
| Event | Producer | Consumer | Payload |
|-------|----------|----------|---------|
| task_status_changed | 主调度服务 | 看门狗Web | { event, data, timestamp } |

## Tests
- 
```

## 调用输入模板

```text
事件类型：WebSocket / IPC
事件名：
生产者：
消费者：
payload：
异常处理：
```

## 守则

- 事件契约未定义前不得开工。
- 不允许隐式 payload。
- 发送与消费都必须有可验证路径。
- Electron 事件必须遵守主进程 / 渲染进程职责分离。

## 完成标准

- [ ] 已实现事件发送与消费链路
- [ ] 已对 payload 与触发时机做断言
- [ ] 已补充错误路径与解绑/清理策略

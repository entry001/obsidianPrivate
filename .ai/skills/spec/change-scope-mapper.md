---
name: change-scope-mapper
stage: spec
engine: OpenSpec
version: 0.1
owner: 架构组 / 开发负责人
inputs:
  - Product Spec
  - 仓库结构与模块上下文
outputs:
  - .ai/specs/change/<ticket>.md
next:
  - contract-auditor
  - migration-planner
---

# change-scope-mapper

## 使命

把需求描述转成 **变更影响面地图**，明确这次改动会触达哪些仓库、模块、表、接口、事件和发布顺序。

## 适用场景

- 建分支后第一次设计梳理
- 跨仓库联动改动
- 数据库或协议调整
- 需要评估 Blast Radius 时

## 必需输入

- `spec-intake` 产出的 Product Spec
- 现有仓库清单与职责边界
- 数据库归属规则
- 跨仓库通信协议定义

## 执行协议

1. **列出目标仓库**：主调度服务 / 主调度Web / 看门狗Web / 看门狗Electron / 调度子系统。
2. **拆分触点类型**：API、DB、WebSocket、IPC、Redis、任务队列、配置项、日志与监控。
3. **识别表主人**：任何数据库写入都要判断“表归属仓库”，非主人仓库只能通过 API/协议。
4. **标注变更等级**：局部改动 / 跨仓库改动 / 破坏性改动 / 热修复。
5. **输出顺序依赖**：谁先改、谁后发、谁兼容老版本、谁需要灰度。
6. **标记阻断项**：没有 owner、没有 rollback、没有 compatibility 声明的变更不能进入实现。

## 输出契约

输出文件建议路径：`.ai/specs/change/<ticket>.md`

```markdown
---
change_id: CHG-2026-001
spec_id: DISPATCH-2026-001
risk_level: medium
cross_repo: true
status: draft
---

# Change Spec

## 1. Repos Involved
| Repo | Module | Change Type | Owner |
|------|--------|-------------|-------|
| 主调度服务 | src/api/tasks | modify | 后端组 |

## 2. Blast Radius
- api:
- db:
- websocket:
- ipc:
- redis:

## 3. Dependency Order
1.
2.
3.

## 4. Compatibility
- backward-compatible / breaking
- 兼容窗口：

## 5. Rollback Strategy
- 
```

## 调用输入模板

```text
spec_id：
涉及仓库：
涉及模块：
涉及数据库表 / 事件 / IPC：
是否跨仓库：
是否需要灰度 / feature flag：
是否需要回滚：
```

## 守则

- 不允许把“可能影响”写成空泛描述，必须落到具体仓库/模块/接口。
- 非表主人仓库不得声明直写数据库。
- 任何 breaking change 都必须写兼容窗口与发布顺序。
- 如果影响 WebSocket / IPC / Redis，必须写清 payload、键名或通道边界。

## 完成标准

- [ ] 已列出所有受影响仓库与模块
- [ ] 已标注 blast_radius
- [ ] 已确认数据库表主人与写入路径
- [ ] 已给出发布顺序与兼容策略
- [ ] 已补充 rollback 思路

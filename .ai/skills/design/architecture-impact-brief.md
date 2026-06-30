---
name: architecture-impact-brief
stage: design
engine: OpenSpec + Superpowers
version: 0.1
owner: 架构组 / 发布负责人
inputs:
  - Change Spec
  - Contract Spec
  - 仓库边界定义
outputs:
  - .ai/runbooks/<ticket>-architecture-impact-brief.md
next:
  - contract-auditor
  - release-gate-checker
---

# architecture-impact-brief

## 使命

把一次改动对 **仓库边界、数据流、协议流、发布顺序、风险点** 的影响压缩成一份架构简报，帮助团队在实现前就看清“会波及什么、应该先发谁、哪里最容易炸”。

## 适用场景

- 跨仓库功能开发
- 涉及 API / WebSocket / IPC / 数据库的联动改动
- 上线顺序敏感的变更
- 需要给管理层、审计组或值班同学快速说明影响面时

## 必需输入

- Change Spec
- Contract Spec
- 相关仓库职责与表归属规则
- 发布 / 灰度约束

## 执行协议

1. 列出本次变更影响的仓库、模块、数据库表、事件与依赖方向。
2. 区分“直接改动对象”和“兼容关注对象”。
3. 标出数据流与控制流：请求从哪里来、状态在哪里变、事件向哪里推。
4. 给出推荐发布顺序：谁先发、谁后发、谁需要兼容窗口。
5. 标出高风险点：共享表、严格解析的消费者、版本不一致窗口、回滚耦合点。
6. 如需要，可联动 `architecture-diagram` / `drawio` 生成图形化拓扑。
7. 输出一份可被 PR、发布评审直接引用的架构影响简报。

## 输出契约

```markdown
# Architecture Impact Brief

## Scope
- Repos:
- Protocols:
- Data Stores:

## Change Flow
1.
2.
3.

## Release Order
1.
2.
3.

## Top Risks
- 
```

## 调用输入模板

```text
change_id：
涉及仓库：
涉及协议：
是否跨仓库：
发布顺序敏感点：
是否需要图：
```

## 守则

- 不能只写“影响前后端”，必须落到具体仓库 / 模块 / 协议。
- 必须区分直接修改与兼容关注，不得混写。
- 如果存在顺序依赖，必须显式给出 backend-first / frontend-first / migration-first 等结论。
- 如果回滚策略依赖发布顺序，必须一并说明。

## 完成标准

- [ ] 已列明影响仓库、协议、数据流
- [ ] 已给出发布顺序与兼容窗口建议
- [ ] 已标出高风险点与回滚耦合点
- [ ] 需要图时已指明可交给 `architecture-diagram` / `drawio`

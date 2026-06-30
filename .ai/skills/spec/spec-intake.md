---
name: spec-intake
stage: spec
engine: OpenSpec
version: 0.1
owner: 业务设计组
inputs:
  - PRD / 工单 / 缺陷单 / 会议纪要
outputs:
  - .ai/specs/product/<ticket>.md
next:
  - change-scope-mapper
  - bdd-expander
---

# spec-intake

## 使命

把模糊需求整理成可执行的 **Product Spec**。先定义真理源，再进入设计、编码和测试。

## 适用场景

- 新功能立项
- 既有功能增强
- 缺陷修复前的需求澄清
- 跨仓库改动的需求入口
- 热修复需要最小化修复面时

## 必需输入

- 业务目标 / 用户价值
- 当前问题描述或工单编号
- 涉及的角色、权限、数据对象
- 已知约束：性能、并发、安全、兼容性、发布时间窗
- 如已存在：相关 API、表结构、页面、运行链路

## 执行协议

1. **先分类任务类型**：feature / refactor / migration / hotfix。
2. **识别影响边界**：仓库、模块、数据库表、接口、WebSocket 事件、IPC 通道、缓存键。
3. **补齐最小元数据**：`spec_id`、`change_type`、`repos`、`blast_radius`、`compatibility`、`release_guard`、`rollback`。
4. **拆出范围**：明确 In Scope / Out of Scope，防止 AI 越界修改。
5. **抽取业务行为**：把自然语言需求转成“用户动作 → 系统响应 → 副作用”。
6. **先标注未知项**：缺信息时输出澄清问题，不允许自行脑补关键业务规则。
7. **形成 Product Spec 初稿**，再交给 `change-scope-mapper` 与 `bdd-expander` 深化。

## 输出契约

输出文件建议路径：`.ai/specs/product/<ticket>.md`

```markdown
---
spec_id: DISPATCH-2026-001
change_type: feature
repos: [主调度服务, 看门狗Web]
blast_radius: [api, websocket, db]
compatibility: backward-compatible
release_guard: feature_flag
rollback: required
owner: 业务设计组
status: draft
---

# Product Spec

## 1. Business Goal
- 目标：
- 用户价值：
- 成功指标：

## 2. In Scope
- 

## 3. Out of Scope
- 

## 4. Actors / Roles
- 

## 5. Core Flow
- 

## 6. Risks / Unknowns
- 
```

## 调用输入模板

```text
任务类型：
工单 / 需求链接：
目标仓库：
业务目标：
涉及角色：
已知接口 / 表 / 页面：
性能 / 安全 / 并发约束：
计划上线窗口：
```

## 守则

- 不直接进入编码。
- 不允许只写 Happy Path。
- 不允许把“待确认”伪装成确定结论。
- 不允许省略兼容性与回滚说明。
- 跨仓库变更必须显式标出影响边界。

## 完成标准

- [ ] 已明确 change_type
- [ ] 已列出 repos 与 blast_radius
- [ ] 已写明 In Scope / Out of Scope
- [ ] 已识别主要业务角色与权限边界
- [ ] 已标记未知项与待确认问题
- [ ] 已生成 Product Spec 初稿

---
name: ui-prototyper
stage: implementation
engine: OpenSpec + Superpowers
version: 0.1
owner: 前端 / Electron 开发
inputs:
  - UI Spec
  - Change Spec
outputs:
  - views / components / stores / api / types 骨架
next:
  - runtime-verifier
  - spec-test-sync
---

# ui-prototyper

## 使命

根据 UI Spec 快速生成 **Vue / Electron** 的页面与交互原型，同时遵守组件复用、状态分层、接口隔离和 IPC 边界。

## 适用场景

- 主调度 Web 页面开发
- 看门狗 Web 仪表盘与告警页面
- 看门狗 Electron 渲染层 UI
- 需要先出交互骨架再补测试的场景

## 必需输入

- UI Spec（页面目标、交互流程、状态变化）
- Change Spec
- 仓库前端约束与现有组件清单

## 执行协议

1. 明确页面职责、共享组件、状态来源、接口依赖。
2. 先复用现有组件与样式体系，避免新造基础组件。
3. 组件内不直接发请求，统一通过 `api/` 层。
4. Pinia 只管理业务状态，不承载 UI 细节杂逻辑。
5. Electron 渲染层只负责 UI，系统级操作必须走 IPC。
6. 生成原型时同步产出类型定义与基础测试骨架。

## 输出契约

```markdown
# UI Prototype Summary

## Pages / Components
- 

## Stores / API
- 

## Interaction Notes
- 

## Follow-up Tests
- 
```

## 调用输入模板

```text
目标仓库：主调度Web / 看门狗Web / 看门狗Electron
页面名称：
关键交互：
数据来源：
是否实时更新：
是否涉及 IPC：
```

## 守则

- 不直接使用 axios，统一走封装 API 层。
- 不在 Store 中写 UI 逻辑。
- 监控数据优先 WebSocket，不得轮询替代。
- Electron 渲染层不得直接访问文件系统或 child_process。

## 完成标准

- [ ] 已生成页面与组件骨架
- [ ] 已接好 store / api / types 基础结构
- [ ] 已遵守 IPC / WebSocket / 组件复用约束
- [ ] 已列出后续验证项

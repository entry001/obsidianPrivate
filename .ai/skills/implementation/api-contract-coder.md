---
name: api-contract-coder
stage: implementation
engine: OpenSpec + Superpowers
version: 0.1
owner: 后端 / 前端开发
inputs:
  - Contract Spec
  - Change Spec
  - 仓库约束（L3）
outputs:
  - API route / schema / types / client code
next:
  - spec-test-sync
  - code-review
---

# api-contract-coder

## 使命

根据 Contract Spec 生成并对齐 **后端接口实现、Schema、前端类型与 API 调用层**，避免“后端手写一套、前端猜一套、文档再写第三套”。

## 适用场景

- FastAPI 接口新增或变更
- Vue / TypeScript 客户端类型同步
- 请求 / 响应字段新增、废弃或重命名
- 需要让 OpenAPI / JSON Schema 成为真理源时

## 必需输入

- Contract Spec
- Change Spec
- 仓库约束（FastAPI、Vue、TypeScript、snake_case 等）
- 已有 API / schema / types 文件位置

## 执行协议

1. 先从 Contract Spec 提取请求体、响应体、错误码、鉴权要求。
2. 在后端生成或调整：route、schema、service 入参/出参适配。
3. 在前端生成或调整：`api/` 调用层、`types/` 定义、必要的 store 使用点。
4. 保持字段命名与契约一致，默认使用 `snake_case`，不要在各层各自发明名字。
5. 对新增字段采取向后兼容策略：前端必须能处理字段缺失的过渡期。
6. 产出后立即补单测 / 接口测试 / 类型检查，保证契约真的落地。

## 输出契约

```markdown
# API Contract Coding Summary

## Backend Changes
- routes:
- schemas:
- services:

## Frontend Changes
- api:
- types:
- views / stores:

## Compatibility Notes
- 
```

## 调用输入模板

```text
spec_id：
contract_id：
后端目标模块：
前端目标模块：
新增 / 修改字段：
兼容要求：
```

## 守则

- 不允许绕过 Contract Spec 直接猜字段。
- 不允许后端和前端各自定义不同语义的同名字段。
- 前端不得直接在组件中散落处理接口细节，统一通过 `api/` 与 `types/` 层。
- 任何 breaking field 变更都必须先回到 Contract 审计阶段。

## 完成标准

- [ ] 后端路由 / schema 已对齐契约
- [ ] 前端 api / types 已同步
- [ ] 已写明兼容处理与过渡策略
- [ ] 已补充对应测试或类型校验

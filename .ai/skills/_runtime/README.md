# Skill 运行化使用指南

## 一、目标

让 `.ai/skills/` 下的 Skill 不只是说明文档，而是能被稳定调用、可审计复用的工程动作单元。

## 二、推荐使用方式

### 方式 1：手工按顺序调用
开发者按 `README` 中的阶段顺序逐步执行。

### 方式 2：由上层 Agent 编排
由 Claude / OMC / 其他多 Agent 系统按以下信息调用：
- frontmatter 元数据
- prerequisites
- next
- integrates_with
- acceptance

## 三、统一约定

- 输入尽量围绕 `spec_id / change_id / ticket`
- 输出尽量落到 `.ai/specs/` 与 `.ai/runbooks/`
- 审计结论统一使用 `pass / warn / block / conditional-pass`
- 状态统一使用 `draft / ready-for-audit / ready-for-implementation / ready-for-release / completed`

## 四、Skill 与 Command 对照

- [[.ai/skills/_runtime/skill-command-map.md|Skill 对应 Command 对照表]]

## 五、建议后续继续补齐的 Skill

- architecture-impact-brief
- api-contract-coder
- security-boundary-reviewer
- changelog-and-runbook
- acceptance-matrix-builder

## 五、后续升级方向

1. 为每个 Skill 增加示例输入/输出
2. 增加跨仓库专项 Skill
3. 增加发布/灰度/回滚专项 Skill
4. 增加事故复盘自动回流链

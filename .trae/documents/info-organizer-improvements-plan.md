# info-organizer 四项改进实施方案

## Context（为什么改）

豆包对 info-organizer 做了深度分析评价，提出6类局限。经核实（见末尾附录勘误）：2条误判（organize-incoming 未缺失、统计能力有基础雏形），1条属过度工程不做（多用户协作——个人文员场景），1条违背轻量化哲学不做（语义检索/向量库）。剩下4条属实且值得落地：五维维度偏少、type 无自定义扩展机制、缺独立统计场景、无定时自动化。本方案把这4条做实，并把豆包误判在附录澄清。

## 改动总览

| 改进 | 改/建文件 | 性质 |
|---|---|---|
| 1 五维加可选维度 | five-dimension-tagging.md（加可选维度小节+schema）、knowledge-card.md（frontmatter 加3可选字段） | 改 |
| 2 type 自定义注册 | five-dimension-tagging.md（加注册小节）、knowledge/MEMORY.md（加注册表） | 改 |
| 3 统计分析场景 | scenes/stats.md（新建）、templates/stats-report.md（新建）、SKILL.md（强触发表+场景速查加条目） | 新建+改 |
| 4 schedule 定时自动化 | scenes/scheduled-review.md（新建）、SKILL.md（强触发表+场景速查加条目） | 新建+改 |

三处副本同步：工作区版 → 全局版 → io-verify-workspace 版，每项改完 diff 校验。最后 git commit + push GitHub。

---

## 改进1：五维加可选维度（priority/audience/confidential）

**原则**：现有五维（source/type/urgency/relates_to/gist）保持必填不动，新增3字段为**可选**，仅存知识卡片 frontmatter，**不进 CSV 台账**（避免破坏旧台账行）。

- `references/five-dimension-tagging.md`：五维定义表后加"可选扩展维度"小节，列表说明 priority(高/中/低,默认中)、audience(接收/知会对象,默认本人)、confidential(公开/内部/机密,默认内部)；schema 代码块追加3行可选字段+默认值注释。
- `templates/knowledge-card.md`：frontmatter 模板追加3行可选字段（占位符+注释"可选，缺省见 five-dimension-tagging.md"）。
- `templates/information-ledger.csv`：**不加列**（保持 date,source,type,urgency,relates_to,gist,path 7列）。如需按 priority 统计，用 Grep 搜卡片 frontmatter。
- `SKILL.md`：五维表下方加一句"另有3个可选扩展维度（priority/audience/confidential），详见 references/five-dimension-tagging.md"。

## 改进2：type 自定义注册机制

- `references/five-dimension-tagging.md`：打标规则后加"type 扩展注册"小节——打标优先用 knowledge/MEMORY.md 已注册值，拿不准用"其他"，确认新行业 type 时回写 MEMORY.md 注册表。
- `knowledge/MEMORY.md`（工作区版；全局版 skill 不带 knowledge/，由用户首次使用时在工作区创建）：新增 `## 自定义 type 注册表` 章节，格式示例：
  ```
  - 法律: 合同/案例/法条
  - 医疗: 病历/医嘱/报告
  - 研发: 需求/设计/缺陷单
  ```
  每行一行业，注册后即纳入 type 候选枚举。
- `SKILL.md`：不适用边界上方加一句"type 可自定义扩展，见 references/five-dimension-tagging.md 注册机制"。

## 改进3：统计分析场景（新建）

- `scenes/stats.md`（新建）：触发条件"统计台账/盘点趋势/出简报"。流程：
  1. Grep `information-ledger.csv` 统计总条数（`pattern: "^2026"` count 模式）
  2. 按 type 分组：`pattern: ",会议纪要,"` count（前后逗号锚定第3列）
  3. 按 source 分组：`pattern: "^2026-10-09,行政部"` count
  4. 按月趋势：`pattern: "^2026-10-"` count
  5. 填 templates/stats-report.md 产出简报
- `templates/stats-report.md`（新建）：含 总条数/按 type 表/按 source 表/按月趋势/Top5 gist 五段。
- `SKILL.md` 强触发索引表加行："要统计台账、出趋势简报 → scenes/stats.md"；场景速查加链接。

## 改进4：schedule 定时自动化（新建）

- `scenes/scheduled-review.md`（新建）：触发条件"设周度自动复盘/定时整理"。流程：
  1. 调用 Skill 工具 name=schedule 创建周度任务（具体参数以 schedule skill 文档为准）
  2. 任务指令：按 scenes/weekly-synthesis.md 执行周度复盘，周报存 knowledge/archives/weekly/YYYY-WW.md
  3. 用 schedule list 确认任务已建
- `SKILL.md` 强触发索引表加行："要设定时自动整理/周度自动复盘 → scenes/scheduled-review.md"；场景速查加链接。

## 改进5（文档）：SKILL.md 索引更新

强触发索引表新增2行（统计、schedule）；场景速查新增2条链接。五维表加可选维度提示句。不适用边界上方加 type 扩展提示句。

---

## 扩展后 frontmatter schema 示例

```yaml
---
title: <必填>                    # 卡片标题
source: <必填>                   # 来源
type: <必填>                     # 枚举或 MEMORY.md 注册值
urgency: <必填>                  # 立即/本周/归档备查
relates_to: <必填>               # 关联
gist: <必填,≤20字>               # 关键信息
date: <必填>                     # YYYY-MM-DD
status: active                   # 必填
# 以下可选
priority: <高/中/低>             # 默认 中
audience: <接收对象>             # 默认 本人
confidential: <公开/内部/机密>   # 默认 内部
superseded_by: <可选>            # 仅 superseded 时
---
```

---

## 验证方式（端到端）

1. **五维可选维度**：新建一张卡片填 priority:高/audience:李总/confidential:内部；旧卡片不填仍能被 Grep 命中 frontmatter。
2. **type 自定义**：MEMORY.md 注册"法律:合同/案例/法条"后，新卡片 type:合同，`Grep pattern: "type: 合同"` 命中。
3. **统计场景**：台账≥3行时按 scenes/stats.md 跑，产出简报数字与台账一致。
4. **schedule 自动化**：调 schedule list 能看到新建周度任务，下次触发产出周报到 archives/weekly/。
5. **三处同步**：工作区版改完 → 全局版 → io-verify-workspace 版，每项 `diff` 或 `Select-String` 校验关键新增词一致。
6. **GitHub**：git commit（feat: 四项改进+勘误）+ push origin main。

---

## 附录：豆包评价勘误

豆包深度评价里以下2条与事实不符，澄清如下：

1. **"organize-incoming.md 缺失/无法获取"** — 误判。该文件在工作区版（.trae/skills/info-organizer/scenes/organize-incoming.md）和全局版均存在且内容完整，含触发条件、9步流程、产出物、验收标准，是"接收即整理"场景的完整 SOP。
2. **"缺数据统计能力"** — 部分误判。templates/monthly-archive.md 已有按类型/来源分组统计片段，scenes/weekly-synthesis.md 已有从台账按 source/type 分组统计条数的流程。准确表述应为"有基础汇总，缺独立统计场景与趋势简报"——这正是改进3要补的。

另2条不做，原因记录如下：
- **多用户协作**：用户是个人文员场景，加权限/冲突/同步属过度工程，违背轻量化哲学。
- **语义检索/向量库**：需引入嵌入服务/向量数据库，超 skill 纯文本+Grep/Glob 能力，违背轻量化设计，不做。

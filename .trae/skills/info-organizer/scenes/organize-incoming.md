# 场景：整理刚收到的杂乱信息

## 触发条件
用户发来一堆零散信息（会议通知 + 附件清单 + 跨部门诉求 + 数据片段……），要求"帮我整理一下""归类一下""梳理一下这些"。

## 执行步骤

1. **接收即整理**：通读全部信息，初步分出独立"信息单元"（一条通知、一个诉求、一组数据各算一个单元）。
2. **判断是否需进一步整理**：原始资料→进入本流程；已加工信息→直接打标入桶。
3. **五维打标**：对每个信息单元，按 [five-dimension-tagging.md](../references/five-dimension-tagging.md) 填 frontmatter 五维（source/type/urgency/relates_to/gist≤20字）。**type 行业优先**：含行业关键词（合同/病历/需求等）必须用对应注册值，不落通用枚举。**可选字段必查**：信息提到具体接收人/责任人（如"李总""王医生"）→ 填 `audience`；涉敏感事项（合同/薪酬/人事/病历）→ 填 `confidential`（机密/内部）；有紧急/关键决策信号 → 填 `priority`（高/中/低）。能填则填，不留空。
4. **三段式提炼**：对每个单元写正文——背景是什么 / 核心结论是什么 / 我需做什么（仅长文档才需完整三段式，简短通知可只写"核心结论 + 我需做什么"）。
5. **生成知识卡片**：用 [templates/knowledge-card.md](../templates/knowledge-card.md) 模板，每单元产出一张卡片。
6. **命名与入桶**：按 `YYYYMMDD_部门_文档类型_v版本` 命名，按 [para-and-naming.md](../references/para-and-naming.md) 的入桶决策放入正确 PARA 桶。
7. **登记台账**：每张卡片追加一行到 `information-ledger.csv`（date/source/type/urgency/relates_to/gist/path）。
8. **写日记**：当天日记 `knowledge/daily/YYYY-MM-DD.md` 追加一条整理记录（单元标题 + 卡片路径）。
9. **处理待确认事项**：模糊信息标"待确认"，形成问题清单回复用户。

## 产出物
- N 张知识卡片（文件名规范，入正确桶）
- `information-ledger.csv` 追加 N 行
- 当天日记追加 N 条
- 待确认问题清单（回复用户）

## 验收
- 每张卡片五维 frontmatter 齐全，gist ≤20 字；
- 卡片在 30 秒内可用 Grep/Glob 定位；
- 台账与卡片路径一一对应；
- 待确认事项有明确清单，不遗漏模糊信息。

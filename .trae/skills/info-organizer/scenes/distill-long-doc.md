# 场景：提炼长文档要点

## 触发条件
用户提供一篇长文档（制度文件、会议纪要全文、报告、长邮件），要求"提炼要点""压缩一下""总结关键""给我摘要"。

## 执行步骤

1. **通读定结构**：先读全文，识别文档天然结构（章节/段落/议题）。
2. **五维打标**：按 [five-dimension-tagging.md](../references/five-dimension-tagging.md) 给整篇文档打 source/type/urgency/relates_to/gist。
3. **三段式提炼**（核心压缩模板）：
   - **背景是什么**——这事为什么发生、上下文、相关制度/项目。
   - **核心结论是什么**——要点、数据、决议，分条列出，每条≤30字。
   - **我需做什么**——动作项（谁、做什么、何时），无动作则写"无需动作，归档备查"。
4. **生成知识卡片**：用 [templates/knowledge-card.md](../templates/knowledge-card.md)，正文填入三段式。
5. **命名入桶**：`YYYYMMDD_部门_文档类型_v1`，按 [para-and-naming.md](../references/para-and-naming.md) 入桶（制度文件多入 resources，会议纪要入对应 project/area 实体）。
6. **登记台账**：追加一行到 `information-ledger.csv`。
7. **超长拆分**（可选）：若原文含多个独立议题，拆成多张卡片，每张一个议题，主卡片 `relates_to` 指向子卡片。

## 产出物
- 1 张（或多张）知识卡片，含三段式正文
- `information-ledger.csv` 追加对应行
- 回复用户：三段式摘要（背景/结论/我需做什么）

## 验收
- 摘要能让用户在 2 分钟内掌握原文要点；
- 核心结论每条≤30字，可被直接引用；
- 动作项有责任人与时限（或明确无动作）；
- 卡片可被 Grep 按 gist 检索到。

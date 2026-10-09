---
name: info-organizer
version: 1.0.0
description: "信息整理能力：把杂乱、碎片、多源信息快速转化为结构化、可检索、可复用的知识资产。当用户要求整理资料、归类文件、提炼长文档要点、建立知识库或台账、做信息盘点、周度复盘、或把零散信息结构化时调用。"
---

# 信息整理（Information Organizer）

把杂乱、碎片、多源的信息，快速转化为**结构化、可检索、可复用**的知识资产。本 Skill 是所有其他文员/办公 Skill 的地基——"判断什么是重要信息"需要业务理解，AI 难以替代，正是真正的杠杆点。

> 设计来源：保留"点点"方案的三段式思路、五维分类法、三段式提炼、命名规范与验收标准；用 paperclip PARA memory 的四桶结构、事实更替（supersede 不删）、"写下来不靠脑子记"补足持久化层；五维标签落到 YAML frontmatter 实现机器可读；检索用内置 Grep/Glob 替代外部工具。

## 核心准则

1. **写下来，不靠脑子记**——记忆跨不过会话重启，文件能。想记住什么就**写进文件**，绝不留在临时上下文里。
2. **接收即整理**——文件/信息一到手就做初步判断与打标，不堆到"以后集中处理"。
3. **不删除，只更替**——旧事实标 `status: superseded` 并指向 `superseded_by`，绝不物理删除，保留演进轨迹。
4. **资产化**——整理的产物必须沉淀为可复用模板/台账/知识卡片，而非一次性输出。

## 不适用边界（加载后若命中以下场景，应退出本 Skill，回退裸答或转交）

本 Skill 专注"把杂乱信息结构化为知识资产"。若用户真实意图属以下场景，**不要**套用打标/入桶/台账流程：

- **纯写作表达**（写通知/邮件/汇报/纪要草稿）→ 用户要"产出文案"，直接写；仅当还需"存档/归类"时才叠加本 Skill。
- **纯消息传递**（发群/转发/通知到人）→ 走 lark-im 等，不需结构化。
- **纯任务/日程执行**（建日程/建任务/预定会议室）→ 走 lark-calendar/lark-task。
- **纯数据计算**（求和/透视/公式）→ 走 lark-sheets/Excel。
- **用户明确只要一个直接答案**（如"这个数据是多少"）→ 直接答，不绕道整理资产。

**边界判定**：用户动词是"整理/归类/提炼/盘点/建库/复盘/结构化"→ 适用；是"写/发/建/算/查某值"→ 不适用，回退。

## 三段式流程

信息整理的本质是建立从输入到输出的标准化处理流程，分三步：

### 一、标准化输入
- 统一接收渠道，拒绝口头转述（要落到文件/消息记录）。
- 对每份信息初步判断：原始资料 or 已加工？是否需进一步整理？
- 重要信息"接收即整理"，不积压。

### 二、结构化处理
按 **五维分类法** 给每份信息打标签（机器可读 YAML，详见 [five-dimension-tagging.md](references/five-dimension-tagging.md)）：

| 维度 | 取值示例 | 说明 |
|---|---|---|
| source 来源 | 部门/人/渠道 | 哪里来的 |
| type 类型 | 制度文件/会议纪要/数据报表/通知公告/项目资料/其他 | 信息性质 |
| urgency 紧急程度 | 立即/本周/归档备查 | 处理时限 |
| relates_to 关联事项 | 项目名/流程名 | 与什么相关 |
| gist 关键信息 | ≤20 字一句话提炼 | 核心内容 |

再用 **三段式提炼** 压缩长文档（见 [distill-long-doc 场景](scenes/distill-long-doc.md)）：
- **背景是什么**——这事为什么发生、上下文。
- **核心结论是什么**——要点、数据、决议。
- **我需做什么**——动作项、责任人、时限。

### 三、资产化输出
- 整理产物存入 PARA 知识库（见 [para-and-naming.md](references/para-and-naming.md)），文件按 `日期_部门_文档类型_版本` 命名。
- 每条信息生成一张 **知识卡片**（模板见 [templates/knowledge-card.md](templates/knowledge-card.md)），并追加一行到 **信息登记台账**（[templates/information-ledger.csv](templates/information-ledger.csv)）。
- 重复出现的同类事项，合并成可复用模板（见 [persist-as-asset 场景](scenes/persist-as-asset.md)）。

## PARA 知识库结构

工作区 `knowledge/` 下按 PARA 四桶组织（Tiago Forte 方法）：

```
knowledge/
 projects/   # 有目标/截止日的进行中工作；完成即移入 archives
   <实体>/
     summary.md   # 快速上下文，先载入
     items.yaml   # 原子事实，按需载入
 areas/      # 持续职责，无截止日（如：人/部门/长期职责）
 resources/  # 参考材料、兴趣主题
 archives/   # 以上三桶中已失效的项，保留不删
 daily/      # 日记：knowledge/daily/YYYY-MM-DD.md，事件时间线
```

- 每实体一个文件夹，含 `summary.md`（先载入快速上下文）+ `items.yaml`（原子事实，按需载入）。
- 命名规范：`YYYYMMDD_部门_文档类型_v版本`，例：`20261009_行政部_会议纪要_v1`。
- 实体创建建议阈值：被提及 3+ 次、或与用户有直接关系、或为重要项目/公司——**建议性**，非强制。

详见 [para-and-naming.md](references/para-and-naming.md)。

## 检索与事实更替

- **检索**：用 Grep（关键词/正则，按 `gist`/`source`/`type` 等 frontmatter 字段）+ Glob（按命名模式 `*_行政部_会议纪要_*`）定位文件，满足"30 秒找到"。
- **更替**：旧事实不删，标 `status: superseded`、`superseded_by: <新卡片路径>`；新卡片 `status: active`。
- **周度复盘**：每周五重写活跃实体的 `summary.md`（从 items.yaml 活跃事实合并），把重复事项合并为模板。

详见 [recall-and-supersede.md](references/recall-and-supersede.md)。

## 强触发索引

命中任一触发条件时，**MUST 在执行下一步前读取对应 reference 或 scene**。命中多条按顺序读取，同一文件只读一次。

| 强触发条件（命中任一即必读） | 必读文件 |
|---|---|
| 要给信息打五维标签、写知识卡片 frontmatter、或不确定某维度取值 | [references/five-dimension-tagging.md](references/five-dimension-tagging.md) |
| 要把文件存入知识库、不确定放哪个 PARA 桶、或要命名文件 | [references/para-and-naming.md](references/para-and-naming.md) |
| 要检索历史信息、或遇到新旧事实冲突需更替 | [references/recall-and-supersede.md](references/recall-and-supersede.md) |
| 收到一堆杂乱信息要当场整理（接收即整理 → 入桶） | [scenes/organize-incoming.md](scenes/organize-incoming.md) |
| 要提炼长文档要点（三段式压缩） | [scenes/distill-long-doc.md](scenes/distill-long-doc.md) |
| 要把整理结果沉淀为模板/台账/可复用资产 | [scenes/persist-as-asset.md](scenes/persist-as-asset.md) |
| 要做周度/月度复盘合并 | [scenes/weekly-synthesis.md](scenes/weekly-synthesis.md) |

## 场景速查

当任务目标与场景匹配时，读对应场景手册按流程执行：

- [整理刚收到的杂乱信息](scenes/organize-incoming.md)：接收即整理 → 五维打标 → 三段式提炼 → 生成知识卡片 → 入桶 + 登记台账。
- [提炼长文档要点](scenes/distill-long-doc.md)：三段式压缩长文档为知识卡片。
- [沉淀为可复用模板/台账](scenes/persist-as-asset.md)：把重复出现的同类事项抽象为模板。
- [周度复盘合并](scenes/weekly-synthesis.md)：重写 summary、合并同类模板、清理失效项。

## 验收标准

- 任何一份经手过的文件，能在 **30 秒内** 用 Grep/Glob 找到。
- 同事问"上个月那个数据是多少"，能在 **1 分钟内** 调出对应知识卡片。
- 新接手的工作，能用整理好的历史资料在 **2 小时内** 理清来龙去脉。
- 旧事实被更替后仍可追溯（`superseded_by` 链可回溯）。

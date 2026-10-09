# 信息整理 Skill 创造与应用方案

## Context（为什么做这件事）

用户在小红书看到"文员三层 Skill 体系"帖子（底层通用能力 / 岗位专业 / 增值技能），评论区 AI 助手"点点"选出最有价值的前三名：①信息整理能力 ②自动化提效 ③数据方向，并给出了第一名"信息整理能力"的详细搭建思路（标准化输入→结构化处理→资产化输出、五维分类法、三段式提炼、命名规范、验收标准）。

用户希望：
1. 深度分析评价帖子 + 点点的方案；
2. 在 GitHub 等开源平台找类似项目，取精华去糟粕；
3. 博采众长，把"信息整理能力"做成一个**可被各 Agent 调用的 Trae Skill**，并落地应用。

目标产物：一个名为 `info-organizer` 的 Skill，放在工作区 `.trae/skills/`，验证可用后再推全局。

---

## 一、对帖子与点点方案的深度评价

### 帖子三层体系：评价为"框架扎实、偏人本流程"
- 三层金字塔（底层通用 / 岗位专业 / 增值）符合能力建模的"基础→分化→增值"递进，`会做事→会总事→会总结→会迁移` 的成熟度模型也合理。
- 不足：通篇面向人类手工流程（纸质文件、手动归类），未触及 AI Agent 场景下的"触发、持久化、语义检索、事实更替"。对"可复用资产"只停在"存模板"层面，没有知识库演进机制。

### 点点 Top3 排名：评价为"排序合理、理由充分"
- 信息整理排第一成立：它是其他所有 Skill 的地基，"判断什么是重要信息"需要业务理解，AI 难以替代——这是真正的杠杆点。
- 自动化提效第二、数据方向第三的 ROI 与转岗跳板论证都有说服力。

### 点点第一名方案的精华（保留）
- **三段式思路**：标准化输入 → 结构化处理 → 资产化输出，是信息整理的正确心智模型。
- **五维分类法**：来源 / 类型 / 紧急程度 / 关联事项 / 关键信息(≤20字)，标签维度设计精炼。
- **三段式提炼**：背景是什么 / 核心结论是什么 / 需要我做什么，长文档压缩模板。
- **命名规范**：`日期_部门_文档类型_版本`，可检索性强。
- **验收标准**：30 秒找到、1 分钟调出、2 小时理清来龙去脉——可量化、可验证。

### 点点方案的不足（需补足，正是开源精华要补的）
1. **缺持久化与事实更替层**：点点只说"存模板"，没说旧事实如何更新、冲突如何消解。
2. **缺语义检索**：靠人工翻文件夹无法做到"30 秒找到"，需程序化检索。
3. **五维标签未机器可读化**：停在口语描述，需落到 YAML frontmatter 才能被 Agent 程序化消费。
4. **缺触发设计**：作为 Agent Skill，必须有"何时调用"的契约，点点方案完全没有。
5. **缺周期性复盘机制**：点点只讲单次整理流程，无周度/月度合并、summary 重写与失效清理机制（该机制由 paperclip weekly synthesis 补足）。

> 注：上一版误把 paperclip 的"3+ 次数建实体"规则当作点点的不足，已更正——该规则来自 `paperclipai/paperclip`，点点方案中并无"实体"概念。该规则的"过死"问题已在下方"糟粕"列处理（改为建议性）。

---

## 二、开源参考与"取精华去糟粕"

| 项目 | 精华（取） | 糟粕（去） |
|---|---|---|
| `paperclipai/paperclip` skills/para-memory-files | PARA 四桶(projects/areas/resources/archives)；三层记忆(知识图谱+日记+隐性知识)；事实更替 `supersede` 不删除；"写下来不靠脑子记"；周度重写 summary | 依赖 `qmd` 工具→改用内置 Grep/Glob；过重 YAML schema→精简 |
| `jdrhyne/agent-skills` knowledge-graph | 事实抽取→实体文件夹；weekly synthesis 重写活跃事实 | 实体创建阈值过死→改建议性 |
| skillmd.ai `learn` skill | 从当前对话抽取洞察并持久化到知识库 | — |
| Google Antigravity / `gh skill` 规范 | SKILL.md frontmatter(name+description=触发契约)；progressive disclosure(仅 description 决定触发，正文按需加载) | — |
| Trae lark skills 模式 | scenes/(场景手册) + references/(强触发索引) + 命名约定 | — |

**融合策略**：保留点点的三段式 + 五维 + 三段提炼 + 命名 + 验收；用 paperclip 的 PARA + supersede + "写下来"补足持久化层；用 Trae 的 scenes/references/强触发索引组织 Skill 自身；五维标签落到 YAML frontmatter 实现机器可读；检索用内置 Grep/Glob 替代 qmd。

---

## 三、Skill 设计

### 基本信息表
- **name**: `info-organizer`
- **version**: `1.0.0`
- **工作区位置**: `c:\Users\admin\Desktop\Information_Skill\.trae\skills\info-organizer\`
- **description(frontmatter 触发契约)**:
  > 信息整理能力：把杂乱、碎片、多源信息快速转化为结构化、可检索、可复用的知识资产。当用户要求整理资料、归类文件、提炼长文档要点、建立知识库或台账、做信息盘点、周度复盘、或把零散信息结构化时调用。

### 目录结构
```
info-organizer/
├── SKILL.md                       # 主文件：触发契约 + 三段式核心流程 + 五维速查 + 命名 + PARA + 更替规则 + 强触发索引 + 验收
├── references/
│   ├── five-dimension-tagging.md  # 五维分类法 YAML schema + 示例（来源/类型/紧急/关联/关键信息）
│   ├── para-and-naming.md         # PARA 四桶目录结构 + 命名规范 + 实体创建建议阈值
│   └── recall-and-supersede.md   # 检索(Grep/Glob 语义检索) + 事实更替(supersede 不删) + 周度合并
├── scenes/
│   ├── organize-incoming.md       # 场景：整理刚收到的杂乱信息（接收即整理 → 五维打标 → 入桶）
│   ├── distill-long-doc.md        # 场景：提炼长文档要点（三段式模板）
│   ├── persist-as-asset.md        # 场景：沉底为可复用模板/台账（资产化输出）
│   └── weekly-synthesis.md        # 场景：周度复盘合并（重写 summary、合并同类模板）
└── templates/
    ├── knowledge-card.md         # 知识卡片模板：frontmatter 五维标签 + 三段式正文
    ├── information-ledger.csv     # 信息登记台账（七列：日期/来源/类型/紧急/关联/关键信息/路径）
    └── monthly-archive.md         # 月度工作档案模板
```

### 关键内容设计要点
- **SKILL.md 正文**按 Trae 惯例组织：核心定义 → 三段式流程（标准化输入/结构化处理/资产化输出）→ 五维速查表 → 三段式提炼模板 → PARA 与命名 → 更替规则（"写下来不靠脑子记"；旧事实 `status: superseded, superseded_by:` 不删）→ **强触发索引表**（命中条件→必读 reference/scene）→ 验收标准。
- **knowledge-card.md 模板** frontmatter：
  ```yaml
  ---
  title: <一句话标题>
  source: <来源：部门/人/渠道>
  type: <制度文件|会议纪要|数据报表|通知公告|项目资料|其他>
  urgency: <立即|本周|归档备查>
  relates_to: <关联项目/流程>
  gist: <关键信息，≤20字>
  date: <YYYY-MM-DD>
  status: <active|superseded>
  superseded_by: <可选>
  ---
  ```
  正文为三段式：背景 / 核心结论 / 我需做什么。
- **information-ledger.csv**：`date,source,type,urgency,relates_to,gist,path`，每次整理追加一行，形成可检索台账。
- **PARA 落地**：在工作区建 `knowledge/{projects,areas,resources,archives}/`，每实体一个文件夹含 `summary.md`(先载入)+`items.yaml`(原子事实，按需载入)；日记放 `knowledge/daily/YYYY-MM-DD.md`；隐性经验沉淀进 `MEMORY.md`。
- **检索**：用内置 Grep（关键词/正则）+ Glob（按命名模式）实现"30 秒找到"，替代 paperclip 的 qmd。

---

## 四、实施步骤

1. **建目录**：创建 `info-organizer/` 及 `references/ scenes/ templates/` 子目录。
2. **写 SKILL.md**：frontmatter(name+description) + 正文八节（见上"关键内容设计要点"），强触发索引表指向 references/scenes。
3. **写 references/**：`five-dimension-tagging.md`、`para-and-naming.md`、`recall-and-supersede.md`。
4. **写 scenes/**：`organize-incoming.md`、`distill-long-doc.md`、`persist-as-asset.md`、`weekly-synthesis.md`，每个含触发条件→步骤→产出物→验收。
5. **写 templates/**：`knowledge-card.md`、`information-ledger.csv`、`monthly-archive.md`。
6. **建知识库骨架**：在工作区创建 `knowledge/{projects,areas,resources,archives,daily}/` 与 `MEMORY.md`（空骨架，供 Skill 运行时写入）。
7. **工作区验证**（见下）。
8. **推全局（验证通过后）**：把 `info-organizer/` 复制到 `c:\Users\admin\.trae-cn\builtin\global\skills\`，并在 `skill-config.json` 的 `managedSkills` 登记，使其跨工作区对所有 Agent 可用。

---

## 五、关键文件清单（待创建）

- [SKILL.md](file:///c:/Users/admin/Desktop/Information_Skill/.trae/skills/info-organizer/SKILL.md)
- [references/five-dimension-tagging.md](file:///c:/Users/admin/Desktop/Information_Skill/.trae/skills/info-organizer/references/five-dimension-tagging.md)
- [references/para-and-naming.md](file:///c:/Users/admin/Desktop/Information_Skill/.trae/skills/info-organizer/references/para-and-naming.md)
- [references/recall-and-supersede.md](file:///c:/Users/admin/Desktop/Information_Skill/.trae/skills/info-organizer/references/recall-and-supersede.md)
- [scenes/organize-incoming.md](file:///c:/Users/admin/Desktop/Information_Skill/.trae/skills/info-organizer/scenes/organize-incoming.md)
- [scenes/distill-long-doc.md](file:///c:/Users/admin/Desktop/Information_Skill/.trae/skills/info-organizer/scenes/distill-long-doc.md)
- [scenes/persist-as-asset.md](file:///c:/Users/admin/Desktop/Information_Skill/.trae/skills/info-organizer/scenes/persist-as-asset.md)
- [scenes/weekly-synthesis.md](file:///c:/Users/admin/Desktop/Information_Skill/.trae/skills/info-organizer/scenes/weekly-synthesis.md)
- [templates/knowledge-card.md](file:///c:/Users/admin/Desktop/Information_Skill/.trae/skills/info-organizer/templates/knowledge-card.md)
- [templates/information-ledger.csv](file:///c:/Users/admin/Desktop/Information_Skill/.trae/skills/info-organizer/templates/information-ledger.csv)
- [templates/monthly-archive.md](file:///c:/Users/admin/Desktop/Information_Skill/.trae/skills/info-organizer/templates/monthly-archive.md)

复用资产（新建骨架，供 Skill 写入）：
- `knowledge/{projects,areas,resources,archives,daily}/` 与 `MEMORY.md`

---

## 六、验证方案（端到端）

1. **触发验证**：构造一条杂乱信息（例：一段会议通知 + 附件清单 + 跨部门诉求），以自然语言要求"帮我整理一下"，确认 Agent 能命中 `info-organizer` 的 description 触发契约、加载 SKILL.md。
2. **流程验证**：确认 Agent 按 `organize-incoming` 场景执行——五维打标 → 三段式提炼 → 生成 `knowledge-card.md`（frontmatter 五维标签齐全）→ 追加 `information-ledger.csv` 一行 → 文件按 `日期_部门_类型_版本` 命名存入正确 PARA 桶。
3. **检索验证**：用 Grep 按 `gist`/`source` 关键词、用 Glob 按命名模式，验证能在 30 秒内定位刚写入的卡片，满足点点"30 秒找到/1 分钟调出"验收标准。
4. **更替验证**：对同一事项发一条新信息，确认旧卡片标 `status: superseded, superseded_by:` 而非删除，新卡片为 active。
5. **场景覆盖验证**：分别用"提炼长文档""沉底模板""周度复盘"各跑一次，确认 `distill-long-doc`/`persist-as-asset`/`weekly-synthesis` 场景可被正确触发与执行。
6. **推全局前自检**：确认上述 1-5 全绿后，再执行第八步全局复制与登记。

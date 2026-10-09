# info-organizer — 信息整理 Skill

把杂乱、碎片、多源的信息，快速转化为**结构化、可检索、可复用**的知识资产。

**跨平台通用 Agent Skill**（Trae/Claude/Cursor/OpenAI/纯 Prompt）——所有文员/办公 Skill 的地基。"判断什么是重要信息"需要业务理解、AI 难以替代，正是真正的杠杆点。

> **不是学一个软件操作，而是建立一套思维习惯和工作系统。** 掌握了它，学 Excel 更快（懂得先理清数据逻辑），学自动化提效更容易（清楚哪些重复环节值得被自动化）。

---

## 核心能力（实测验证）

### 1. 三段式信息整理流程
`标准化输入 → 结构化处理 → 资产化输出`
- **标准化输入**：统一接收渠道，接收即整理，不积压
- **结构化处理**：五维分类 + 三段式提炼
- **资产化输出**：沉淀为知识卡片/台账/模板，存入 PARA 知识库

### 2. 五维分类法（机器可读 YAML frontmatter）
每条信息从五维打标，落到 Markdown frontmatter，使信息可被程序化检索：

| 维度 | 字段 | 取值 |
|---|---|---|
| 来源 | `source` | 部门/人/渠道 |
| 类型 | `type` | 通用：制度文件/会议纪要/数据报表/通知公告/项目动态/其他；行业注册值：合同/案例/法条/病历/医嘱/报告/需求/设计/缺陷单 |
| 紧急程度 | `urgency` | 立即/本周/归档备查 |
| 关联事项 | `relates_to` | 项目名/流程名（可多值） |
| 关键信息 | `gist` | ≤20 字一句话 |

另 3 个可选维度（缺省有默认值）：`priority`（高/中/低）、`audience`（接收对象）、`confidential`（公开/内部/机密）。

### 3. type 行业优先 + 与桶解耦
- **行业优先（必做）**：信息含行业关键词（合同/病历/医嘱/需求等）→ type **必须**用对应注册值，不落通用枚举。例："医嘱执行复核单" → `type: 医嘱`（非项目动态）。
- **type 与桶解耦**：type 是信息性质，**不含桶名**。有截止的工作入 projects 桶，但 type 仍用行业值/通用枚举——废弃了把"项目资料"（实为 PARA projects 桶名）当作 type 的混层设计。
- **type 自定义注册**：默认内置法律/医疗/研发三行业，工作区 `MEMORY.md` 可覆盖/扩展。

### 4. 三段式提炼（压缩长文档）
- **背景是什么**——上下文、相关制度/项目
- **核心结论是什么**——要点、数据、决议（每条 ≤30 字）
- **我需做什么**——动作项、责任人、时限

### 5. PARA 四桶知识库（Tiago Forte 方法）
```
knowledge/
  projects/   # 有目标/截止日的进行中工作；完成→移入 archives
     <实体>/
       summary.md   # 快速上下文，先载入（≤200字）
       items.yaml   # 原子事实，按需载入
       <卡片>.md
  areas/      # 持续职责，无截止日（人/部门/长期职责）
  resources/  # 参考材料、通用知识
  archives/   # 以上三桶中已失效的项，保留不删
  daily/      # 日记：daily/YYYY-MM-DD.md，事件时间线
  MEMORY.md   # 隐性知识层：操作经验、模板索引、自定义 type 注册表
```

### 6. 事实更替（不删除，只更替）
旧事实从不物理删除，仅标 `status: superseded` 并通过 `superseded_by` 指向新事实，形成可回溯的演进链路。支持双向追溯（旧→新、新→旧），满足复盘/审计/追责需求。

### 7. 检索（轻量化，无外部依赖）
- **Grep**：按 frontmatter 字段（`source`/`type`/`urgency`/`gist` 等）正则检索
- **Glob**：按命名模式（`*_行政部_会议纪要_*`）秒级定位
- **命名规范**：`YYYYMMDD_部门_文档类型_v版本`，文件名本身携带元数据
- 完全基于纯文本文件系统，**不依赖任何外部数据库/搜索服务**，数据本地化无隐私泄露

### 8. 不适用边界（防误触发）
加载后若命中以下场景，应退出本 Skill 回退裸答或转交：
- 纯写作表达（写通知/邮件/汇报草稿）→ 直接写
- 纯消息传递（发群/转发）→ 用通讯/IM 工具或对应能力
- 纯任务/日程执行（建日程/建任务）→ 用日历/任务管理工具
- 纯数据计算（求和/透视）→ 用表格/计算工具
- 用户明确只要一个直接答案 → 直接答

### 9. 六大场景 SOP
| 场景 | 触发 | 产出 |
|---|---|---|
| 整理杂乱信息 | 用户发零散信息要求整理 | 知识卡片+台账+日记 |
| 提炼长文档 | 要求提炼要点/压缩 | 三段式知识卡片 |
| 沉淀模板 | 同类事项重复出现 | 可复用模板（存 resources/templates/） |
| 周度复盘 | 要求周度/月度合并 | 重写 summary + 合并模板 + 清理失效 + 周报 |
| 统计简报 | 要求统计台账/出趋势 | 按维度分组统计 + 趋势简报 |
| 定时自动复盘 | 要求设周度自动任务 | 调平台定时任务能力（Trae 中为 schedule skill）设 cron 周度触发 weekly-synthesis |

### 10. 可量化验收标准
- 任何经手文件，**30 秒内**用 Grep/Glob 找到
- 同事问"上个月那个数据"，**1 分钟内**调出对应知识卡片
- 新接手工作，**2 小时内**用历史资料理清来龙去脉
- 旧事实更替后仍可追溯（`superseded_by` 链可回溯）

---

## 文件结构

```
info-organizer/
├── SKILL.md                         # 主文件：触发契约+核心准则+三段式+PARA+强触发索引+不适用边界+验收
├── references/
│   ├── five-dimension-tagging.md    # 五维分类+type行业优先+注册机制+打标规则
│   ├── para-and-naming.md           # PARA四桶+实体结构+命名规范+入桶决策
│   └── recall-and-supersede.md      # 检索+事实更替+周度复盘
├── scenes/
│   ├── organize-incoming.md         # 整理杂乱信息
│   ├── distill-long-doc.md          # 提炼长文档
│   ├── persist-as-asset.md          # 沉淀模板
│   ├── weekly-synthesis.md          # 周度复盘
│   ├── stats.md                     # 统计简报
│   └── scheduled-review.md          # 定时自动复盘
├── templates/
│   ├── knowledge-card.md            # 知识卡片模板
│   ├── information-ledger.csv       # 信息台账
│   ├── monthly-archive.md           # 月度档案
│   └── stats-report.md              # 统计简报模板
└── knowledge/                       # PARA 知识库（运行时生成）
    ├── projects/ areas/ resources/ archives/
    ├── daily/
    └── MEMORY.md                    # 隐性知识+自定义 type 注册表
```

---

## 安装

跨平台安装支持——见 [`.trae/skills/info-organizer/adapters/`](.trae/skills/info-organizer/adapters/) 目录：

| 平台 | 安装方式 | 说明 |
|---|---|---|
| Trae 工作区 | 复制文件夹到 `.trae\skills\` | 见下方"Trae 工作区安装" |
| Trae 全局 | 复制到 `.trae-cn\builtin\global\skills\` | 见下方"Trae 全局安装" |
| 任何大模型（ChatGPT/Claude/豆包等） | 粘贴 prompt 即用 | 见 [adapters/prompt-only/SKILL.md](.trae/skills/info-organizer/adapters/prompt-only/SKILL.md) |
| Claude/Cursor/OpenAI | 待扩展（等真实反馈） | 见 [adapters/](.trae/skills/info-organizer/adapters/) 目录 |

### Trae 工作区安装（单工作区 Agent 可用）
```powershell
cd D:\你的工作区
New-Item -ItemType Directory -Force -Path ".\.trae\skills"
git clone https://github.com/gorgeoustrouble10-maker/info-organizer $env:TEMP\io
Copy-Item "$env:TEMP\io\.trae\skills\info-organizer" ".\.trae\skills\info-organizer" -Recurse -Force
```

### Trae 全局安装（跨所有工作区，重启 Trae 后生效）
```powershell
$src = "<clone目录>\.trae\skills\info-organizer"
$dst = "$env:USERPROFILE\.trae-cn\builtin\global\skills\info-organizer"
Copy-Item $src $dst -Recurse -Force
```

> **重要**：clone 下来不会自动生效——必须把 `info-organizer\` 文件夹复制进 `.trae\skills\`（工作区）或 `.trae-cn\builtin\global\skills\`（全局）。**不是下载即用。**

---

## 使用

### 自动触发
用户对 Agent 说"整理/归类/提炼/盘点/建库/复盘/结构化"等动词并给出杂乱信息时，Agent 会自动调用本 Skill。

**示例**：
```
整理这条：医务室发来一份医嘱执行复核单，赵护士长要今日内签字
```
Agent 会产出知识卡片（`type: 医嘱`、`audience: 赵护士长`、`confidential: 机密`、`priority: 高`），入 projects 桶，登记台账，写日记。

### 精简 Prompt（任何 Agent 即用，零安装）

> 完整版见 [adapters/prompt-only/SKILL.md](.trae/skills/info-organizer/adapters/prompt-only/SKILL.md)（含最新 type 行业优先 + 桶解耦 + 可选维度必查改进）。下方为历史精简版：
```
你是信息整理专家。对用户发来的任何杂乱信息，按以下流程处理：
【三段式】标准化输入 → 结构化处理 → 资产化输出
1. 拆分：把信息拆成独立单元。
2. 五维打标：source来源/type类型(通用:制度文件|会议纪要|数据报表|通知公告|项目动态|其他；行业:合同|病历|医嘱|需求等，含行业关键词优先用行业值)/urgency紧急(立即|本周|归档备查)/relates_to关联/gist关键信息(≤20字)。
3. 三段式提炼：背景/核心结论(每条≤30字)/我需做什么(动作项+责任人+时限)。
4. 产出知识卡片：markdown，frontmatter含五维+status，命名 YYYYMMDD_部门_类型_v版本。
5. 存入PARA：projects/areas/resources/archives；日记记daily/YYYY-MM-DD.md。
6. 不删除只更替：旧标superseded+superseded_by，新标active。
7. 检索用Grep按frontmatter+Glob按命名，目标30秒找到。
8. 重复事项合并成模板；每周重写活跃实体summary。
准则：写下来不靠脑子记/接收即整理/不删除只supersede/必须资产化。
```

---

## 设计来源

本 Skill 并非从零原创，而是对多个成熟知识管理方法论的**工程化整合与落地**：

| 来源 | 取精华 | 去糟粕 |
|---|---|---|
| 小红书"点点"方案 | 三段式思路、五维分类法、命名规范、验收标准 | 缺持久化/语义检索/触发契约/周期复盘（已补足） |
| [paperclipai/paperclip](https://github.com/paperclipai/paperclip) PARA memory | PARA 四桶、三层记忆、事实更替 supersede、"写下来不靠脑子记"、周度重写 summary | 依赖 qmd 外部工具（改用内置 Grep/Glob）、过重 YAML schema（精简）、"3+次建实体"过死（改建议性） |
| Google Antigravity / `gh skill` 规范 | SKILL.md frontmatter 契约、references/scenes/templates 分层 | — |
| Trae lark skills | 强触发索引模式、不适用边界 | — |
| 豆包深度分析评价 | 五维可选维度（priority/audience/confidential）、type 自定义注册、统计场景、定时自动化 | organize-incoming 误判缺失（实际存在）、统计能力误判（已有雏形） |

---

## 已验证能力

- ✅ 工作区内 Agent 自动触发调用
- ✅ 跨工作区 Agent 调用（io-verify-workspace 实测）
- ✅ 三段式流程产出知识卡片
- ✅ Grep/Glob 检索（30 秒定位）
- ✅ 事实更替链（superseded → active 可回溯）
- ✅ 统计场景产出趋势简报
- ✅ schedule skill 定时自动化（周度复盘 cron `0 18 * * 5`）
- ✅ type 行业优先 + 桶解耦（重构后）

## 局限性（诚实）

- 检索为基础关键词/正则，无语义检索/向量检索（遵循轻量化哲学，不引入外部依赖）
- 单用户设计，无多用户协作/权限/冲突合并
- 流程需人工触发，无监听新文件自动打标（定时自动化仅周度复盘）
- 五维+通用 type 覆盖通用办公，垂直行业需自行扩展注册

---

## License

MIT

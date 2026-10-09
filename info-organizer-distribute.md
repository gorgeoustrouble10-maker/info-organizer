# info-organizer 分发与装载指南

跨平台通用 Agent Skill——支持 Trae、Claude、Cursor、OpenAI、任何大模型（纯 Prompt）。

## 方式一：粘贴 Prompt（零安装，任何 Agent 即用）

把 [adapters/prompt-only/SKILL.md](.trae/skills/info-organizer/adapters/prompt-only/SKILL.md) 中的代码块整段复制给任意 Agent（Trae/ChatGPT/Claude/豆包/Cursor/Wise……），它即按 info-organizer 方法论整理信息，无需安装 Skill。

**示例**：
```
整理这条：医务室发来一份医嘱执行复核单，赵护士长要今日内签字
```
Agent 会产出知识卡片（`type: 医嘱`、`audience: 赵护士长`、`confidential: 机密`、`priority: 高`），入 projects 桶，登记台账，写日记。

## 方式二：Trae 工作区安装（推荐，完整能力）

详见 [adapters/trae/README.md](.trae/skills/info-organizer/adapters/trae/README.md)。简述：

```powershell
# 在你的工作区根目录执行
cd D:\你的工作区
New-Item -ItemType Directory -Force -Path ".\.trae\skills"
git clone https://github.com/gorgeoustrouble10-maker/info-organizer $env:TEMP\io
Copy-Item "$env:TEMP\io\.trae\skills\info-organizer" ".\.trae\skills\info-organizer" -Recurse -Force
```
装好后该工作区所有 Agent 自动识别（会读 SKILL.md 的 description 决定何时触发）。

## 方式三：Trae 全局安装（跨所有工作区）

```powershell
# 复制到全局 skills 目录（需重启 Trae 生效）
Copy-Item ".trae\skills\info-organizer" "$env:USERPROFILE\.trae-cn\builtin\global\skills\info-organizer" -Recurse -Force
```
与 skill-creator/dynamic-ui 同级，重启 Trae 后所有工作区 Agent 可用。

## 方式四：其他平台（Claude/Cursor/OpenAI 等）

见 [adapters/](.trae/skills/info-organizer/adapters/) 目录——目前占位待扩展，等真实用户反馈后增加对应平台适配包。

## GitHub 分发的正确姿势

1. 仓库推到 `gorgeoustrouble10-maker/info-organizer`
2. 别人 `git clone` 后，按上方方式二/三复制到 Trae skills 目录
3. **不是下载即用**——必须把 `info-organizer/` 放进对应平台的 skills 扫描目录
4. 跨平台适配包在 [adapters/](.trae/skills/info-organizer/adapters/) 下

## 四种方式对比

| 方式 | 适用 | 门槛 | 能力完整度 |
|---|---|---|---|
| 粘贴 Prompt | 任何 Agent、临时任务 | 零安装 | 方法论完整，无模板/场景文件加载 |
| Trae 工作区安装 | 固定工作区团队 | 复制一个文件夹 | 完整（含 references/scenes/templates） |
| Trae 全局安装 | 个人跨工作区复用 | 复制+重启 | 完整，所有工作区自动可用 |
| 其他平台 | Claude/Cursor/OpenAI | 见 adapters/ | 待扩展 |

---

## 核心方法论（所有方式共用）

无论哪种装载方式，Agent 都遵循同一套方法论：

- **三段式流程**：标准化输入 → 结构化处理 → 资产化输出
- **五维分类法**：source/type/urgency/relates_to/gist + 3 可选（priority/audience/confidential）
- **type 行业优先**：含行业关键词（合同/病历/医嘱等）必须用对应注册值，不落通用枚举
- **type 与桶解耦**：type 是信息性质，不写桶名（projects/areas 等）
- **PARA 知识库**：projects/areas/resources/archives + daily + MEMORY.md
- **事实更替**：不删除只 supersede，旧事实标 superseded + superseded_by 指向新事实
- **六大场景**：整理杂乱信息 / 提炼长文档 / 沉淀模板 / 周度复盘 / 统计简报 / 定时自动复盘
- **验收标准**：30 秒找到任何文件、1 分钟调出历史信息、2 小时理清来龙去脉

详见 [SKILL.md](.trae/skills/info-organizer/SKILL.md)。

# Trae 平台适配

## 安装

### 方式一：工作区安装（单工作区可用）

```powershell
cd D:\你的工作区
New-Item -ItemType Directory -Force -Path ".\.trae\skills"
# 从 GitHub clone 后把 info-organizer 文件夹放进 .trae\skills\
git clone https://github.com/gorgeoustrouble10-maker/info-organizer $env:TEMP\io
Copy-Item "$env:TEMP\io\.trae\skills\info-organizer" ".\.trae\skills\info-organizer" -Recurse -Force
```

装好后该工作区所有 Agent 自动识别（会读 SKILL.md 的 description 决定何时触发）。

### 方式二：全局安装（跨所有工作区，重启 Trae 后生效）

```powershell
$src = "<clone目录>\.trae\skills\info-organizer"
$dst = "$env:USERPROFILE\.trae-cn\builtin\global\skills\info-organizer"
Copy-Item $src $dst -Recurse -Force
```

与 skill-creator/dynamic-ui 同级，重启 Trae 后所有工作区 Agent 可用。

> **重要**：clone 下来不会自动生效——必须把 `info-organizer\` 文件夹复制进 `.trae\skills\`（工作区）或 `.trae-cn\builtin\global\skills\`（全局）。**不是下载即用。**

## Trae 专属机制

### 强触发索引自动加载

Trae 会话启动时扫描 skill 目录，把 SKILL.md 注入 available skills 列表。Agent 语义判断调用 Skill 工具时，加载 SKILL.md 全文 + 命中场景读对应 scene/reference 文件。

### 与 lark-* skills 协同

info-organizer 的"不适用边界"明确转交场景：
- 纯消息传递 → lark-im
- 纯任务/日程执行 → lark-calendar/lark-task
- 纯数据计算 → lark-sheets

### 与 schedule skill 集成

定时自动复盘场景（scenes/scheduled-review.md）通过 `Skill` 工具调用 `schedule` skill 创建周度 cron 任务，自动触发 weekly-synthesis 场景。

## 已验证能力

- ✅ 工作区内 Agent 自动触发调用
- ✅ 跨工作区 Agent 调用（io-verify-workspace 实测）
- ✅ 三段式流程产出知识卡片
- ✅ Grep/Glob 检索（30 秒定位）
- ✅ 事实更替链（superseded → active 可回溯）
- ✅ 统计场景产出趋势简报
- ✅ schedule skill 定时自动化（周度复盘 cron `0 18 * * 5`）
- ✅ type 行业优先 + 桶解耦（重构后验证通过）

## 注意

- Trae 会话恢复沿用旧 skill 快照——改 skill 后需新建会话（点"+"）才生效
- 全局安装后需重启 Trae
- 与 lark-* 协同时，info-organizer 只做"结构化沉淀"，消息/日程/计算由对应 skill 处理

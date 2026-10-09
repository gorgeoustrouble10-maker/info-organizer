# 场景：定时自动复盘（schedule 集成）

## 触发条件

- 用户说"设周度自动复盘 / 定时整理 / 自动周报 / 每周自动盘点"
- 用户希望 info-organizer 在固定周期自动触发周度合成，不靠手动记起

## 前置

- Trae 全局 skill `schedule` 可用（可通过 Skill 工具调用）
- knowledge/ 目录已初始化（PARA 四桶存在）

## 流程

1. **确认周期与时段**：与用户确认频率（周/月）和触发时刻（如每周五 18:00）。默认每周五 18:00。
2. **调用 schedule skill 创建任务**：通过 `Skill` 工具调用 `schedule`，创建一个周度任务。任务参数以 schedule skill 当前文档为准（典型字段：任务名、cron/周期、触发指令）。
3. **任务触发指令**：任务被触发时，执行指令为——
   > 按 `scenes/weekly-synthesis.md` 执行周度复盘：定位本周变更的 items/卡片 → 汇总台账 → 重写活跃实体 summary.md → 合并同类模板 → 清理失效项 → 生成周报，存 `knowledge/archives/weekly/YYYY-WW.md`。
4. **确认任务已建**：调用 schedule 的 list/inspect 能力，确认任务已注册、下次触发时间正确。
5. **登记到 MEMORY.md**：在操作经验区追加一条 `[YYYY-MM-DD] 已设定周度自动复盘任务，每周五18:00触发weekly-synthesis`。

## 产出物

- schedule 中注册的周度任务（1 个）
- MEMORY.md 操作经验条目（1 行）
- 触发后每次产出：`knowledge/archives/weekly/YYYY-WW.md` 周报

## 验收

- schedule list 能看到该任务，下次触发时间符合预期。
- 任务触发指令明确指向 weekly-synthesis 场景，产出落到 archives/weekly/。
- 用户不再需要手动记起"该复盘了"。

## 注意

- schedule skill 的具体调用参数以其当前文档为准，本场景只规定"调什么+任务指令内容"，不锁死参数格式。
- 若 schedule skill 不可用（未安装/未授权），回退为"每周五由用户口头触发 weekly-synthesis"，并提示用户安装 schedule。

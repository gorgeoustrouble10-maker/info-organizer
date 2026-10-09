# 场景：统计分析（台账盘点与趋势简报）

## 触发条件

- 用户说"统计台账 / 盘点趋势 / 出简报 / 这月整理了多少 / 信息汇总"
- 需要把 information-ledger.csv 里的条目按维度统计，产出可读简报

## 前置

- `templates/information-ledger.csv` 存在且 ≥1 行数据
- CSV 列序固定：`date,source,type,urgency,relates_to,gist,path`（type 第 3 列）

## 流程

1. **总条数**：`Grep pattern: "^2026-" output_mode: count path: <CSV路径>` → 所有有日期的行数。
2. **按 type 分组**（type 第3列，前后逗号锚定）：
   - 会议纪要：`Grep pattern: ",会议纪要," output_mode: count path: <CSV>`
   - 通知公告：`Grep pattern: ",通知公告," output_mode: count`
   - 数据报表：`Grep pattern: ",数据报表," output_mode: count`
   - 制度文件：`Grep pattern: ",制度文件," output_mode: count`
   - 项目资料：`Grep pattern: ",项目资料," output_mode: count`
   - 其他：`Grep pattern: ",其他," output_mode: count`
3. **按 source 分组**（source 第2列，用行首 date+source 前缀锚定，或直接搜 source 值）：
   - 例：`Grep pattern: "^2026-10-09,行政部" output_mode: count` → 行政部当日条数
   - 跨日期同 source：`Grep pattern: ",行政部-张主管," output_mode: count`（逗号锚定第2列）
4. **按月趋势**：`Grep pattern: "^2026-08-" output_mode: count`、`"^2026-09-"`、`"^2026-10-"` 逐月。
5. **Top5 gist**：`Grep pattern: "gist" output_mode: content` 取最近 5 条（或按 urgency=立即 优先筛）。
6. **填简报**：用 `templates/stats-report.md` 模板，填入数字，存 `knowledge/archives/stats/YYYYMMDD_统计简报_v1.md`。

## 产出物

- `knowledge/archives/stats/YYYYMMDD_统计简报_v1.md`（1 份）

## 验收

- 简报总条数 = CSV 实际行数 - 1（去表头）
- 各 type 分组数之和 = 总条数
- 按月趋势之和 = 总条数
- 数字与台账一致，无遗漏

## 注意

- CSV 若未加 priority/audience/confidential 列（本 Skill 设计上不进 CSV），按这三维统计时改用 Grep 搜知识卡片 frontmatter：`Grep pattern: "priority: 高" path: knowledge/`。
- 若台账 <3 行，提示用户"数据量太少，统计意义有限，建议积累后再出简报"。

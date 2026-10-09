# 场景：统计分析（台账盘点与趋势简报）

## 触发条件

- 用户说"统计台账 / 盘点趋势 / 出简报 / 这月整理了多少 / 信息汇总"
- 需要把 information-ledger.csv 里的条目按维度统计，产出可读简报

## 前置

- `templates/information-ledger.csv` 存在且 ≥1 行数据
- CSV 列序固定：`date,source,type,urgency,relates_to,gist,path`（type 第 3 列）

## 流程

1. **总条数**：在 CSV 中搜索 `^2026-`（行首日期锚定）→ 所有有日期的行数（Trae Grep 用 `output_mode: count`）。
2. **按 type 分组**（type 第3列，前后逗号锚定）——在 CSV 中搜索对应值（Trae Grep 用 `output_mode: count`）：
   - 会议纪要：搜索 `,会议纪要,`
   - 通知公告：搜索 `,通知公告,`
   - 数据报表：搜索 `,数据报表,`
   - 制度文件：搜索 `,制度文件,`
   - 项目动态：搜索 `,项目动态,`（注：原"项目资料"已废弃为"项目动态"）
   - 行业值：搜索 `,医嘱,`/`,合同,`/`,病历,`等
   - 其他：搜索 `,其他,`
3. **按 source 分组**（source 第2列，用行首 date+source 前缀锚定，或直接搜 source 值）：
   - 例：搜索 `^2026-10-09,行政部` → 行政部当日条数
   - 跨日期同 source：搜索 `,行政部-张主管,`（逗号锚定第2列）
4. **按月趋势**：逐月搜索 `^2026-08-`、`^2026-09-`、`^2026-10-` 等行首日期锚定。
5. **Top5 gist**：搜索 `gist` 取最近 5 条（或按 urgency=立即 优先筛）。
6. **填简报**：用 `templates/stats-report.md` 模板，填入数字，存 `knowledge/archives/stats/YYYYMMDD_统计简报_v1.md`。

## 产出物

- `knowledge/archives/stats/YYYYMMDD_统计简报_v1.md`（1 份）

## 验收

- 简报总条数 = CSV 实际行数 - 1（去表头）
- 各 type 分组数之和 = 总条数
- 按月趋势之和 = 总条数
- 数字与台账一致，无遗漏

## 注意

- CSV 若未加 priority/audience/confidential 列（本 Skill 设计上不进 CSV），按这三维统计时改用文件检索搜知识卡片 frontmatter（如搜索 `priority: 高`）。
- 若台账 <3 行，提示用户"数据量太少，统计意义有限，建议积累后再出简报"。

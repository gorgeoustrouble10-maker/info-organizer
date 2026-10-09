# 场景：周度复盘合并

## 触发条件
用户要求"本周复盘""周度盘点""合并一下本周信息""整理本周台账""月度汇总"。

## 执行步骤

1. **定位本周变更**：用 Grep 在 `knowledge/` 检索本周日期范围：
   ```
   Grep pattern: "date: 2026-10-0[2-8]"  path: knowledge/
   ```
   或 Glob 按命名：`Glob pattern: "2026100*_*"  path: knowledge/`。
2. **汇总台账**：从 `information-ledger.csv` 过滤本周行，按 source/type 分组统计条数。
3. **重写活跃实体 summary**：对本周变更过的实体，仅载入 `status: active` 事实，覆盖重写 `summary.md`：
   - ≤200 字概览；
   - 关键事实索引（指向 items.yaml 的 fact id 或卡片路径）。
4. **合并同类模板**：本周重复出现的同类事项，按 [persist-as-asset.md](persist-as-asset.md) 抽象合并为模板。
5. **清理失效项**：
   - 已完成的 project 实体→移入 `archives/`；
   - 已更替的旧事实→确认 `status: superseded` + `superseded_by` 链完整；
   - 不删除任何文件。
6. **生成周报**：用 [templates/monthly-archive.md](../templates/monthly-archive.md)（兼作周/月报）产出本周工作档案——本周整理条数、新增模板、待确认事项、下周关注。
7. **更新 MEMORY.md**：记录本周学到的操作经验/教训（隐性知识层）。

## 产出物
- 重写后的若干 `summary.md`（仅活跃事实）
- 本周工作档案（周报）
- 新合并的可复用模板（如有）
- 失效项已归档/标更替
- `MEMORY.md` 追加经验条目

## 验收
- 每个变更实体的 summary 反映最新活跃事实，无过期内容；
- 周报可在 1 分钟内调出"本周做了什么、新增了什么、下周关注什么"；
- 失效项可经 `superseded_by` 链回溯；
- 无任何文件被物理删除。

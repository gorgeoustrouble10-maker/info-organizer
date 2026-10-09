# 检索与事实更替

## 检索：用 Grep + Glob 实现"30 秒找到"

不依赖外部语义搜索工具，用 Trae 内置 Grep（基于 ripgrep）+ Glob 即可覆盖绝大多数检索需求。

### 按内容/标签检索（Grep）
```
# 按来源
Grep pattern: "source: 行政部"  path: knowledge/

# 按类型
Grep pattern: "type: 会议纪要"  path: knowledge/

# 按关联
Grep pattern: "relates_to:.*考勤"  path: knowledge/

# 按 gist 关键词
Grep pattern: "gist:.*预算"  path: knowledge/

# 正文关键词
Grep pattern: "办公用品"  path: knowledge/
```

### 按命名模式检索（Glob）
```
# 某部门某类全部文件
Glob pattern: "*_行政部_会议纪要_*"  path: knowledge/

# 某日期段
Glob pattern: "202610*_行政部_*"  path: knowledge/

# 某实体下全部卡片
Glob pattern: "**"  path: knowledge/projects/<实体>/
```

### 检索策略
- 先 **Glob 按命名** 缩小范围，再 **Grep 按内容** 精确定位，通常 2 步内找到。
- 跨实体主题检索：直接 Grep 全 `knowledge/` 按 frontmatter 字段或正文关键词。
- 检索不到时，回 `information-ledger.csv`（台账）按 gist/source 关键词查路径。

## 事实更替：不删除，只 supersede

**核心原则**：旧事实绝不物理删除，标记为更替并指向新事实，保留演进轨迹。

### 更替流程
1. 旧卡片/旧事实：`status` 改为 `superseded`，加 `superseded_by: <新卡片路径>`。
2. 新卡片：新建，`status: active`，命名版本号递增（如 `v1`→`v2`）。
3. 若是 `items.yaml` 中的原子事实：旧条目 `status: superseded` + `superseded_by`，新条目追加到末尾。

### 示例
旧卡片 `20261009_行政部_会议纪要_v1.md`：
```yaml
status: superseded
superseded_by: 20261011_行政部_会议纪要_v2.md
```
新卡片 `20261011_行政部_会议纪要_v2.md`：
```yaml
status: active
```

### 为什么不删
- 保留"当时认为是什么"的历史，便于复盘决策依据；
- `superseded_by` 链可从最旧回溯到最新，也可从最新反查演进；
- 满足点点"验收标准"中"理清来龙去脉"的要求。

## 周度复盘合并（weekly synthesis）

每周五（或月末）执行一次，见 [scenes/weekly-synthesis.md](../scenes/weekly-synthesis.md)：

1. 列出本周变更过的实体（Grep `date: <本周日期>`）。
2. 仅载入每个实体的 `status: active` 事实。
3. 用活跃事实重写 `summary.md`（覆盖式重写，summary 不保留旧版）。
4. 把本周重复出现的同类事项，合并成可复用模板（存 `templates/` 或实体下）。
5. 把本周已失效的实体/事实标 `superseded` 或移入 `archives/`。
6. 日记 `knowledge/daily/` 当周文件保留，不删。

> summary.md 是覆盖重写的唯一例外——它是"当前快照"而非历史记录，历史在 items.yaml 和卡片里。

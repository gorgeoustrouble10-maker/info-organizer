# knowledge/ — PARA 知识库

按 Tiago Forte 的 PARA 方法组织，由 `info-organizer` Skill 写入与维护。

## 结构

| 桶 | 路径 | 放什么 |
|---|---|---|
| Projects | `projects/` | 有目标/截止日的进行中工作；完成→`archives/` |
| Areas | `areas/` | 持续职责、人、部门（无截止日） |
| Resources | `resources/` | 参考材料、通用知识 |
| Archives | `archives/` | 上述三桶已失效项，保留不删 |
| Daily | `daily/` | 日记 `YYYY-MM-DD.md`，事件时间线 |

根目录：
- `MEMORY.md` — 隐性知识与操作经验（怎么干活的模式）
- `information-ledger.csv` 信息登记台账由 Skill 在 `.trae/skills/info-organizer/templates/` 维护模板，运行时复制/追加。

## 实体文件夹约定

每个实体（项目/人/主题）一个文件夹：
```
<实体>/
├── summary.md   # 快速上下文，先载入
├── items.yaml   # 原子事实，按需载入
└── <卡片>.md     # 知识卡片
```

## 命名规范

文件/卡片：`YYYYMMDD_部门_文档类型_v版本.md`

详见 `.trae/skills/info-organizer/references/para-and-naming.md`。

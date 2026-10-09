# 五维分类法（机器可读 YAML）

每条信息/每张知识卡片必须从五个维度打标签，落到 YAML frontmatter，使信息可被程序化检索与消费。

## 五维定义

| 维度 | frontmatter 字段 | 取值 | 说明 |
|---|---|---|---|
| 来源 | `source` | 自由文本：部门名/人名/渠道名 | 信息来自哪里 |
| 类型 | `type` | 枚举：制度文件 / 会议纪要 / 数据报表 / 通知公告 / 项目资料 / 其他 | 信息性质，必须落在枚举内 |
| 紧急程度 | `urgency` | 枚举：立即 / 本周 / 归档备查 | 处理时限 |
| 关联事项 | `relates_to` | 自由文本：项目名/流程名 | 与什么相关，可多值用逗号 |
| 关键信息 | `gist` | 自由文本，**≤20 字**一句话 | 核心内容浓缩 |

## 可选扩展维度（可选，缺省有默认值）

五维之外，以下3字段为可选，缺省时按默认值处理：

| 维度 | 字段 | 取值 | 默认 | 说明 |
|---|---|---|---|---|
| 优先级 | `priority` | 枚举：高 / 中 / 低 | 中 | 处理优先级 |
| 受众 | `audience` | 自由文本：接收/知会对象 | 本人 | 谁需知会 |
| 密级 | `confidential` | 枚举：公开 / 内部 / 机密 | 内部 | 保密等级 |

> 仅写入知识卡片 frontmatter，不进 information-ledger.csv 台账（避免破坏旧台账行）。需按 priority 统计时用 Grep 搜卡片 frontmatter。

## 完整 frontmatter schema

```yaml
---
title: <一句话标题>           # 必填，卡片标题
source: <来源>                # 必填
type: <枚举值或注册值>         # 必填
urgency: <枚举值>             # 必填
relates_to: <关联>            # 可多值，逗号分隔
gist: <≤20字关键信息>          # 必填
date: <YYYY-MM-DD>            # 必填，信息日期
status: active                 # 必填，active | superseded
superseded_by: <可选，新卡片路径>  # 仅 status=superseded 时填
priority: <高|中|低>          # 可选，默认 中
audience: <接收对象>          # 可选，默认 本人
confidential: <公开|内部|机密>  # 可选，默认 内部
---
```

## type 扩展注册机制

通用 type 枚举（制度文件/会议纪要/数据报表/通知公告/项目资料/其他）覆盖通用办公。垂直行业可注册自定义 type：

- 注册位置：工作区 `knowledge/MEMORY.md` 的 `## 自定义 type 注册表` 章节
- 格式：`- 行业: 值1/值2/值3`
- 打标优先用已注册值；拿不准选"其他"并在 gist 说明；确认新行业 type 时回写 MEMORY.md 注册表
- 注册后该值即纳入 type 候选枚举

## 打标规则

1. **gist 必须≤20 字**：超长说明没提炼到位，继续压缩。例：✅"Q3考勤异常5人需复核" ❌"关于第三季度考勤数据中出现异常情况的说明"。
2. **type 必须落在枚举**：拿不准选"其他"，并在 `gist` 里说明性质。
3. **relates_to 用项目/流程的标准名**：保持命名一致，便于跨卡片检索关联。
4. **urgency 按时限**：立即=当天处理；本周=本周内；归档备查=无需动作，备查。
5. **date 用信息本身的发生日期**，不是整理日期。

## 示例

### 示例1：会议通知
```yaml
---
title: 周五部门例会改为周四
source: 行政部-张主管
type: 通知公告
urgency: 本周
relates_to: 部门例会
gist: 周五例会改周四14:00会议室B
date: 2026-10-08
status: active
---
```

### 示例2：数据报表
```yaml
---
title: 9月办公用品消耗超标
source: 后勤部
type: 数据报表
urgency: 立即
relates_to: 办公用品采购,月度盘点
gist: 9月耗材超预算23%需说明
date: 2026-10-07
status: active
---
```

## 检索配合

打标后，可用 Grep 按 frontmatter 字段检索：
- 按来源：`Grep pattern: "source: 行政部"`
- 按类型：`Grep pattern: "type: 会议纪要"`
- 按关联：`Grep pattern: "relates_to:.*考勤"`
- 按 gist 关键词：`Grep pattern: "gist:.*考勤"`

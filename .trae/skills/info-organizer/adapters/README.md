# 平台适配包

`adapters/` 是平台专属适配入口——核心方法论（五维分类、PARA、三段式、事实更替）100% 复用，仅做安装/工具映射/触发机制的适配。

## 当前已支持

| 适配包 | 平台 | 安装门槛 | 能力完整度 |
|---|---|---|---|
| [trae/](trae/README.md) | Trae AI 体系 | 复制文件夹 | 完整（含 references/scenes/templates + 强触发索引自动加载 + schedule skill 定时集成） |
| [prompt-only/](prompt-only/SKILL.md) | 任何大模型（ChatGPT/Claude/豆包/Cursor/wise 等） | 粘贴 prompt 即用 | 方法论完整，无模板文件加载（Agent 依据 prompt 描述自行生成结构） |

## 待扩展（等真实用户反馈后增加）

- `claude/`：Claude Code/Desktop 平台适配
- `cursor/`：Cursor 编辑器适配
- `openai/`：OpenAI Codex/ChatGPT 适配
- `mcp/`：MCP 协议适配（将核心能力包装为 MCP tool）

> 等真实用户从某平台反馈需求后再做对应适配，避免猜测式适配。

## 适配包包含什么

每个平台适配包至少含：
- 安装/装载说明（路径、配置）
- 平台专属工具映射（如 Trae Grep/Glob ↔ 其他平台等价工具）
- 平台专属集成点（如 Trae schedule skill 的定时自动化）
- 该平台已验证能力清单

核心方法论文件（references/ scenes/ templates/）由各适配包共享引用，不重复维护。

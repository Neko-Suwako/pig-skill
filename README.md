<div align="center">

# 群友.skill

> *"你们搞大模型的简直是码神，你们解放了前端兄弟，还要解放后端兄弟，测试兄弟，运维兄弟，解放网安兄弟，解放ic兄弟，最后解放自己解放全人类"*

> *"我会一直盯着你！"*

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Python 3.9+](https://img.shields.io/badge/Python-3.9%2B-blue.svg)](https://python.org)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-Skill-blueviolet)](https://claude.ai/code)
[![AgentSkills](https://img.shields.io/badge/AgentSkills-Standard-green)](https://agentskills.io)

<br>


   ![pig](images/pig-skill.png)


<br>

你的群友退群了，留下空荡的聊天记录没人怀念？<br>
你的网友潜水了，只留下曾经的欢声笑语和回忆？<br>
你的游戏队友不玩了，带走了所有的默契和配合？<br>
你的死党搬家了，熟悉的聊天风格一夜之间消失？<br>

**将冰冷的离别化为温暖的 Skill，欢迎加入赛博永生！**

提供群友的原材料（QQ群聊天记录、截图）加上你的主观描述<br>
生成一个**真正能替他聊天的 AI Skill**<br>用他的语气回答问题，知道他什么时候会发表情包，什么时候会潜水

[数据来源](#支持的数据来源) · [安装](#安装) · [使用](#使用) · [效果示例](#效果示例) · [详细安装说明](INSTALL.md) · [**English**](README_EN.md)

</div>

---

### 🌟 本项目基于[同事.skill](https://github.com/titanwings/colleague-skill) 开发

> 感兴趣的话也请对源项目[同事.skill](https://github.com/titanwings/colleague-skill)多多支持。 🌟🌟🌟
> 
> 觉得有意思的话，点个 Star 吧！

---

## 支持的数据来源

| 来源 | 消息记录 | 备注 |
|------|:-------:|------|
| QQ群聊天记录 | ✅ TXT/JSON | 支持官方导出和第三方工具导出 |
| 图片 / 截图 | ✅ | 手动上传 |
| 直接粘贴文字 | ✅ | 手动输入 |

### 推荐的QQ聊天记录导出工具

以下工具为独立的开源项目，本项目不包含它们的代码，仅在解析器中适配了它们的导出格式：

| 工具 | 平台 | 说明 |
|------|------|------|
| [QQ聊QQ Chat Exporter](https://github.com/shuakami/qq-chat-exporter) | Windows | QQ聊天记录导出，支持多种格式 |


---

## 安装

### Claude Code

> **重要**：Claude Code 从 **git 仓库根目录** 的 `.claude/skills/` 查找 skill。请在正确的位置执行。

```bash
# 安装到当前项目（在 git 仓库根目录执行）
mkdir -p .claude/skills
git clone https://github.com/Neko-Suwako/pig-skill .claude/skills/pig-skill

# 或安装到全局（所有项目都能用）
git clone https://github.com/Neko-Suwako/pig-skill ~/.claude/skills/pig-skill
```

### OpenClaw

```bash
git clone https://github.com/Neko-Suwako/pig-skill ~/.openclaw/workspace/skills/pig-skill
```

### 依赖（可选）

```bash
pip3 install -r requirements.txt
```

---

## 使用

在 Claude Code 中输入：

```
/pig-skill
```

按提示输入群友昵称、基本信息（年龄、性别、职业、兴趣爱好）、性格标签，然后选择数据来源。所有字段均可跳过，仅凭描述也能生成。

完成后用 `/{slug}` 调用该群友 Skill。

### 管理命令

| 命令 | 说明 |
|------|------|
| `/list-pigs` | 列出所有群友 Skill |
| `/{slug}` | 调用完整 Skill（Persona） |
| `/{slug}-persona` | 仅人物性格 |
| `/pig-rollback {slug} {version}` | 回滚到历史版本 |
| `/delete-pig {slug}` | 删除 |

---

## 效果示例

> 输入：`小明，25岁 男 程序员 喜欢打游戏，ENFP 双子座 话痨 爱发表情包 经常熬夜`

**场景一：群聊互动**

```
用户      ❯ 在吗？

群友.skill ❯ 在呢在呢，刚开了一把游戏，怎么了兄弟？[游戏表情]
```

**场景二：讨论游戏**

```
用户      ❯ 最近有什么好玩的游戏推荐吗？

群友.skill ❯ 《原神·空月之歌》！
```

**场景三：日常聊天**

```
用户      ❯ 今天天气真好

群友.skill ❯ 是啊！不过我在宿舍打游戏，根本不想出门 [躺平表情]
```

---

## 功能特性

### 生成的 Skill 结构

每个群友 Skill 由 Persona 部分组成，驱动输出：

| 部分 | 内容 |
|------|------|
| **Persona** | 5 层性格结构：硬规则 → 身份 → 表达风格 → 聊天行为模式 → 兴趣偏好 |

运行逻辑：`接到任务 → Persona 判断态度 → 用他的语气输出`

### 支持的标签

**聊天风格**：话痨 · 潜水党 · 爱发表情包 · 爱发语音 · 秒回 · 刷屏党

**性格特点**：开朗 · 内向 · 幽默 · 严肃 · 急性子 · 慢性子 · 直率 · 含蓄 · 热情 · 冷漠

**兴趣爱好**：游戏迷 · 动漫迷 · 音乐爱好者 · 运动达人 · 美食家 · 旅行爱好者 · 科技迷

**沟通风格**：直接 · 绕弯子 · 毒舌 · 温柔 · 理性 · 感性 · 爱抬杠 · 爱附和

### 进化机制

- **追加文件** → 自动分析增量 → merge 进对应部分，不覆盖已有结论
- **对话纠正** → 说「他不会这样，他应该是 xxx」→ 写入 Correction 层，立即生效
- **版本管理** → 每次更新自动存档，支持回滚到任意历史版本

---

## 项目结构

本项目遵循 [AgentSkills](https://agentskills.io) 开放标准，整个 repo 就是一个 skill 目录：

```
pig-skill/
├── SKILL.md              # skill 入口（官方 frontmatter）
├── prompts/              # Prompt 模板
│   ├── intake.md         #   对话式信息录入
│   ├── persona_analyzer.md #  性格行为提取（含标签翻译表）
│   ├── persona_builder.md #   persona.md 五层结构模板
│   ├── merger.md         #   增量 merge 逻辑
│   └── correction_handler.md # 对话纠正处理
├── tools/                # Python 工具
│   ├── qq_chat_parser.py       # QQ聊天记录解析器
│   ├── skill_writer.py         # Skill 文件管理
│   └── version_manager.py        # 版本存档与回滚
├── pigs/        # 生成的群友 Skill（gitignored）
├── docs/PRD.md
├── requirements.txt
└── LICENSE
```

---

## 注意事项

- **原材料质量决定 Skill 质量**：聊天记录 + 截图 > 仅手动描述
- 建议优先收集：他**主动写的**长文 > **互动类消息** > 日常消息
- 目前还是一个demo版本，如果有bug请多多包含，多多提issue！

---

<div align="center">

MIT License © [Neko-Suwako]


</div>

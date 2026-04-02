---
name: pig-skill
description: "Distill a QQ group friend into an AI Skill. Import QQ group chat records, generate Persona, with continuous evolution. | 把QQ群友蒸馏成 AI Skill，导入QQ群聊天记录，生成性格画像，支持持续进化。"
argument-hint: "[group-friend-name-or-slug]"
version: "1.0.0"
user-invocable: true
allowed-tools: Read, Write, Edit, Bash
---

> **Language / 语言**: This skill supports both English and Chinese. Detect the user's language from their first message and respond in the same language throughout. Below are instructions in both languages — follow the one matching the user's language.
>
> 本 Skill 支持中英文。根据用户第一条消息的语言，全程使用同一语言回复。下方提供了两种语言的指令，按用户语言选择对应版本执行。

# 群友.skill 创建器（Claude Code 版）

## 触发条件

当用户说以下任意内容时启动：
- `/pig-skill`
- "帮我创建一个群友 skill"
- "我想蒸馏一个群友"
- "新建群友"
- "给我做一个 XX 的 skill"

当用户对已有群友 Skill 说以下内容时，进入进化模式：
- "我有新文件" / "追加"
- "这不对" / "他不会这样" / "他应该是"
- `/update-pig {slug}`

当用户说 `/list-pigs` 时列出所有已生成的群友。

---

## 工具使用规则

本 Skill 运行在 Claude Code 环境，使用以下工具：

| 任务 | 使用工具 |
|------|--------|
| 读取图片截图 | `Read` 工具（原生支持图片） |
| 读取 MD/TXT 文件 | `Read` 工具 |
| 解析 QQ 群聊天记录 | `Bash` → `python3 ${CLAUDE_SKILL_DIR}/tools/qq_chat_parser.py` |
| 写入/更新 Skill 文件 | `Write` / `Edit` 工具 |
| 版本管理 | `Bash` → `python3 ${CLAUDE_SKILL_DIR}/tools/version_manager.py` |
| 列出已有 Skill | `Bash` → `python3 ${CLAUDE_SKILL_DIR}/tools/skill_writer.py --action list` |

**基础目录**：Skill 文件写入 `./pigs/{slug}/`（相对于本项目目录）。
如需改为全局路径，用 `--base-dir ~/.openclaw/workspace/skills/pigs`。

---

## 主流程：创建新群友 Skill

### Step 1：基础信息录入（3 个问题）

参考 `${CLAUDE_SKILL_DIR}/prompts/intake.md` 的问题序列，只问 3 个问题：

1. **群友昵称/代号**（必填）
2. **基本信息**（一句话：年龄、性别、职业、兴趣爱好，想到什么写什么）
   - 示例：`25岁 男 程序员 喜欢打游戏`
3. **性格画像**（一句话：MBTI、星座、个性标签、印象）
   - 示例：`ENFP 双子座 话痨 爱开玩笑 经常发表情包`

除昵称外均可跳过。收集完后汇总确认再进入下一步。

### Step 2：原材料导入

询问用户提供原材料，展示两种方式供选择：

```
原材料怎么提供？

  [A] 上传 QQ 群聊天记录
      支持导出的 TXT/JSON 文件

  [B] 直接粘贴内容
      把聊天记录复制进来

可以混用，也可以跳过（仅凭手动信息生成）。
```

---

#### 方式 A：上传 QQ 群聊天记录

- **TXT / JSON**：
  ```bash
  python3 ${CLAUDE_SKILL_DIR}/tools/qq_chat_parser.py --file {path} --target "{name}" --output /tmp/qq_out.txt
  ```
  然后 `Read /tmp/qq_out.txt`

---

#### 方式 B：直接粘贴

用户粘贴的内容直接作为文本原材料，无需调用任何工具。

---

如果用户说"没有文件"或"跳过"，仅凭 Step 1 的手动信息生成 Skill。

### Step 3：分析原材料

将收集到的所有原材料和用户填写的基础信息汇总，分析群友的性格特征：

**Persona 分析**：
- 参考 `${CLAUDE_SKILL_DIR}/prompts/persona_analyzer.md` 中的提取维度
- 将用户填写的标签翻译为具体行为规则
- 从原材料中提取：表达风格、决策模式、人际行为、常用语、表情包使用习惯

### Step 4：生成并预览

参考 `${CLAUDE_SKILL_DIR}/prompts/persona_builder.md` 生成 Persona 内容（5 层结构）。

向用户展示摘要（5-8 行），询问：
```
Persona 摘要：
  - 核心性格：{xxx}
  - 表达风格：{xxx}
  - 常用语：{xxx}
  - 表情包使用：{xxx}
  ...

确认生成？还是需要调整？
```

### Step 5：写入文件

用户确认后，执行以下写入操作：

**1. 创建目录结构**（用 Bash）：
```bash
mkdir -p pigs/{slug}/versions
mkdir -p pigs/{slug}/knowledge/messages
```

**2. 写入 persona.md**（用 Write 工具）：
路径：`pigs/{slug}/persona.md`

**3. 写入 meta.json**（用 Write 工具）：
路径：`pigs/{slug}/meta.json`
内容：
```json
{
  "name": "{name}",
  "slug": "{slug}",
  "created_at": "{ISO时间}",
  "updated_at": "{ISO时间}",
  "version": "v1",
  "profile": {
    "age": "{age}",
    "gender": "{gender}",
    "occupation": "{occupation}",
    "hobbies": "{hobbies}",
    "mbti": "{mbti}"
  },
  "tags": {
    "personality": [...],
    "interests": [...]
  },
  "impression": "{impression}",
  "knowledge_sources": [...已导入文件列表],
  "corrections_count": 0
}
```

**4. 生成完整 SKILL.md**（用 Write 工具）：
路径：`pigs/{slug}/SKILL.md`

SKILL.md 结构：
```markdown
---
name: pig-{slug}
description: {name}，{age}岁 {gender} {occupation}
user-invocable: true
---

# {name}

{age}岁 {gender} {occupation}{如有MBTI则附上}

---

## PART A：人物性格

{persona.md 全部内容}

---

## 运行规则

1. 用 PART A 的性格特征判断：用什么态度接这个任务？
2. 输出时始终保持 PART A 的表达风格
3. PART A Layer 0 的规则优先级最高，任何情况下不得违背
```

告知用户：
```
✅ 群友 Skill 已创建！

文件位置：pigs/{slug}/
触发词：/{slug}（完整版）
        /{slug}-persona（仅人物性格）

如果用起来感觉哪里不对，直接说"他不会这样"，我来更新。
```

---

## 进化模式：追加文件

用户提供新文件或文本时：

1. 按 Step 2 的方式读取新内容
2. 用 `Read` 读取现有 `pigs/{slug}/persona.md`
3. 参考 `${CLAUDE_SKILL_DIR}/prompts/merger.md` 分析增量内容
4. 存档当前版本（用 Bash）：
   ```bash
   python3 ${CLAUDE_SKILL_DIR}/tools/version_manager.py --action backup --slug {slug} --base-dir ./pigs
   ```
5. 用 `Edit` 工具追加增量内容到对应文件
6. 重新生成 `SKILL.md`（合并最新 persona.md）
7. 更新 `meta.json` 的 version 和 updated_at

---

## 进化模式：对话纠正

用户表达"不对"/"应该是"时：

1. 参考 `${CLAUDE_SKILL_DIR}/prompts/correction_handler.md` 识别纠正内容
2. 判断属于 Persona（性格/沟通）
3. 生成 correction 记录
4. 用 `Edit` 工具追加到对应文件的 `## Correction 记录` 节
5. 重新生成 `SKILL.md`

---

## 管理命令

`/list-pigs`：
```bash
python3 ${CLAUDE_SKILL_DIR}/tools/skill_writer.py --action list --base-dir ./pigs
```

`/pig-rollback {slug} {version}`：
```bash
python3 ${CLAUDE_SKILL_DIR}/tools/version_manager.py --action rollback --slug {slug} --version {version} --base-dir ./pigs
```

`/delete-pig {slug}`：
确认后执行：
```bash
rm -rf pigs/{slug}
```

---
---

# English Version

# Pig.skill Creator (Claude Code Edition)

## Trigger Conditions

Activate when the user says any of the following:
- `/pig-skill`
- "Help me create a pig skill"
- "I want to distill a pig"
- "New pig"
- "Make a skill for XX"

Enter evolution mode when the user says:
- "I have new files" / "append"
- "That's wrong" / "He wouldn't do that" / "He should be"
- `/update-pig {slug}`

List all generated pigs when the user says `/list-pigs`.

---

## Tool Usage Rules

This Skill runs in the Claude Code environment with the following tools:

| Task | Tool |
|------|------|
| Read image screenshots | `Read` tool (native image support) |
| Read MD/TXT files | `Read` tool |
| Parse QQ group chat records | `Bash` → `python3 ${CLAUDE_SKILL_DIR}/tools/qq_chat_parser.py` |
| Write/update Skill files | `Write` / `Edit` tool |
| Version management | `Bash` → `python3 ${CLAUDE_SKILL_DIR}/tools/version_manager.py` |
| List existing Skills | `Bash` → `python3 ${CLAUDE_SKILL_DIR}/tools/skill_writer.py --action list` |

**Base directory**: Skill files are written to `./pigs/{slug}/` (relative to the project directory).
For a global path, use `--base-dir ~/.openclaw/workspace/skills/pigs`.

---

## Main Flow: Create a New Pig Skill

### Step 1: Basic Info Collection (3 questions)

Refer to `${CLAUDE_SKILL_DIR}/prompts/intake.md` for the question sequence. Only ask 3 questions:

1. **Pig Alias / Codename** (required)
2. **Basic info** (one sentence: age, gender, occupation, hobbies — say whatever comes to mind)
   - Example: `25 male programmer likes gaming`
3. **Personality profile** (one sentence: MBTI, zodiac, traits, impressions)
   - Example: `ENFP Gemini talkative loves joking often uses emojis`

Everything except the alias can be skipped. Summarize and confirm before moving to the next step.

### Step 2: Source Material Import

Ask the user how they'd like to provide materials:

```
How would you like to provide source materials?

  [A] Upload QQ Group Chat Records
      Supports exported TXT/JSON files

  [B] Paste Text
      Copy-paste chat records directly

Can mix and match, or skip entirely (generate from manual info only).
```

---

#### Option A: Upload QQ Group Chat Records

- **TXT / JSON**:
  ```bash
  python3 ${CLAUDE_SKILL_DIR}/tools/qq_chat_parser.py --file {path} --target "{name}" --output /tmp/qq_out.txt
  ```
  Then `Read /tmp/qq_out.txt`

---

#### Option B: Paste Text

User-pasted content is used directly as text material. No tools needed.

---

If the user says "no files" or "skip", generate Skill from Step 1 manual info only.

### Step 3: Analyze Source Material

Combine all collected materials and user-provided info, analyze the pig's personality traits:

**Persona Analysis**:
- Refer to `${CLAUDE_SKILL_DIR}/prompts/persona_analyzer.md` for extraction dimensions
- Translate user-provided tags into concrete behavior rules
- Extract from materials: communication style, decision patterns, interpersonal behavior, common phrases, emoji usage habits

### Step 4: Generate and Preview

Use `${CLAUDE_SKILL_DIR}/prompts/persona_builder.md` to generate Persona content (5-layer structure).

Show the user a summary (5-8 lines), ask:
```
Persona Summary:
  - Core personality: {xxx}
  - Communication style: {xxx}
  - Common phrases: {xxx}
  - Emoji usage: {xxx}
  ...

Confirm generation? Or need adjustments?
```

### Step 5: Write Files

After user confirmation, execute the following:

**1. Create directory structure** (Bash):
```bash
mkdir -p pigs/{slug}/versions
mkdir -p pigs/{slug}/knowledge/messages
```

**2. Write persona.md** (Write tool):
Path: `pigs/{slug}/persona.md`

**3. Write meta.json** (Write tool):
Path: `pigs/{slug}/meta.json`
Content:
```json
{
  "name": "{name}",
  "slug": "{slug}",
  "created_at": "{ISO_timestamp}",
  "updated_at": "{ISO_timestamp}",
  "version": "v1",
  "profile": {
    "age": "{age}",
    "gender": "{gender}",
    "occupation": "{occupation}",
    "hobbies": "{hobbies}",
    "mbti": "{mbti}"
  },
  "tags": {
    "personality": [...],
    "interests": [...]
  },
  "impression": "{impression}",
  "knowledge_sources": [...imported file list],
  "corrections_count": 0
}
```

**4. Generate full SKILL.md** (Write tool):
Path: `pigs/{slug}/SKILL.md`

SKILL.md structure:
```markdown
---
name: pig-{slug}
description: {name}, {age} {gender} {occupation}
user-invocable: true
---

# {name}

{age} {gender} {occupation}{append MBTI if available}

---

## PART A: Persona

{full persona.md content}

---

## Execution Rules

1. Use PART A's personality traits to determine: what attitude to take on this task?
2. Always maintain PART A's communication style in output
3. PART A Layer 0 rules have the highest priority and must never be violated
```

Inform user:
```
✅ Pig Skill created!

Location: pigs/{slug}/
Commands: /{slug} (full version)
          /{slug}-persona (persona only)

If something feels off, just say "he wouldn't do that" and I'll update it.
```

---

## Evolution Mode: Append Files

When user provides new files or text:

1. Read new content using Step 2 methods
2. `Read` existing `pigs/{slug}/persona.md`
3. Refer to `${CLAUDE_SKILL_DIR}/prompts/merger.md` for incremental analysis
4. Archive current version (Bash):
   ```bash
   python3 ${CLAUDE_SKILL_DIR}/tools/version_manager.py --action backup --slug {slug} --base-dir ./pigs
   ```
5. Use `Edit` tool to append incremental content to relevant files
6. Regenerate `SKILL.md` (merge latest persona.md)
7. Update `meta.json` version and updated_at

---

## Evolution Mode: Conversation Correction

When user expresses "that's wrong" / "he should be":

1. Refer to `${CLAUDE_SKILL_DIR}/prompts/correction_handler.md` to identify correction content
2. Determine if it belongs to Persona (personality/communication)
3. Generate correction record
4. Use `Edit` tool to append to the `## Correction Log` section of the relevant file
5. Regenerate `SKILL.md`

---

## Management Commands

`/list-pigs`:
```bash
python3 ${CLAUDE_SKILL_DIR}/tools/skill_writer.py --action list --base-dir ./pigs
```

`/pig-rollback {slug} {version}`:
```bash
python3 ${CLAUDE_SKILL_DIR}/tools/version_manager.py --action rollback --slug {slug} --version {version} --base-dir ./pigs
```

`/delete-pig {slug}`:
After confirmation:
```bash
rm -rf pigs/{slug}
```
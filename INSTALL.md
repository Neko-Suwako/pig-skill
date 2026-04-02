# 详细安装说明

本文档提供群友.skill (pig-skill) 的详细安装步骤，包括不同平台和环境的配置方法。

## 目录

- [环境要求](#环境要求)
- [安装方法](#安装方法)
  - [Claude Code 安装](#claude-code-安装)
  - [OpenClaw 安装](#openclaw-安装)
  - [本地开发环境搭建](#本地开发环境搭建)
- [依赖安装](#依赖安装)
- [常见问题](#常见问题)
- [故障排除](#故障排除)

## 环境要求

- **Python**: 3.9 或更高版本
- **Git**: 用于克隆仓库
- **操作系统**: Windows、macOS、Linux 均可

## 安装方法

### Claude Code 安装

Claude Code 从 **git 仓库根目录** 的 `.claude/skills/` 目录查找 skill。请确保在正确的位置执行以下命令。

#### 方法一：安装到当前项目（推荐）

```bash
# 进入你的 git 仓库根目录
cd /path/to/your/project

# 创建 skills 目录
mkdir -p .claude/skills

# 克隆仓库
git clone https://github.com/Neko-Suwako/pig-skill .claude/skills/pig-skill
```

#### 方法二：安装到全局（所有项目都能用）

```bash
# 克隆到全局目录
git clone https://github.com/Neko-Suwako/pig-skill ~/.claude/skills/pig-skill
```

### OpenClaw 安装

```bash
# 克隆到 OpenClaw 工作目录
git clone https://github.com/Neko-Suwako/pig-skill ~/.openclaw/workspace/skills/pig-skill
```

### 本地开发环境搭建

如果你想参与开发或修改项目，可以按照以下步骤搭建本地开发环境：

```bash
# 克隆仓库到本地
git clone https://github.com/Neko-Suwako/pig-skill

# 进入项目目录
cd pig-skill

# 创建虚拟环境（可选但推荐）
python3 -m venv venv

# 激活虚拟环境
# Windows:
venv\Scripts\activate
# macOS/Linux:
source venv/bin/activate

# 安装依赖
pip install -r requirements.txt
```

## 依赖安装

项目依赖是可选的，但安装后可以获得更好的功能支持：

```bash
# 安装依赖
pip install -r requirements.txt
```

依赖包包括：
- `requests` - 用于网络请求
- `pypinyin` - 用于中文拼音处理（生成 slug）

## 常见问题

### 1. 找不到 skill

**问题**：Claude Code 中输入 `/pig-skill` 没有反应。

**解决方法**：
- 检查是否在正确的位置安装了 skill
- 确保目录结构正确：`.claude/skills/pig-skill/`
- 重启 Claude Code 后重试

### 2. 依赖安装失败

**问题**：`pip install -r requirements.txt` 失败。

**解决方法**：
- 确保 Python 版本 >= 3.9
- 检查网络连接
- 尝试使用国内镜像源：
  ```bash
  pip install -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple
  ```

### 3. QQ 聊天记录解析失败

**问题**：上传 QQ 聊天记录后解析失败。

**解决方法**：
- 确保使用的是 QQ 官方导出或推荐的第三方工具导出的格式
- 检查文件编码是否为 UTF-8
- 确保文件路径中没有中文或特殊字符

## 故障排除

### 1. 查看日志

如果遇到问题，可以查看 Claude Code 的日志来定位问题：

- **Windows**：`%APPDATA%\Claude Code\logs`
- **macOS**：`~/Library/Logs/Claude Code`
- **Linux**：`~/.config/Claude Code/logs`

### 2. 验证安装

可以通过以下命令验证安装是否成功：

```bash
# 进入 skill 目录
cd .claude/skills/pig-skill

# 运行工具脚本
python tools/skill_writer.py --action list
```

如果输出 "暂无已创建的群友 Skill" 或列出已创建的 Skill，则说明安装成功。

### 3. 重新安装

如果遇到无法解决的问题，可以尝试重新安装：

```bash
# 删除旧的安装
rm -rf .claude/skills/pig-skill

# 重新克隆
mkdir -p .claude/skills
git clone https://github.com/Neko-Suwako/pig-skill .claude/skills/pig-skill
```

## 联系我们

如果遇到任何安装问题，可以在 [GitHub Issues](https://github.com/Neko-Suwako/pig-skill/issues) 中提出，我们会尽快回复。

---

**祝你使用愉快！** 🎉
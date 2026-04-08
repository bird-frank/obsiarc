# Obsiarc Skills

> Obsidian + AI 的 Skill 集合，为 Claude Code、OpenCode、OpenClaw 等 AI 编码环境提供 Obsidian 相关的增强能力。

## 包含的 Skills

| Skill | 描述 |
|-------|------|
| **concept-card-extractor** | 从文档中提取关键概念和技术术语，并生成或补充 Obsidian 原子概念卡片（Zettelkasten 风格）。 |

## 安装说明

### 克隆仓库

```bash
git clone https://github.com/bird-frank/obsiarc.git
cd obsiarc
```

### Claude Code

Claude Code 支持从项目本地或全局 `skills` 目录加载自定义 Skill。

#### 方式一：项目本地安装（推荐）

在目标项目的根目录下创建本地 skills 目录，并复制所需 skill：

```bash
# 进入你的 Obsidian vault 或任意项目目录
cd /path/to/your/project

# 复制 skill 到项目本地
mkdir -p .claude/skills
cp -r /path/to/obsiarc/skills/concept-card-extractor .claude/skills/
```

#### 方式二：全局安装

复制到 Claude Code 全局 skills 目录：

```bash
# macOS / Linux
mkdir -p ~/.claude/skills
cp -r /path/to/obsiarc/skills/concept-card-extractor ~/.claude/skills/
```

> 安装后，Claude Code 会在对话中自动识别并调用对应的 skill。

---

### OpenCode

OpenCode 支持从工作区 `.opencode/skills` 目录或环境变量配置的全局目录加载 skills。

#### 方式一：工作区本地安装（推荐）

```bash
cd /path/to/your/project

mkdir -p .opencode/skills
cp -r /path/to/obsiarc/skills/concept-card-extractor .opencode/skills/
```

#### 方式二：全局安装

```bash
# macOS / Linux
mkdir -p ~/.opencode/skills
cp -r /path/to/obsiarc/skills/concept-card-extractor ~/.opencode/skills/
```

> 如果你配置了 `OPENCODE_SKILLS_PATH`，也可以将 skill 复制到该路径下。

---

### OpenClaw

OpenClaw 的 skill 加载机制与上述平台保持一致。将 skill 放置到对应路径即可：

#### 方式一：项目本地安装

```bash
cd /path/to/your/project

mkdir -p .openclaw/skills
cp -r /path/to/obsiarc/skills/concept-card-extractor .openclaw/skills/
```

#### 方式二：全局安装

```bash
# macOS / Linux
mkdir -p ~/.openclaw/skills
cp -r /path/to/obsiarc/skills/concept-card-extractor ~/.openclaw/skills/
```

## 目录结构

```
obsiarc/
├── LICENSE
├── README.md
└── skills/
    └── concept-card-extractor/
        ├── SKILL.md
        └── evals/
            └── evals.json
```

## License

MIT

# make-knowledge-cards

将粘贴文章或本地 Markdown/TXT 文件整理成知识卡片的 Codex Skill。

## 解决什么问题

文章通常包含重复表述、背景和例子，直接用于复习时不易抓住重点。本项目帮助提取真正重要的知识，整理成便于理解和自测的卡片，同时保留原文依据，方便回查。

这是由智能体执行的 Skill 指令集，不是独立运行的转换程序。

## 主要功能

- **支持正文与本地文件**：接受粘贴文章，以及可读取的 `.md`、`.markdown`、`.txt` 文件。
- **提炼与去重**：通常生成 5～8 张卡片，每张只讲一个知识点；知识不足时少于 5 张，没有可提炼内容时可输出 0 张，不强行凑数。
- **统一卡片结构**：标题、核心知识、简明解释，以及原文例子或自测问题；自测问题附参考答案。
- **忠于原文**：不引入外部事实或编造例子，保留数字、适用条件、不确定性及作者观点归属，并附来源定位。
- **Markdown 输出**：默认中文，可按用户要求改用其他语言。多篇文章默认分别处理，明确要求合并时才跨文去重。

暂不支持网页抓取、PDF、Anki 导出或图形界面。仅有链接或 PDF 时，需要先提供正文或支持的文本文件。

## 安装方法

需要能够加载本地 Skill 的 Codex 环境；处理本地文件时，Codex 还需有读取该文件的权限。Skill 本身没有额外脚本或第三方库依赖，也不要求单独配置 API 密钥；模型访问由 Codex 环境提供。

按 [Codex 官方 Skill 文档](https://developers.openai.com/codex/skills/)，可安装到用户级目录 `~/.agents/skills/`。在本项目根目录执行以下 macOS/Linux 命令（目标位置尚未存在同名 Skill 时）：

```sh
mkdir -p ~/.agents/skills
cp -R skills/make-knowledge-cards ~/.agents/skills/
```

也可以手动复制整个 `skills/make-knowledge-cards` 文件夹到该目录。安装后应保留以下结构：

```text
~/.agents/skills/make-knowledge-cards/
├── SKILL.md
└── agents/
    └── openai.yaml
```

若已有同名 Skill，先检查已有版本，避免直接混合或覆盖文件。Codex 会自动发现 Skill；未出现时重启 Codex。

本项目的 `skills/` 是源文件存放位置，不是默认的 `.agents/skills/` 发现目录。尚未安装时，也可以在当前项目对话中明确要求 Codex 读取 `skills/make-knowledge-cards/SKILL.md` 并按其执行。

## 使用方法

**粘贴文章**：安装后在对话中明确调用 Skill，并提供正文。

```text
请使用 $make-knowledge-cards 把下面文章整理成知识卡片：
……粘贴文章正文……
```

**处理本地文件**：提供相对于当前工作目录的路径，或文件的绝对路径。可先使用项目自带测试文章：

```text
请使用 $make-knowledge-cards 处理 ./tests/fixtures/cache.md
```

**合并多篇文章**：明确说明合并要求，否则默认逐篇生成卡片。

```text
请使用 $make-knowledge-cards 合并处理 ./notes/a.md 和 ./notes/b.txt，
跨文章去重，并在每张卡片标明来源文章。
```

结果以 Markdown 文本返回。文件缺失、不可读或乱码时，Skill 会说明问题并请求可读正文。

## 输入输出示例

输入：

```text
主动回忆指不看材料，尝试从记忆中提取内容。复习时可以先合上书，
写下刚学过的要点，再对照原文检查遗漏。
```

输出：

```markdown
主题：主动回忆。共 1 张卡片：原文只支持一个独立知识点，不强行凑数。

### 卡片 1：用主动回忆复习
- **核心知识**：主动回忆是在不看材料时尝试提取记忆中的内容。
- **简明解释**：复习时先回忆，再对照原文检查遗漏。
- **例子**：合上书，写下刚学过的要点，再对照原文检查遗漏。
- **来源**：「主动回忆指不看材料，尝试从记忆中提取内容。」
```

## 本地验证

使用官方 `skill-creator` 的 `init_skill.py` 初始化；行为测试的输入、输出及检查结论见 [tests/cases.md](tests/cases.md)。结构验证命令（将路径替换为本机官方工具目录）：

```sh
python3 /path/to/skill-creator/scripts/quick_validate.py skills/make-knowledge-cards
```

验证器需要 Python 和 PyYAML；可在临时虚拟环境中安装 PyYAML，不影响 Skill 使用。本次验证结果为 `Skill is valid!`。结构验证不代表内容质量保证，仍需逐卡核对原文依据。

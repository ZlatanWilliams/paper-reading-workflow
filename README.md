# Paper Reading Workflow v0.1.1

这是一个基于 Codex App 对话交互的论文阅读工作流。

它不把论文 PDF 保存到本地工作流仓库，也不在仓库中创建每篇论文的文件夹。你在对话框中上传论文并持续提问，阅读结束后，Codex 将本次对话整理成一份结构化 Markdown 笔记，保存到你配置的 Obsidian 知识库目录。

## 工作方式

```text
Codex App 对话
    │
    ├── 临时读取论文 PDF
    ├── 按章节交互提问
    ├── 记录论文内容、问题、解答和理解修正
    │
    └── “整理并保存本次阅读”
             │
             ▼
Obsidian/raw/paper-reading-notes/<年份>-<作者>-<短标题>-<阅读日期>.md
```

仓库中的主要文件：

```text
AGENTS.md                                      # 项目级规则
.agents/skills/paper-reading/SKILL.md          # Codex 论文阅读 Skill
.agents/skills/paper-reading/references/       # 证据与方法评价标准
templates/paper-reading-note.md                # 单文件笔记结构
.codex/paper-reading.local.example.md          # 本地输出配置示例
```

## 首次配置

复制配置示例：

```text
.codex/paper-reading.local.example.md
→ .codex/paper-reading.local.md
```

然后在本地配置中填写 Obsidian 输出目录：

```text
OUTPUT_DIR=<YOUR_OBSIDIAN_NOTES_DIRECTORY>
FILENAME_TEMPLATE={year}-{author}-{short_title}-{reading_date}.md
```

实际的 `.codex/paper-reading.local.md` 不会提交到 GitHub。没有本地配置时，Skill 不会写入未知目录，而会先提示你配置输出路径。

## 开始一次阅读

### 1. 打开项目

在 Codex App 中打开当前仓库作为 Local Project：

```text
<PATH_TO_PAPER_READING_WORKFLOW>
```

### 2. 上传论文即可开始

在新聊天中直接上传 PDF，不需要输入 `$paper-reading` 或其他固定启动 Prompt。

上传后，Skill 会自动：

- 识别论文标题、作者、年份和研究领域；
- 建立摘要、Introduction、Related Work、Method、Experiments、Discussion 等结构地图；
- 从 Introduction 或论文第一个实质性章节开始第一轮讲解；
- 询问你的研究背景，但不会因为你暂时没有填写而阻塞阅读；
- 记录后续问题、解答和理解修正；
- 不创建论文文件夹、不复制 PDF、不保存中间 Markdown。

你可以直接继续提问，例如：

```text
我目前主要研究推荐系统，请从这个背景出发解释这篇论文的贡献。
```

如果你没有额外说明，Codex 会按“结构化第一轮阅读”开始。

### 3. 继续分阶段提问

可以直接提出自然语言问题，例如：

```text
我不理解 Introduction 中作者为什么认为已有方法不能解决这个问题，请结合原文解释。
```

```text
请详细解释 Method 中这个公式每一项的含义，并说明它和论文研究问题的关系。
```

```text
这个实验是否真的证明了作者的核心结论？请检查 baseline、指标和 ablation。
```

Skill 会把这些问题和回答整理到对应的 `Introduction`、`Method` 或 `Experiments and Results` 章节，而不是把所有问答堆在笔记末尾。只有在你明确要求保存时，才会写入 Obsidian。

### 4. 结束并保存

阅读结束时发送：

```text
整理并保存本次阅读。
```

Codex 会检查论文材料、读取本地输出配置、生成文件名、检查重名，并将一份完整 Markdown 写入 Obsidian。目标文件已存在时，Codex 会先询问你是否覆盖、另存或取消。

## 笔记内容

最终文件包含：

- 论文元信息
- 核心结论
- Introduction、Related Work、Method、Experiments、Discussion 等章节梳理
- 对应章节中的用户问题与模型解答
- 用户理解或修正
- 证据位置
- 对当前研究的启发
- 未解决问题
- 最终总结

问答是经过整理的章节化内容，不是完整逐字聊天转录。

## 重要边界

- 当前对话是临时阅读空间；新开聊天后，需要重新上传论文或提供项目中可访问的来源。
- 只有最终 Markdown 笔记写入 Obsidian；PDF 不写入工作流仓库。
- 目标目录不存在时，不会静默改用其他路径或自动改变配置。
- 不得编造论文页码、实验结果、引用或用户没有提出过的问题。
- 本仓库只用于开发和备份工作流，不用于存放受版权保护的论文全文。

## SESSION_REVIEW 与持续优化

每次论文阅读会话结束后，可以在仓库根目录生成一个 `SESSION_REVIEW-YYYY-MM-DD-论文短名.md`，记录本次会话中的沟通问题、阅读重点偏差、证据边界、保存与格式问题，以及后续改进建议。同一天多次会话时，在文件名末尾增加 `-01`、`-02` 等后缀。

复盘文档不是自动变更指令。处理时必须先完整理解问题，区分事实、诊断、建议和论文特定问题，再判断它是否是具有通用性的工作流缺陷。只有经过评估后确实值得修改的问题，才进入 [WORKFLOW_OPTIMIZATION_PRIORITY.md](WORKFLOW_OPTIMIZATION_PRIORITY.md)，并按该文档从高到低的顺序迭代。每次迭代都记录采纳、暂缓、拒绝或不纳入的理由。

可直接复制 [templates/session-review.md](templates/session-review.md) 作为复盘模板。`HANDOFF-论文短名-YYYY-MM-DD.md` 用于会话交接，与复盘文档用途不同。两类文件均为本地会话产物，默认不会提交或同步到远程仓库。

## 版本与发布权限

版本号、Git 标签、提交和远程推送都属于受控操作。除非用户在当前请求中明确授权，否则不得擅自修改版本号、创建或推送标签、提交或推送发布。小型文档修复默认只保留为本地变更。

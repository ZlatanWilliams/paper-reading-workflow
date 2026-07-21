# Paper Reading Workflow v0.1.0

这是一个基于 Codex App 对话交互的论文阅读工作流。

它不把论文 PDF 保存到当前 C 盘仓库，也不在仓库中创建每篇论文的文件夹。你在对话框中上传论文并持续提问，阅读结束后，Codex 将本次对话整理成一份结构化 Markdown 笔记，保存到本机 Obsidian 知识库。

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
OUTPUT_DIR=D:\Personal Data\obsidian\zzy-kb\raw\paper-reading-notes
FILENAME_TEMPLATE={year}-{author}-{short_title}-{reading_date}.md
```

实际的 `.codex/paper-reading.local.md` 不会提交到 GitHub。没有本地配置时，Skill 不会写入未知目录，而会先提示你配置输出路径。

## 开始一次阅读

### 1. 打开项目

在 Codex App 中打开当前仓库作为 Local Project：

```text
C:\Users\zhaoy\Documents\论文阅读 2
```

### 2. 上传论文并启动交互

在新聊天中上传 PDF，然后发送：

```text
请使用 $paper-reading 开始交互式阅读这篇论文。

我的研究背景：
<填写你的研究方向、当前问题和已有基础>

请先完成论文身份确认和整体结构定位，然后从 Introduction 开始。
阅读过程中：
- 按论文结构组织内容；
- 记录我提出的问题及你的回答；
- 把我的理解和后续修正放回对应章节；
- 关键结论标注页码、章节、图、表或附录；
- 不创建论文文件夹，不复制 PDF，不保存中间 Markdown。

在我说“整理并保存本次阅读”之前，不要写入 Obsidian。
```

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

Skill 会把这些问题和回答整理到对应的 `Introduction`、`Method` 或 `Experiments and Results` 章节，而不是把所有问答堆在笔记末尾。

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
- 只有最终 Markdown 笔记写入 Obsidian；PDF 不写入 C 盘仓库。
- 目标目录不存在时，不会静默改用其他路径或自动改变配置。
- 不得编造论文页码、实验结果、引用或用户没有提出过的问题。
- 本仓库只用于开发和备份工作流，不用于存放受版权保护的论文全文。

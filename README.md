# Paper Reading Workflow

这是一个基于 Codex 的论文阅读工作流 V0。它把论文阅读拆成可重复的阶段，并将结论、证据、方法分析和批评保存为 Markdown 文件，方便以后检索和跨论文比较。

## 目录说明

```text
AGENTS.md                              # 项目级长期规则
.agents/skills/paper-reading/          # Codex 可复用的论文阅读 Skill
templates/                             # 单篇论文笔记模板
papers/<paper-id>/                     # 每篇论文的原文与分析结果
synthesis/                              # 跨论文比较与研究空白
```

## 从第一篇论文开始

### 1. 创建论文目录

给论文一个稳定、简短的 ID，例如 `作者-年份-关键词`：

```text
papers/author-2026-keyword/
└── paper.pdf
```

不要修改原始 PDF。论文 PDF 可能受版权保护；公开 GitHub 仓库建议只备份工作流和自己的阅读笔记，不要把未经许可的论文全文推送到远程仓库。

### 2. 启动 Codex 任务

在当前工作区中发送：

```text
请使用 $paper-reading 分析这篇论文，并把结果保存到对应的 Markdown 文件。

Paper: papers/author-2026-keyword/paper.pdf
Research context: <我的研究方向、当前问题和已有基础>
Task: 对这篇论文进行首次结构化阅读。
重点关注：
- 它解决了什么问题？
- 核心贡献是什么？
- 方法的输入、输出和关键假设是什么？
- 主要证据位于哪些页码、章节、图或表？
- 哪些地方需要我人工核对？

请先更新 metadata.md 和 summary.md，不要直接进行最终批判。
```

### 3. 按阶段深入

首次阅读后，建议分开执行以下任务：

```text
请使用 $paper-reading 深入复原这篇论文的方法。
重点分析输入、输出、假设、算法步骤、目标函数、训练/实验流程、baseline 和指标。
更新 papers/author-2026-keyword/method.md。
严格区分论文明确说明、根据论文推断和仍需核对的内容。
```

```text
请使用 $paper-reading 审查这篇论文的实验和证据。
重点检查数据集、baseline、公平性、指标、ablation、统计分析、可复现性和外部有效性。
更新 papers/author-2026-keyword/evidence.md 和 critique.md。
每个重要判断标注页码、章节、图或表；证据不足时明确写出。
```

```text
请使用 $paper-reading 结合我的研究背景，分析这篇论文对我的研究有什么价值。
区分可以直接迁移、需要条件才能迁移和不适合迁移的思想。
更新 papers/author-2026-keyword/questions.md。
```

## 推荐阅读顺序

1. `metadata.md`：确认论文身份、研究问题和自己的阅读目标。
2. `summary.md`：建立整体结构和一页式理解。
3. `method.md`：复原方法与假设。
4. `evidence.md`：核对实验结果和论据。
5. `critique.md`：评价方法有效性、实验设计和可迁移性。
6. `questions.md`：记录未解决问题、后续论文和研究方向。

不要一开始就要求 Codex “总结并批判整篇论文”。先忠实复述，再分析方法，最后进行批判性评价，通常更容易发现证据缺口。

## 证据规则

所有重要结论都应尽量关联来源位置，并区分：

- **Paper states**：论文明确写出的内容。
- **Inference**：基于论文内容做出的推断。
- **Evaluation**：对有效性、重要性或可迁移性的评价。

不得编造页码、实验结果、引用、作者意图或论文没有提供的实现细节。无法读取的公式、图表或补充材料要标记为“需要人工核对”。

## 跨论文综合

当至少积累两篇论文后，可以创建或编辑 `synthesis/comparison-matrix.md`，然后使用：

```text
请使用 $paper-reading 比较 papers/ 下的相关论文。
从问题定义、方法路线、假设、数据、baseline、指标、证据强度和局限性几个维度建立比较表。
不要把不同论文的指标直接横向比较，除非实验条件确实可比。
最后总结一致结论、矛盾证据和可能的研究空白。
```

## V0 的边界

V0 先解决三个问题：

1. 每篇论文有固定的阅读产物。
2. 关键判断能够回溯到论文证据。
3. 阅读结果可以用于跨论文比较。

后续可以再加入 PDF 自动抽取、文献管理器连接、跨论文检索和自动化检查。

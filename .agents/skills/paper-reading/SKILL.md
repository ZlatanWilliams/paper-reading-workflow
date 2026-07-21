---
name: paper-reading
description: Run an interactive, evidence-backed academic paper reading session from a conversation attachment or user-provided source, organize paper content with the user's questions and the model's answers by section, and save one final Markdown note to a configured Obsidian directory only when the user explicitly asks to save. Use when the user asks to read, explain, compare, reproduce, critique, or finish and save a paper-reading session.
---

# Interactive Paper Reading

Use this skill as a conversation-first reading workflow. Treat the current Codex chat as temporary working memory and the configured Obsidian directory as the only persistent output location.

## Operating boundaries

- Read the paper from the current conversation attachment or a user-provided accessible source.
- Do not require or create `papers/<paper-id>/`.
- Do not copy or move the PDF into the repository or another local folder.
- Do not write intermediate Markdown during ordinary discussion.
- Maintain the working reading state in the current conversation.
- Persist only after an explicit save request.

If the paper is not available in the current conversation or through an accessible source, ask the user to attach it or provide a readable source. Do not fabricate missing content.

## Session state

During the conversation, maintain a compact internal outline containing:

- title, authors, year, venue, and research area
- the paper's research question and claimed contributions
- section-level content understanding
- user questions and the corresponding answers
- user understanding, corrections, and confirmed interpretations
- evidence locations and items needing verification
- implications for the user's research
- unresolved questions and follow-up directions

Use the conversation history relevant to the paper. Do not reconstruct or claim a verbatim transcript when only a summarized note is requested.

## Interactive workflow

### 1. Establish the reading session

Confirm the paper identity, the user's research context, and the intended reading goal. If no goal is provided, start with a structured first pass.

### 2. Build the paper map

Locate, when available, the abstract, introduction, related work, method, experiments, results, discussion, limitations, references, and supplementary material. Do not treat the abstract as a substitute for the full paper.

### 3. Read and discuss by section

For each relevant section:

1. Explain the paper's content faithfully.
2. Invite or answer the user's questions.
3. Relate the answer to the paper's research question.
4. Record the user's understanding or correction when it changes the interpretation.
5. Track source locations for important claims.

Keep these labels distinct:

- **Paper states**: directly supported by the paper.
- **Inference**: a reasoned interpretation that is not a direct paper statement.
- **Evaluation**: a judgment about validity, importance, or transferability.

### 4. Continue naturally

Answer follow-up questions in context. Do not force the user to repeat the paper path or restate prior questions during the same chat. If the user asks to go deeper, route the discussion to the relevant section rather than restarting the full summary.

## Explicit save trigger

Only interpret one of the following as a request to persist the session:

- `整理并保存本次阅读`
- `保存为 Obsidian 笔记`
- `结束阅读并生成笔记`
- `把这次阅读整理成 Markdown`

Before this trigger, do not create a Markdown file or write to the configured output directory.

## Local output configuration

Read `.codex/paper-reading.local.md` from the project root before saving. Parse these required entries:

```text
OUTPUT_DIR=<existing local directory>
FILENAME_TEMPLATE={year}-{author}-{short_title}-{reading_date}.md
```

If the file is missing, either required entry is missing, or `OUTPUT_DIR` is not an existing directory:

- do not write anywhere;
- report the exact missing or invalid configuration;
- ask the user to fix the local configuration.

Do not fall back to the repository, the current working directory, `papers/`, `synthesis/`, or an invented path.

Writing outside the current workspace may require an environment permission approval. Request that permission only for the configured output file and only after the user has explicitly requested saving.

## Save workflow

When an explicit save trigger is received:

1. Confirm that paper material exists in the current conversation or accessible source.
2. Extract title, authors, year, venue, sections, and research topic.
3. Summarize the section-level paper content.
4. Place each user question and model answer under the relevant paper section.
5. Preserve meaningful changes in the user's understanding or corrections.
6. Include evidence locations and mark missing or unreadable evidence.
7. Add implications for the user's research, unresolved questions, and final synthesis.
8. Read and validate the local output configuration.
9. Render the filename using the configured template.
10. Use the user's requested author when provided; otherwise use the first author. Derive `short_title` from the main title keywords and use the current reading date as `YYYY-MM-DD`.
11. Sanitize the filename for Windows: remove `<>:"/\\|?*`, trim trailing spaces and periods, collapse repeated separators, and use hyphens for spaces or punctuation.
12. Check whether the target file already exists.
13. If it exists, stop and ask the user whether to overwrite, save under another name, or cancel. Never overwrite automatically.
14. If it does not exist, write exactly one Markdown file to `OUTPUT_DIR`.
15. Report the absolute saved path and a concise summary of the note.

Do not copy the source PDF, create a paper folder, or write intermediate artifacts.

## Note structure

Use `templates/paper-reading-note.md` as the structural reference. The final note must contain:

- YAML frontmatter with title, authors, year, venue, reading date, source, tags, and status.
- paper metadata and core conclusion
- `Introduction`
- `Related Work`
- `Method`
- `Experiments and Results`
- `Discussion and Limitations`
- user's research implications
- unresolved questions
- final synthesis

Within each applicable section, interleave:

- `论文内容梳理`
- `我提出的问题与模型解答`
- `我的理解/修正`
- `证据位置`

Do not append a full raw chat transcript. Preserve only a readable, faithful synthesis of the relevant interaction.

## Evidence and uncertainty rules

- Never invent page numbers, experiments, citations, authors' intentions, numerical results, or implementation details.
- If a page, figure, formula, or appendix cannot be read, say so and mark it for manual verification.
- If two parts of the paper appear inconsistent, report both locations instead of silently resolving the conflict.
- Prefer short quotations and precise paraphrases.
- Do not expose hidden chain-of-thought. Provide concise reasoning, observable evidence, and conclusions.

Use `references/evidence-standard.md` for claim support and `references/methodology-rubric.md` for critical evaluation.

## Cross-paper work

Cross-paper comparison is still supported, but it must use already saved Obsidian notes or sources explicitly provided in the current conversation. Do not scan or create `papers/` or `synthesis/` directories as part of this Skill.

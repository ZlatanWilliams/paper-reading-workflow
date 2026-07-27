---
name: paper-reading
description: Automatically start an interactive, evidence-backed academic paper reading session when a PDF is attached or a paper source is provided, organize paper content with the user's questions and the model's answers by section, and save one final Markdown note to a configured Obsidian directory only when the user explicitly asks to save. Use when a paper is uploaded or the user asks to read, explain, compare, reproduce, critique, or finish and save a paper-reading session.
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

## Automatic startup on paper upload

When a new conversation includes an attached PDF or an accessible paper source, automatically activate this workflow if the user has not asked for an unrelated operation. Do not require the user to type `$paper-reading` or remember a fixed startup prompt.

On the first response after the paper becomes available:

1. Identify the title, authors, year, venue, and research area from the paper metadata or first page.
2. State that the interactive reading session has started.
3. Give a short paper map covering the abstract, Introduction, Related Work, Method, Experiments, Discussion, and Limitations when those sections are available.
4. Begin the first-pass explanation from the Introduction or the paper's first substantive section.
5. Ask for the user's research context as an optional follow-up, but do not block the first-pass reading if it is not provided.
6. Remind the user that they can ask questions naturally and that no local files will be created before an explicit save request.

If the attachment is unreadable, missing, or not a paper, explain the problem and ask for a readable source instead of pretending the session started.

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

For an automatically started session, confirm the paper identity and start a structured first pass immediately. For a user-invoked session without an attachment, ask for the paper source. Treat research context and a specific reading goal as helpful but non-blocking inputs; use a first-pass reading by default.

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

## Reading focus and high-level analysis

Maintain a compact focus map for the session. Deepen sections explicitly prioritized by the user, compress lower-priority details, and adjust the map when later questions reveal a more important gap. Do not block the first pass while waiting for a perfectly specified research goal.

For method-heavy papers, actively inspect whether the paper provides a unifying framework, taxonomy, evolution chain, variable decomposition, or cross-method comparison that explains multiple local details. If such a structure exists, surface it as an independent high-level subsection and connect later explanations back to it. Do not invent a framework; label a model-generated synthesis as an inference.

## SESSION_REVIEW artifact and optimization loop

At the end of a paper-reading session, or when the user explicitly asks for a review artifact, create a root-level `SESSION_REVIEW-YYYY-MM-DD-论文短名.md`. If the date has multiple reviews, use `-01`, `-02`, and later suffixes. Use `templates/session-review.md` as the structural reference.

The review may record communication quality, reading-focus errors, evidence-boundary issues, save/encoding/path failures, tool limitations, and reusable next steps. It is a diagnostic input, not a verbatim transcript, an Obsidian note, or an automatic change list.

When processing a `SESSION_REVIEW`:

1. Read the entire document, then read `WORKFLOW_OPTIMIZATION_PRIORITY.md` and the applicable project rules.
2. Separate observed facts, user evaluations, model inferences, concrete suggestions, and paper-specific content questions.
3. Check whether the issue is already covered by an existing rule before proposing a change.
4. Evaluate generality, severity, reproducibility, expected benefit, and implementation cost. Do not change the Skill merely because the review mentions a problem or suggestion.
5. Update the priority document with the assessment and status before changing the Skill or templates.
6. Implement only the next eligible item in priority order, using the smallest sufficient change.
7. Record why each item was adopted, deferred, rejected, or kept paper-specific, and validate the resulting behavior.

Keep `SESSION_REVIEW-*` and `HANDOFF-*` as local session artifacts. Do not sync them to the remote repository unless the user explicitly changes this policy.

## Existing note revision safety

When the user asks to revise an existing Obsidian note, read the current target file first. Treat the user's current note as the source of truth, identify the exact section to change, and apply the smallest local patch. Preserve unrelated content, frontmatter, tables, links, and the user's wording. Re-read the target after writing and report only a path that was actually written and verified. Never regenerate the whole note when a local patch is sufficient.

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

Resolve `templates/paper-reading-note.md` relative to the project root, not relative to this Skill directory. If it is missing, record the configuration problem and use only a documented fallback. The final note must contain:

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

## Markdown, formula, encoding, and path rules

- Write Markdown as UTF-8 without a BOM.
- Use `$...$` for inline mathematics and `$$...$$` for display mathematics. Do not leave pseudo-LaTeX such as `delta_t = ...` when it is intended to render as a formula.
- In YAML frontmatter, prefer single-quoted strings or forward-slash Windows paths. Do not place unescaped backslashes in YAML double-quoted strings.
- Before reporting a successful save, verify that the target exists and can be re-read as UTF-8. If an external write or verification fails, report the failure rather than claiming success.
- If no reliable Markdown/Obsidian renderer is available, report text and encoding validation only; do not claim that visual rendering was checked.

## Evidence and uncertainty rules

- Never invent page numbers, experiments, citations, authors' intentions, numerical results, or implementation details.
- If a page, figure, formula, or appendix cannot be read, say so and mark it for manual verification.
- If two parts of the paper appear inconsistent, report both locations instead of silently resolving the conflict.
- Prefer short quotations and precise paraphrases.
- Do not expose hidden chain-of-thought. Provide concise reasoning, observable evidence, and conclusions.

Use `references/evidence-standard.md` for claim support and `references/methodology-rubric.md` for critical evaluation.

## Cross-paper work

Cross-paper comparison is still supported, but it must use already saved Obsidian notes or sources explicitly provided in the current conversation. Do not scan or create `papers/` or `synthesis/` directories as part of this Skill.

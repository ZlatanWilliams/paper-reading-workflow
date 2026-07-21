---
name: paper-reading
description: Analyze academic papers from PDFs or extracted text and produce structured, evidence-backed notes, method reconstructions, experiment reviews, critiques, comparisons, and research questions. Use when the user asks to read, summarize, explain, compare, reproduce, or critically evaluate one or more papers.
---

# Paper Reading

Use this skill to turn a paper into a traceable research record. Prefer writing durable results into the current paper directory instead of leaving important conclusions only in chat.

## Inputs

Locate the paper under `papers/<paper-id>/`. Read, when available:

- `paper.pdf`
- `metadata.md`
- existing `summary.md`, `method.md`, `evidence.md`, `critique.md`, and `questions.md`
- the user's research context and current task

If the paper is not present, ask for the file or an accessible path. Do not fabricate missing content.

## Workflow

### 1. Establish scope

Identify the paper title, authors, year, venue, domain, research question, and the user's goal. If the user did not specify a goal, default to a structured first-pass reading.

### 2. Map the paper

Before evaluating it, locate the abstract, introduction, related work, method, experiments, results, discussion, limitations, references, and supplementary material. Use the full paper when available; do not treat the abstract as a substitute for the paper.

### 3. Extract claims and evidence

For every important claim, record:

- claim in concise language
- claim type: contribution, method, result, limitation, or interpretation
- evidence: equation, experiment, table, figure, or quoted passage
- source location: page, section, figure, table, or appendix
- confidence and unresolved questions

Use `references/evidence-standard.md` when deciding whether a conclusion is supported.

### 4. Analyze the method

Reconstruct inputs, outputs, assumptions, steps, objective functions, training or experimental protocol, baselines, and evaluation metrics. Explain equations intuitively, but preserve the original notation when it matters. Do not infer implementation details that the paper does not provide.

### 5. Evaluate evidence

Review problem formulation, data, baselines, metrics, ablations, controls, statistical analysis, reproducibility, and external validity. Use `references/methodology-rubric.md` for the checklist.

### 6. Connect to the user's research

Separate direct transfer, conditional transfer, and non-transferable ideas. State which assumptions would need to hold in the user's setting.

### 7. Persist artifacts

Write only the requested files, using the templates in `templates/` when present. At the end, report files changed and list items that require human verification.

## Output contract

Use this order unless the user requests another format:

1. Core conclusion
2. Evidence and source locations
3. Method reconstruction
4. Strengths and limitations
5. Uncertainty and missing evidence
6. Relevance to the user's research
7. Next questions or actions

Use tables for repeated claim/evidence mappings. Keep these distinctions explicit:

- **Paper states**: directly supported by the source.
- **Inference**: a reasoned interpretation that is not a direct quote.
- **Evaluation**: a judgment about validity, importance, or transferability.

## Evidence and uncertainty rules

- Never invent page numbers, experiments, citations, authors' intentions, or numerical results.
- If a page, figure, formula, or appendix cannot be read, say so and mark the affected conclusion for verification.
- If two parts of the paper appear inconsistent, report both locations rather than silently resolving the conflict.
- Prefer short quotations and paraphrases with precise locations.
- Do not expose hidden chain-of-thought. Provide concise reasoning, observable evidence, and conclusions.

## Task routing

- **First pass**: update `metadata.md` and `summary.md`.
- **Method explanation or reproduction**: update `method.md`.
- **Results or claim checking**: update `evidence.md`.
- **Critical review**: update `critique.md`.
- **Open questions or reading plan**: update `questions.md`.
- **Cross-paper synthesis**: read relevant paper folders and write under `synthesis/`.

## Reusable prompt shape

For a focused task, ask the user for or infer these fields:

```text
Paper: papers/<paper-id>/paper.pdf
Research context: <my topic and current level>
Task: <what to determine this time>
Focus questions: <specific questions>
Output file: <target markdown file>
```

Then apply the output contract and evidence rules above.

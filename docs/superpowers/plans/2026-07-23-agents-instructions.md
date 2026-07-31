# Repository Agent Instructions Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add a compact root `AGENTS.md` with verified build, structure, and workflow guidance for this LaTeX repository.

**Architecture:** Keep all guidance in one root instruction file because the repository has only three document roots. Derive commands and gotchas from the current entrypoints, classes, ignore rules, and CI workflow; do not alter resume sources.

**Tech Stack:** Markdown, LaTeX (`pdflatex`), GitHub Actions

## Global Constraints

- Create only the requested root `AGENTS.md`; do not change LaTeX sources or workflow behavior.
- Include only verified, repository-specific facts likely to prevent an agent mistake.
- Do not commit generated PDFs or LaTeX intermediates.
- Do not create a Git commit unless the user explicitly requests one.

---

### Task 1: Add Repository Instructions

**Files:**
- Create: `AGENTS.md`

**Interfaces:**
- Consumes: `README.md`, `.gitignore`, `.github/workflows/build-latex.yaml`, the three `main.tex` entrypoints, and both active `resume.cls` files.
- Produces: Root-level OpenCode instructions applied across the repository.

- [ ] **Step 1: Create the compact instruction file**

Create `AGENTS.md` with exactly this content:

```markdown
# Repository Guide

## Documents

- `professional/main.tex` and `academic/main.tex` are independent CI build entrypoints. Run LaTeX from the document's directory so its local `resume.cls`, `sections/`, and `styles/` resolve correctly.
- `sop/main.tex` is standalone; CI neither builds nor publishes it.
- Check the relevant `main.tex` before editing a section: several academic section files exist but are not currently included.

## Verification

- Professional: from `professional/`, run `pdflatex -interaction=nonstopmode -halt-on-error main.tex` twice.
- Academic: from `academic/`, run `pdflatex -interaction=nonstopmode -halt-on-error main.tex` twice.
- SOP changes: from `sop/`, run `pdflatex -interaction=nonstopmode -halt-on-error main.tex`.
- There is no automated test suite. Successful compilation is the focused check; existing overfull-box and `fancyhdr` warnings do not fail CI.
- PDFs and LaTeX intermediates are ignored. Do not force-add them; CI alone force-adds `output/*.pdf` on its output branch.

## LaTeX Gotchas

- Each resume has its own active `resume.cls`. The professional document deliberately leaves `styles/packages.tex` and `styles/commands.tex` disabled; edit `professional/resume.cls` for active shared layout or macro behavior.
- Do not copy environment calls blindly between resumes. For example, professional education uses `rSubsectionPlain`, while academic education uses `rTextSubsectionPlain`; their experience entries also assign the four `rSubsection` arguments differently.
- Similar facts are duplicated across academic and professional section files. When changing shared resume content, inspect both versions, but preserve document-specific wording and layout.
- CI runs BibTeX only when `professional/main.tex` or `academic/main.tex` itself contains `\bibliography{`; placing that command only in an included section will not trigger BibTeX.

## Publishing

- Pushes to `main` build only the professional and academic resumes, force-update `gh-actions/output`, and update the professional PDF through a PR in `alphaNewrex/alphanewrex.github.io`.
```

- [ ] **Step 2: Compile the professional resume twice**

From `professional/`, run:

```bash
pdflatex -interaction=nonstopmode -halt-on-error main.tex && pdflatex -interaction=nonstopmode -halt-on-error main.tex
```

Expected: exit 0 and `Output written on main.pdf`; existing overfull-box warnings are acceptable.

- [ ] **Step 3: Compile the academic resume twice**

From `academic/`, run:

```bash
pdflatex -interaction=nonstopmode -halt-on-error main.tex && pdflatex -interaction=nonstopmode -halt-on-error main.tex
```

Expected: exit 0 and `Output written on main.pdf`; existing overfull-box warnings are acceptable.

- [ ] **Step 4: Inspect the final file and worktree**

Read `AGENTS.md`, then run:

```bash
git status --short
```

Expected: `AGENTS.md` and the approved design/plan documents are untracked; pre-existing resume edits remain untouched. Do not commit.

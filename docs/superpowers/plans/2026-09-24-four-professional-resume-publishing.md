# Four Professional Resume Publishing Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build and archive four professional resume variants plus the academic CV while preserving the current stable SWE Gmail and academic CV paths.

**Architecture:** Keep SWE and MLE content in their existing LaTeX entrypoints, select the header through a defaultable path macro, and add two thin Georgia Tech wrapper entrypoints. Expand the existing Actions build artifact and generated archive pull request to publish five explicitly named PDFs, and document all outputs and commands.

**Tech Stack:** LaTeX, `pdflatex`, GitHub Actions, Bash, Git, GitHub CLI, YAML.

**Spec:** `docs/superpowers/specs/2026-09-24-four-professional-resume-publishing-design.md`

## Global Constraints

- Store generated archives on `main` only after their generated pull request is merged.
- Use UTC date names with one file per type per day.
- Preserve every merged date; replace same-day files.
- Keep `output/Harsh_Gujarathi_Resume.pdf` and `output/Harsh_Gujarathi_CV.pdf` synchronized to the latest clean-runner build.
- Keep local PDFs ignored; CI force-adds only canonical dated archives and stable latest files.
- Keep the academic entrypoint `academic/main.tex` and its stable and dated archive paths unchanged.
- The professional wrappers select `sections/header_gatech` and input the existing SWE or MLE entrypoint.
- The artifact and generated pull request contain exactly four professional variants and one academic CV.

---

### Task 1: Add shared-content Georgia Tech entrypoints

**Files:**
- Modify: `professional/main.tex`
- Modify: `professional/main_mle.tex`
- Create: `professional/main_gatech.tex`
- Create: `professional/main_mle_gatech.tex`
- Use: `professional/sections/header.tex`
- Use: `professional/sections/header_gatech.tex`

**Interfaces:**
- Consumes: shared SWE and MLE entrypoints plus the selected `\ResumeHeaderPath` macro.
- Produces: four locally buildable professional entrypoints whose only email difference is the selected header.

- [x] **Step 1: Make the current Gmail header the default in each primary entrypoint**

In `main.tex` and `main_mle.tex`, define the default once before the document body:

```latex
\providecommand{\ResumeHeaderPath}{sections/header}
```

Replace the current direct header input with:

```latex
\input{\ResumeHeaderPath}
```

- [x] **Step 2: Add the two Georgia Tech wrappers**

Create `main_gatech.tex` with:

```latex
\def\ResumeHeaderPath{sections/header_gatech}
\input{main.tex}
```

Create `main_mle_gatech.tex` with:

```latex
\def\ResumeHeaderPath{sections/header_gatech}
\input{main_mle.tex}
```

- [x] **Step 3: Compile and inspect each professional variant**

From `professional/`, compile `main.tex`, `main_gatech.tex`, `main_mle.tex`, and `main_mle_gatech.tex` twice with `pdflatex -interaction=nonstopmode -halt-on-error`. Use `-jobname=main_gatech` and `-jobname=main_mle_gatech` for wrapper builds to retain distinct PDFs and auxiliary files. Verify one page per PDF, Gmail text only in Gmail variants, Georgia Tech text only in the wrapper variants, and SWE/MLE section routing.

Completion criterion: all four entrypoints compile twice and their extracted text matches the selected header and resume type.

### Task 2: Build and upload five explicit CI artifacts

**Files:**
- Modify: `.github/workflows/build-latex.yaml`

**Interfaces:**
- Consumes: the five LaTeX entrypoints and UTC date.
- Produces: five explicit dated artifact paths exposed as job outputs and uploaded by `compiled-resumes`.

- [x] **Step 1: Define all five UTC dated paths**

In `Set dated output paths`, define the existing SWE and academic paths plus:

```text
output/professional/Harsh_Gujarathi_Resume_GeorgiaTech_YYYY_MM_DD.pdf
output/professional/Harsh_Gujarathi_MLE_Resume_YYYY_MM_DD.pdf
output/professional/Harsh_Gujarathi_MLE_Resume_GeorgiaTech_YYYY_MM_DD.pdf
```

Write each as a named step output, create each parent directory, and retain the existing `SOURCE_DATE_EPOCH` calculation.

- [x] **Step 2: Expose all five paths as build job outputs**

Keep `professional_path` and `academic_path` for compatibility with the current workflow structure. Add `professional_gatech_path`, `mle_path`, and `mle_gatech_path`, each mapped to its matching step output.

- [x] **Step 3: Compile all four professional entrypoints**

Run both LaTeX passes from `professional/`. Use distinct `-jobname` values for the two wrapper entrypoints and copy each resulting PDF to its named path under `${GITHUB_WORKSPACE}`. Keep the existing two-pass compile for the SWE Gmail entrypoint.

- [x] **Step 4: Keep the academic build unchanged and upload exactly five files**

Compile `academic/main.tex` twice as before. List the five named paths explicitly in `actions/upload-artifact@v4`; retain `if-no-files-found: error` and do not use a PDF wildcard.

Completion criterion: YAML exposes five dated outputs and the artifact step lists exactly those five paths.

### Task 3: Expand canonical archive publishing to five PDFs

**Files:**
- Modify: `.github/workflows/build-latex.yaml`

**Interfaces:**
- Consumes: the five exact artifact paths from `build_latex` and existing `gh-actions/output` archive history.
- Produces: five stable latest PDFs, five dated archives, and the existing generated PDF-only pull request.

- [x] **Step 1: Download the five-file artifact and pass explicit paths**

Keep the artifact name `compiled-resumes`. Add the three new job outputs to the publish job environment while preserving the existing `PROFESSIONAL_PATH` and `ACADEMIC_PATH` variables.

- [x] **Step 2: Define and validate canonical archive and stable paths**

Accept dated professional files matching exactly the four documented professional basenames plus the UTC date suffix; accept the academic path only at its documented dated basename. Validate all five environment paths and artifact files before touching the staging branch.

Stable files are exactly:

```text
output/Harsh_Gujarathi_Resume.pdf
output/Harsh_Gujarathi_Resume_GeorgiaTech.pdf
output/Harsh_Gujarathi_MLE_Resume.pdf
output/Harsh_Gujarathi_MLE_Resume_GeorgiaTech.pdf
output/Harsh_Gujarathi_CV.pdf
```

- [x] **Step 3: Preserve valid history and copy current artifacts**

When reading archive files from `gh-actions/output`, preserve only dated paths matching one of the canonical archive patterns and absent from candidate `main`. Copy all five current dated artifacts and copy each same artifact to its corresponding stable path.

- [x] **Step 4: Stage and guard only the ten canonical output paths**

Add the preserved dated archives, five current dated outputs, and five stable paths with `git add -f`. Extend the staged-path guard to reject every path outside those exact patterns and filenames.

- [x] **Step 5: Update generated pull-request wording**

Keep source-compatibility, ancestry, force-with-lease, and reconciliation checks unchanged. Update the generated pull-request description to say it archives four professional resume variants and one academic CV.

Completion criterion: every accepted artifact and staged path is canonical, all five latest files are copied from the current build, and prior dated archives are preserved.

### Task 4: Update contributor and repository guidance

**Files:**
- Modify: `README.md`
- Modify: `AGENTS.md`

**Interfaces:**
- Consumes: the entrypoints, path names, and verification commands defined in Tasks 1-3.
- Produces: accurate local build instructions and publishing guidance.

- [x] **Step 1: Document all five local entrypoints**

List the four professional entrypoints and academic entrypoint. Give exact two-pass `pdflatex` commands from each document directory, including `-jobname=main_gatech` and `-jobname=main_mle_gatech` for wrappers.

- [x] **Step 2: Document stable and dated paths**

List all five stable latest paths and all five UTC dated archive patterns. State that the existing `Harsh_Gujarathi_Resume.pdf` remains the SWE Gmail version and the existing CV path remains unchanged.

- [x] **Step 3: Document the five-file Actions artifact and archive PR**

Describe source PR validation, main-branch builds, the exact five-file Actions artifact, and the generated PDF-only archive PR. Preserve existing local-only SOP and ignored-PDF guidance.

Completion criterion: README and AGENTS agree with the workflow’s entrypoints, exact output names, and publication behavior.

### Task 5: Verify all five builds and archive rules

**Files:**
- Verify: `professional/main.tex`
- Verify: `professional/main_mle.tex`
- Verify: `professional/main_gatech.tex`
- Verify: `professional/main_mle_gatech.tex`
- Verify: `academic/main.tex`
- Verify: `.github/workflows/build-latex.yaml`
- Verify: `README.md`
- Verify: `AGENTS.md`

**Interfaces:**
- Consumes: Tasks 1-4.
- Produces: evidence that five variants compile, route correctly, and meet the exact archive contract.

- [x] **Step 1: Compile all five documents twice**

Run two-pass `pdflatex -interaction=nonstopmode -halt-on-error` builds from each document directory. Professional builds use these exact commands:

```bash
pdflatex -interaction=nonstopmode -halt-on-error main.tex
pdflatex -interaction=nonstopmode -halt-on-error main.tex
pdflatex -interaction=nonstopmode -halt-on-error -jobname=main_gatech main_gatech.tex
pdflatex -interaction=nonstopmode -halt-on-error -jobname=main_gatech main_gatech.tex
pdflatex -interaction=nonstopmode -halt-on-error main_mle.tex
pdflatex -interaction=nonstopmode -halt-on-error main_mle.tex
pdflatex -interaction=nonstopmode -halt-on-error -jobname=main_mle_gatech main_mle_gatech.tex
pdflatex -interaction=nonstopmode -halt-on-error -jobname=main_mle_gatech main_mle_gatech.tex
```

Build the academic entrypoint twice with its existing command.

- [x] **Step 2: Verify page counts and extracted headers/sections**

Use `pdfinfo` or `mdls` to verify all four professional PDFs are one page. Extract each PDF’s text and confirm email and SWE/MLE section selection. Confirm the academic CV compiles successfully.

- [x] **Step 3: Parse and inspect the workflow**

Parse `.github/workflows/build-latex.yaml` with an available YAML parser and run `actionlint` when installed. Inspect that artifact upload lists five explicit paths, path validation checks five artifacts, stable output has five exact entries, archive regex accepts four professional plus one academic dated path, staged guards reject noncanonical files, and all existing stale-source and force-with-lease checks remain.

- [x] **Step 4: Check documentation and diff hygiene**

Search README and AGENTS for stale claims that only two documents build. Run `git diff --check` and inspect the full implementation diff while confirming pre-existing dirty resume files remain present and untouched except where the plan requires.

Completion criterion: five named outputs match the specification, every required local build succeeds, professional page and header routing are correct, and the final diff contains only the planned publishing and guidance changes.

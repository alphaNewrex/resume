# Resume Publishing Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Archive CI-built professional resumes and academic CVs through a generated pull request to `main`, expose stable latest-download files, and keep the local SOP out of new Git commits.

**Architecture:** Use a `contents: read` build job to compile both documents and upload only the current dated pair. A dependent trusted-`main` publish job alone receives `contents: write` and `pull-requests: write`, and uses the existing `gh-actions/output` branch for a PDF-only pull request containing dated archives plus stable latest copies. Every `main` push runs reconciliation, while generated output-only pull requests skip their pull-request run. Git ignore rules protect local PDFs and the entire SOP directory.

**Tech Stack:** GitHub Actions, Bash, GitHub CLI, Git, LaTeX/pdflatex

## Global Constraints

- Store generated archives on `main` only after their generated pull request is merged.
- Use UTC date names with one file per type per day.
- Preserve every merged date; replace same-day files.
- Use `output/professional/Harsh_Gujarathi_Resume_YYYY_MM_DD.pdf` and `output/academic/Harsh_Gujarathi_CV_YYYY_MM_DD.pdf`.
- Keep `output/Harsh_Gujarathi_Resume.pdf` and `output/Harsh_Gujarathi_CV.pdf` synchronized to the latest clean-runner build.
- Keep local PDFs ignored; do not add local-PDF enforcement beyond `.gitignore`.
- Ignore and untrack the entire `sop/` directory without deleting its local files or rewriting history.
- Remove website publishing.
- Add no scripts or dependencies.

---

### Task 1: Replace The Publishing Workflow

**Files:**
- Modify: `.github/workflows/build-latex.yaml`

**Interfaces:**
- Consumes: `professional/main.tex`, `academic/main.tex`, and the repository `GITHUB_TOKEN`.
- Produces: two dated archives, two stable latest files, one two-file Actions artifact, and an updated `gh-actions/output` pull request.

- [ ] **Step 1: Add the pull-request output exclusion, explicit permissions, and concurrency**

Configure only `pull_request` to ignore `output/**`; every push to `main`, including an output-only archive merge, must run one reconciliation build. Grant the build job only `contents: read`, grant only the trusted-`main` publish job `contents: write` and `pull-requests: write`, and cancel an older run for the same ref.

- [ ] **Step 2: Generate exact UTC output paths**

Use `date -u +%Y_%m_%d`, expose the two paths through `$GITHUB_OUTPUT`, and create their parent directories.

- [ ] **Step 3: Compile both documents with hard failure behavior**

Run the repository's documented `pdflatex -interaction=nonstopmode -halt-on-error main.tex` command twice in each document directory, then copy each result to its exact dated path. The publish job also copies those clean-runner files to `output/Harsh_Gujarathi_Resume.pdf` and `output/Harsh_Gujarathi_CV.pdf`.

- [ ] **Step 4: Upload only the current pair**

Pass the two step outputs explicitly to `actions/upload-artifact@v4` and use `if-no-files-found: error`; do not use a PDF wildcard.

- [ ] **Step 5: Publish through a generated pull request**

On `main` pushes only, require `origin/main` to descend from the trigger SHA with no changes outside `output/**`. Build the candidate from that fetched main SHA, retain only staging-branch dated archives absent from `main`, recopy the clean-runner results so same-day archives and both stable latest files are replaced, commit only canonical dated/stable paths, force-push with lease protection, and create a PR only when no same-repository generated PR is open. Fetch and repeat the compatibility check immediately before pushing and after pushing before PR reconciliation. Rebuild and, when needed, repush on a compatible output-only advance; exit successfully on a source-changing or non-descendant advance.

- [ ] **Step 6: Remove obsolete pipeline behavior**

Delete debug-only file listings, ignore inspection, fixed output filenames, direct output-branch storage semantics, and the complete personal-website job.

### Task 2: Make SOP And Local Output Private By Default

**Files:**
- Modify: `.gitignore`
- Remove from Git tracking only: `sop/main.tex`

**Interfaces:**
- Consumes: the existing local `sop/` directory.
- Produces: an ignored local-only SOP directory while leaving its files on disk.

- [ ] **Step 1: Ignore the SOP directory**

Add `sop/` to `.gitignore`; retain the existing global `*.pdf` rule.

- [ ] **Step 2: Remove SOP from the index without deleting it locally**

Run `git rm --cached -r sop/` and verify `sop/main.tex` remains readable from the working tree.

- [ ] **Step 3: Verify ignore behavior**

Run `git check-ignore -v sop/main.tex sop/main.pdf professional/main.pdf academic/main.pdf output/professional/Harsh_Gujarathi_Resume_2026_07_29.pdf` and expect every path to match `.gitignore`.

### Task 3: Update Repository Guidance And Settings

**Files:**
- Modify: `README.md`
- Modify: `AGENTS.md`

**Interfaces:**
- Consumes: the final workflow and paths from Task 1.
- Produces: accurate contributor and publishing documentation.

- [ ] **Step 1: Document the archive and generated PR**

Replace dedicated-output-branch download instructions with the two dated archive locations and stable `output/Harsh_Gujarathi_Resume.pdf` and `output/Harsh_Gujarathi_CV.pdf` download paths. Explain that generated PDFs arrive through a PDF-only PR.

- [ ] **Step 2: Document local-only SOP behavior**

State that `sop/` is intentionally ignored and outside CI, and that CI force-adds only its current generated resume/CV pair.

- [ ] **Step 3: Enable native Actions PR creation**

Update the repository Actions workflow permission setting so `GITHUB_TOKEN` can create pull requests; do not use `PERSONAL_ACCESS_TOKEN`.

### Task 4: Verify End To End

**Files:**
- Verify: `.github/workflows/build-latex.yaml`
- Verify: `.gitignore`
- Verify: `README.md`
- Verify: `AGENTS.md`

**Interfaces:**
- Consumes: Tasks 1-3.
- Produces: evidence that both documents compile and the repository state matches the design.

- [ ] **Step 1: Compile the professional resume**

Run twice from `professional/`: `pdflatex -interaction=nonstopmode -halt-on-error main.tex`. Expect exit code 0 both times.

- [ ] **Step 2: Compile the academic CV**

Run twice from `academic/`: `pdflatex -interaction=nonstopmode -halt-on-error main.tex`. Expect exit code 0 both times.

- [ ] **Step 3: Validate workflow syntax and policy details**

Parse the YAML with an available YAML parser, inspect the diff, confirm no website/PAT references or wildcard PDF uploads remain, and simulate initial, pre-push, and post-push source-stale exits, compatible pre-push and post-push output-only rebuilds, replacement of the two stable latest files, preservation of dated history, canonical staging scope, and fork/same-repository pull-request handling.

- [ ] **Step 4: Verify Git and repository settings**

Confirm SOP is staged only as a tracked deletion but still exists locally, generated files remain ignored until CI force-adds them, and `can_approve_pull_request_reviews` is `true` in the Actions workflow-permissions API response.

### Task 5: Add Stable Latest Downloads

**Files:**
- Modify: `.github/workflows/build-latex.yaml`
- Modify: `README.md`
- Modify: `AGENTS.md`

**Interfaces:**
- Consumes: the two exact dated artifact paths produced by `build_latex`.
- Produces: `output/Harsh_Gujarathi_Resume.pdf` and `output/Harsh_Gujarathi_CV.pdf` in the generated archive pull request.

- [ ] **Step 1: Define and validate the stable paths**

In the publish script, define:

```bash
latest_professional_path="output/Harsh_Gujarathi_Resume.pdf"
latest_academic_path="output/Harsh_Gujarathi_CV.pdf"
latest_professional_pattern='^output/Harsh_Gujarathi_Resume\.pdf$'
latest_academic_pattern='^output/Harsh_Gujarathi_CV\.pdf$'
```

Extend the staged-path guard so a path is accepted only when it matches one of the dated archive patterns or one of these exact stable patterns.

- [ ] **Step 2: Copy and stage the stable latest files**

After copying the two dated files, copy the same clean-runner artifacts to the stable paths:

```bash
cp -- "${artifact_dir}/${PROFESSIONAL_PATH}" "$latest_professional_path"
cp -- "${artifact_dir}/${ACADEMIC_PATH}" "$latest_academic_path"
```

Include both stable paths in `canonical_paths`. Do not restore root-level stable files from the old staging branch; each run recreates them from the current artifact.

- [ ] **Step 3: Document direct-download paths**

Add `output/Harsh_Gujarathi_Resume.pdf` and `output/Harsh_Gujarathi_CV.pdf` to the README archive section and AGENTS publishing guidance. State that dated files are the archive and stable files always contain the latest merged pair.

- [ ] **Step 4: Verify replacement and scope**

Run `actionlint`, parse the YAML, run `git diff --check`, and simulate two builds against a local bare remote. Assert that dated history remains, the two root files equal the second build's artifacts, and no non-canonical output path is staged.

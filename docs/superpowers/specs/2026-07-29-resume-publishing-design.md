# Resume Publishing Design

## Goal

Archive CI-generated professional and academic PDFs on `main`, provide stable latest-download paths, and keep local PDFs and the entire SOP directory out of new commits.

## Publishing Flow

- Every push to `main` and source or content pull requests targeting `main` compile both LaTeX documents.
- Generated output-only pull requests skip their pull-request run because that trigger ignores `output/**`. Merging one triggers a `main` reconciliation build, which normally creates no further PDF pull request when the compiled bytes are unchanged. The workflow never pushes `main`, so this does not loop.
- Each build uses the UTC date and creates exactly:
  - `output/professional/Harsh_Gujarathi_Resume_YYYY_MM_DD.pdf`
  - `output/academic/Harsh_Gujarathi_CV_YYYY_MM_DD.pdf`
- Different dates accumulate in both archive directories. A later build on the same UTC date replaces that date's files.
- The same generated pull request updates stable copies at `output/Harsh_Gujarathi_Resume.pdf` and `output/Harsh_Gujarathi_CV.pdf` for direct download and sharing.
- Stable copies always match the current clean-runner build; only dated files are retained as history.
- Actions artifacts contain only the two files compiled on that clean runner.
- On a `main` push, the trusted publish job accepts the current `origin/main` only when it descends from the trigger SHA and differs from it exclusively under `output/**`. It builds each archive candidate from that fetched main SHA, force-updates the existing `gh-actions/output` staging branch, and opens or updates one same-repository PDF-only pull request into `main`.
- The publish job fetches and repeats the compatibility check immediately before pushing and again before pull-request reconciliation. A compatible output-only advance rebuilds and, when necessary, republishes the candidate on the newer main while preserving branch-only dated archives; a source change or non-descendant exits successfully for the newer workflow to handle.
- The repository's pull-request rule remains intact. The user merges the generated PR to place PDFs on `main`.
- The personal-website publishing job is removed.

## Local Files And SOP

- `*.pdf` remains ignored. CI force-adds only canonical dated archives and the two stable latest files.
- This is accidental-add protection, not enforcement against deliberately changing an already tracked archive PDF.
- `sop/` is ignored and removed from the current Git index while retained in the local working tree.
- Existing SOP copies remain available in public Git history; this change does not rewrite history.

## Repository Settings

- Enable GitHub Actions to create pull requests with `GITHUB_TOKEN`.
- Grant the build job only `contents: read`, including when it compiles untrusted pull-request LaTeX.
- Grant only the trusted `main`-push publish job `contents: write` and `pull-requests: write`.
- Use branch-scoped concurrency and verify source-tree compatibility after the initial fetch, immediately before pushing, and after pushing so stale builds cannot replace newer output.

## Verification

- Compile `professional/main.tex` and `academic/main.tex` twice with `pdflatex -interaction=nonstopmode -halt-on-error`.
- Parse the workflow YAML and inspect its diff.
- Confirm the publish simulations reject source-stale and non-descendant runs, rebuild across compatible output-only advances, preserve dated history, and replace both stable latest files.
- Confirm `sop/` and local/generated PDFs are ignored.
- Confirm `sop/main.tex` still exists locally but is staged for removal from Git tracking.
- Confirm the GitHub Actions repository setting allows pull-request creation.

# Repository Guide

## Documents

- `professional/main.tex` (SWE/Gmail), `professional/main_gatech.tex` (SWE/Georgia Tech), `professional/main_mle.tex` (MLE/Gmail), `professional/main_mle_gatech.tex` (MLE/Georgia Tech), and `academic/main.tex` are the five CI build entrypoints. Run LaTeX from the document's directory so its local `resume.cls`, `sections/`, and `styles/` resolve correctly.
- The two Georgia Tech professional entrypoints select `sections/header_gatech.tex` and import their shared SWE or MLE entrypoint. Keep variant content in the shared entrypoints rather than duplicating it in wrappers.
- `sop/` is intentionally local-only and ignored. CI neither builds nor publishes it; ignoring the directory does not erase its old Git history.
- Section inclusion is controlled by each `main.tex`; do not assume every file under `sections/` is active. For example, `professional/sections/awards.tex` is currently excluded.

## Verification

- SWE/Gmail: from `professional/`, run `pdflatex -interaction=nonstopmode -halt-on-error main.tex` twice.
- SWE/Georgia Tech: from `professional/`, run `pdflatex -interaction=nonstopmode -halt-on-error -jobname=main_gatech main_gatech.tex` twice.
- MLE/Gmail: from `professional/`, run `pdflatex -interaction=nonstopmode -halt-on-error main_mle.tex` twice.
- MLE/Georgia Tech: from `professional/`, run `pdflatex -interaction=nonstopmode -halt-on-error -jobname=main_mle_gatech main_mle_gatech.tex` twice.
- Academic: from `academic/`, run `pdflatex -interaction=nonstopmode -halt-on-error main.tex` twice.
- SOP changes: from `sop/`, run `pdflatex -interaction=nonstopmode -halt-on-error main.tex`.
- There is no automated test runner or configured lint command. Successful compilation is the focused check; current documents emit warnings while still exiting successfully.
- PDFs and LaTeX intermediates are ignored. Do not stage them; CI alone force-adds the canonical dated archives and five exact stable files.

## LaTeX Gotchas

- Each resume has its own active `resume.cls`. The professional document deliberately leaves `styles/packages.tex` and `styles/commands.tex` disabled; edit `professional/resume.cls` for active shared layout or macro behavior.
- Do not copy environment calls blindly between resumes. Academic education uses `rTextSubsection`, while professional education uses `rSubsectionPlain`; the available class APIs differ.
- Similar facts are duplicated across academic and professional section files. When changing shared resume content, inspect both versions, but preserve document-specific wording and layout.

## Publishing

- Source or content pull requests compile all five documents for validation; their `compiled-resumes` Actions artifact contains exactly the four professional PDFs and academic CV. Generated output-only archive pull requests intentionally skip their pull-request run because `output/**` is ignored for that trigger.
- Every push to `main` builds all five documents. Source changes generate or update a PDF-only pull request from `gh-actions/output` when the PDFs change. Merging that pull request archives the PDFs and triggers one reconciliation build, normally without another PDF pull request when the bytes are unchanged.
- Stable latest paths are `output/Harsh_Gujarathi_Resume.pdf` (SWE/Gmail), `output/Harsh_Gujarathi_Resume_GeorgiaTech.pdf` (SWE/Georgia Tech), `output/Harsh_Gujarathi_MLE_Resume.pdf` (MLE/Gmail), `output/Harsh_Gujarathi_MLE_Resume_GeorgiaTech.pdf` (MLE/Georgia Tech), and `output/Harsh_Gujarathi_CV.pdf` (academic CV). They always contain the latest generated versions merged into `main`.
- UTC archive paths are `output/professional/Harsh_Gujarathi_Resume_YYYY_MM_DD.pdf`, `output/professional/Harsh_Gujarathi_Resume_GeorgiaTech_YYYY_MM_DD.pdf`, `output/professional/Harsh_Gujarathi_MLE_Resume_YYYY_MM_DD.pdf`, `output/professional/Harsh_Gujarathi_MLE_Resume_GeorgiaTech_YYYY_MM_DD.pdf`, and `output/academic/Harsh_Gujarathi_CV_YYYY_MM_DD.pdf`.
- All merged dates are retained. A changed repeat build on the same UTC date replaces that date's five files.

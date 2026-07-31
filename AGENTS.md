# Repository Guide

## Documents

- `professional/main.tex` and `academic/main.tex` are independent CI build entrypoints. Run LaTeX from the document's directory so its local `resume.cls`, `sections/`, and `styles/` resolve correctly.
- `sop/` is intentionally local-only and ignored. CI neither builds nor publishes it; ignoring the directory does not erase its old Git history.
- Section inclusion is controlled by each `main.tex`; do not assume every file under `sections/` is active. For example, `professional/sections/awards.tex` is currently excluded.

## Verification

- Professional: from `professional/`, run `pdflatex -interaction=nonstopmode -halt-on-error main.tex` twice.
- Academic: from `academic/`, run `pdflatex -interaction=nonstopmode -halt-on-error main.tex` twice.
- SOP changes: from `sop/`, run `pdflatex -interaction=nonstopmode -halt-on-error main.tex`.
- There is no automated test runner or configured lint command. Successful compilation is the focused check; current documents emit warnings while still exiting successfully.
- PDFs and LaTeX intermediates are ignored. Do not stage them; CI alone force-adds the canonical dated paths and exact stable pair.

## LaTeX Gotchas

- Each resume has its own active `resume.cls`. The professional document deliberately leaves `styles/packages.tex` and `styles/commands.tex` disabled; edit `professional/resume.cls` for active shared layout or macro behavior.
- Do not copy environment calls blindly between resumes. Academic education uses `rTextSubsection`, while professional education uses `rSubsectionPlain`; the available class APIs differ.
- Similar facts are duplicated across academic and professional section files. When changing shared resume content, inspect both versions, but preserve document-specific wording and layout.

## Publishing

- Source or content pull requests compile both documents for validation, and their Actions artifact contains only the current pair. Generated output-only archive pull requests intentionally skip their pull-request run because `output/**` is ignored for that trigger.
- Every push to `main` builds both documents. Source changes generate or update a PDF-only pull request from `gh-actions/output` when the PDFs change. Merging that pull request archives the PDFs and triggers one reconciliation build, normally without another PDF pull request when the bytes are unchanged.
- Stable latest paths are `output/Harsh_Gujarathi_Resume.pdf` and `output/Harsh_Gujarathi_CV.pdf`; they always contain the latest pair merged into `main`.
- Archive paths are `output/professional/Harsh_Gujarathi_Resume_YYYY_MM_DD.pdf` and `output/academic/Harsh_Gujarathi_CV_YYYY_MM_DD.pdf`, dated in UTC.
- All merged dates are retained. A changed repeat build on the same UTC date replaces that date's pair.

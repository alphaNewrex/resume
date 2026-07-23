# Repository Guide

## Documents

- `professional/main.tex` and `academic/main.tex` are independent CI build entrypoints. Run LaTeX from the document's directory so its local `resume.cls`, `sections/`, and `styles/` resolve correctly.
- `sop/main.tex` is standalone; CI neither builds nor publishes it.
- Section inclusion is controlled by each `main.tex`; do not assume every file under `sections/` is active. For example, `professional/sections/awards.tex` is currently excluded.

## Verification

- Professional: from `professional/`, run `pdflatex -interaction=nonstopmode -halt-on-error main.tex` twice.
- Academic: from `academic/`, run `pdflatex -interaction=nonstopmode -halt-on-error main.tex` twice.
- SOP changes: from `sop/`, run `pdflatex -interaction=nonstopmode -halt-on-error main.tex`.
- There is no automated test runner or configured lint command. Successful compilation is the focused check; current documents emit warnings while still exiting successfully.
- PDFs and LaTeX intermediates are ignored. Do not stage them; CI alone force-adds `output/*.pdf` on its output branch.

## LaTeX Gotchas

- Each resume has its own active `resume.cls`. The professional document deliberately leaves `styles/packages.tex` and `styles/commands.tex` disabled; edit `professional/resume.cls` for active shared layout or macro behavior.
- Do not copy environment calls blindly between resumes. Academic education uses `rTextSubsection`, while professional education uses `rSubsectionPlain`; the available class APIs differ.
- Similar facts are duplicated across academic and professional section files. When changing shared resume content, inspect both versions, but preserve document-specific wording and layout.
- CI runs BibTeX only when `professional/main.tex` or `academic/main.tex` itself contains `\bibliography{`; placing that command only in an included section will not trigger BibTeX.

## Publishing

- Pushes to `main` build `output/professional_resume.pdf` and `output/academic_resume.pdf`, force-update `gh-actions/output`, and update the professional PDF through a PR in `alphaNewrex/alphanewrex.github.io`.

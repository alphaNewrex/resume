# AGENTS.md Design

## Goal

Add one compact root `AGENTS.md` containing only verified guidance that prevents likely mistakes in this LaTeX repository.

## Content

- Identify `professional/main.tex` and `academic/main.tex` as the two CI-built documents; note that `sop/main.tex` is standalone and not built or published by CI.
- Give directory-local `pdflatex` commands matching the workflow, including the second pass used for references.
- Explain that generated PDFs and LaTeX intermediates are ignored and should not be committed.
- Record non-obvious wiring: each resume uses its own local `resume.cls`; the professional document leaves `styles/packages.tex` and `styles/commands.tex` disabled because its class owns those definitions.
- Warn that the academic and professional class APIs differ, and that similar content is duplicated rather than shared.
- Note that pushes to `main` publish PDFs to `gh-actions/output` and open or update a website PR for the professional PDF.

## Verification

Compile both CI targets twice from their own directories with `pdflatex -interaction=nonstopmode -halt-on-error main.tex`. Treat existing overfull-box warnings as warnings, not compilation failures. There is no automated test suite.

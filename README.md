# LaTeX Resume Repository

This repository contains LaTeX source files for both professional and academic resumes. The repository is structured with a modular approach, separating content into reusable components that can be easily updated and maintained.

## Compiled PDFs

### Latest Downloads

- [SWE resume (Gmail): `output/Harsh_Gujarathi_Resume.pdf`](output/Harsh_Gujarathi_Resume.pdf)
- [SWE resume (Georgia Tech): `output/Harsh_Gujarathi_Resume_GeorgiaTech.pdf`](output/Harsh_Gujarathi_Resume_GeorgiaTech.pdf)
- [MLE resume (Gmail): `output/Harsh_Gujarathi_MLE_Resume.pdf`](output/Harsh_Gujarathi_MLE_Resume.pdf)
- [MLE resume (Georgia Tech): `output/Harsh_Gujarathi_MLE_Resume_GeorgiaTech.pdf`](output/Harsh_Gujarathi_MLE_Resume_GeorgiaTech.pdf)
- [Academic CV: `output/Harsh_Gujarathi_CV.pdf`](output/Harsh_Gujarathi_CV.pdf)

These stable paths contain the latest generated versions merged into `main`. The existing `Harsh_Gujarathi_Resume.pdf` path remains the SWE resume with the Gmail header; the existing academic CV path is unchanged.

### Dated History

Historical PDFs are archived on `main` at:

- `output/professional/Harsh_Gujarathi_Resume_YYYY_MM_DD.pdf` (SWE, Gmail)
- `output/professional/Harsh_Gujarathi_Resume_GeorgiaTech_YYYY_MM_DD.pdf` (SWE, Georgia Tech)
- `output/professional/Harsh_Gujarathi_MLE_Resume_YYYY_MM_DD.pdf` (MLE, Gmail)
- `output/professional/Harsh_Gujarathi_MLE_Resume_GeorgiaTech_YYYY_MM_DD.pdf` (MLE, Georgia Tech)
- `output/academic/Harsh_Gujarathi_CV_YYYY_MM_DD.pdf`

Dates use UTC. Every push to `main` compiles all four professional resumes and the academic CV. Source changes generate or update a PDF-only pull request from `gh-actions/output` when the PDFs change; deterministic same-day builds with no changes need no new pull request. Merging the generated pull request updates the five stable latest copies, adds the current dated set to the archive, and triggers one reconciliation build on `main`. That build normally creates no further PDF pull request when the bytes are unchanged. Previously merged dates remain available, while a changed build on the same UTC date replaces that date's five files.

Source or content pull requests compile all five documents for validation, and their `compiled-resumes` Actions artifact contains exactly the five current PDFs. Generated output-only archive pull requests intentionally skip their pull-request run because `output/**` is ignored for that trigger.

## Repository Structure
```
resume/ 
├── academic/            # Academic resume files
│   ├── main.tex         # Main document that imports all components 
│   ├── resume.cls       # Class file for academic resume formatting 
│   ├── sections/        # Content sections (education, experience, etc.)
│   └── styles/          # Style configurations 
├── professional/        # Professional resume files 
│   ├── main.tex         # SWE resume with Gmail header
│   ├── main_gatech.tex  # SWE resume with Georgia Tech header
│   ├── main_mle.tex     # MLE resume with Gmail header
│   ├── main_mle_gatech.tex # MLE resume with Georgia Tech header
│   ├── sections/        # Content sections (education, experience, etc.)
│   └── styles/          # Style configurations and commands 
├── output/              # Generated PDF files 
└── .github/workflows/   # GitHub Actions workflow definitions
```

## Prerequisites

To work with this repository, you'll need:

1. **LaTeX Distribution**:
   - For macOS: [MacTeX](https://www.tug.org/mactex/)
   - For Windows: [MiKTeX](https://miktex.org/) or [TeX Live](https://www.tug.org/texlive/)
   - For Linux: [TeX Live](https://www.tug.org/texlive/)

2. **Git** for version control

3. **Visual Studio Code** as the recommended editor

## VSCode Setup

### Installation

1. Download and install [Visual Studio Code](https://code.visualstudio.com/)
2. Clone this repository:

    ```bash
    git clone https://github.com/alphaNewrex/resume.git
    cd resume
    ```

### Extensions

1. Install the [LaTeX Workshop](https://marketplace.visualstudio.com/items?itemName=James-Yu.latex-workshop) extension for Visual Studio Code
2. Install the [LaTeX Utilities](https://marketplace.visualstudio.com/items?itemName=tecosaur.latex-utilities) extension for Visual Studio Code
3. Install the [LaTeX Snippets](https://marketplace.visualstudio.com/items?itemName=blang.latex) extension for Visual Studio Code
4. Install the [LaTeX Preview](https://marketplace.visualstudio.com/items?itemName=ajshort.latex-preview) extension for Visual Studio Code.

### Build LaTeX

1. Open the `resume` folder in Visual Studio Code.
2. Open a LaTeX entrypoint and use the `Build LaTeX project` button in the editor. The generated PDF is saved in that document directory.
3. To build all outputs from a terminal, run these commands from the repository root:

    ```bash
    cd professional
    pdflatex -interaction=nonstopmode -halt-on-error main.tex
    pdflatex -interaction=nonstopmode -halt-on-error main.tex
    pdflatex -interaction=nonstopmode -halt-on-error -jobname=main_gatech main_gatech.tex
    pdflatex -interaction=nonstopmode -halt-on-error -jobname=main_gatech main_gatech.tex
    pdflatex -interaction=nonstopmode -halt-on-error main_mle.tex
    pdflatex -interaction=nonstopmode -halt-on-error main_mle.tex
    pdflatex -interaction=nonstopmode -halt-on-error -jobname=main_mle_gatech main_mle_gatech.tex
    pdflatex -interaction=nonstopmode -halt-on-error -jobname=main_mle_gatech main_mle_gatech.tex
    cd ../academic
    pdflatex -interaction=nonstopmode -halt-on-error main.tex
    pdflatex -interaction=nonstopmode -halt-on-error main.tex
    ```

## GitHub Actions

GitHub Actions compiles all five LaTeX entrypoints for source or content pull requests and every push to `main`. Its `compiled-resumes` artifact contains the current four professional PDFs and academic CV. A trusted `main` build opens or updates a PDF-only archive pull request when outputs change. Generated output-only archive pull requests skip their pull-request run, but merging one triggers a `main` reconciliation build. Local PDFs are ignored; CI force-adds only the five canonical dated archive paths and five stable latest files.

The `sop/` directory is intentionally local-only, ignored by Git, and outside CI. Existing SOP history remains in the repository's earlier commits.

[![Build LaTeX Resumes](https://github.com/alphaNewrex/resume/actions/workflows/build-latex.yaml/badge.svg)](https://github.com/alphaNewrex/resume/actions/workflows/build-latex.yaml)

## License

This repository is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

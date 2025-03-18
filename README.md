# LaTeX Resume Repository

This repository contains LaTeX source files for both professional and academic resumes. The repository is structured with a modular approach, separating content into reusable components that can be easily updated and maintained.

## Repository Structure
```
resume/ 
├── academic/            # Academic resume files
│   ├── main.tex         # Main document that imports all components 
│   ├── resume.cls       # Class file for academic resume formatting 
│   ├── sections/        # Content sections (education, experience, etc.)
│   └── styles/          # Style configurations 
├── professional/        # Professional resume files 
│   ├── main.tex         # Main document that imports all components 
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

### Extensions

1. Install the [LaTeX Workshop](https://marketplace.visualstudio.com/items?itemName=James-Yu.latex-workshop) extension for Visual Studio Code
2. Install the [LaTeX Utilities](https://marketplace.visualstudio.com/items?itemName=tecosaur.latex-utilities) extension for Visual Studio Code
3. Install the [LaTeX Snippets](https://marketplace.visualstudio.com/items?itemName=blang.latex) extension for Visual Studio Code
4. Install the [LaTeX Preview](https://marketplace.visualstudio.com/items?itemName=ajshort.latex-preview) extension for Visual Studio Code.

### Build LaTeX

1. Open the `resume` folder in Visual Studio Code
2. Open the `main.tex` file in either the `academic` or `professional` directory
3. Click on the `Build LaTeX project` button in the top right corner of the editor
4. The generated PDF will be saved in the `same` directory
5. You can also use the `LaTeX Workshop` extension to build the project

## GitHub Actions

This repository is configured with a GitHub Actions workflow that automatically builds the LaTeX resumes on every push to the repository. The generated PDF files are saved in the `output` directory.

[![Build LaTeX Resumes](https://github.com/alphaNewrex/resume/actions/workflows/build-latex.yaml/badge.svg)](https://github.com/alphaNewrex/resume/actions/workflows/build-latex.yaml)

## License

This repository is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

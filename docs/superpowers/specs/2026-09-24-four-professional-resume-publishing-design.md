# Four Professional Resume Publishing Design

## Status

Approved for implementation.

## Goal

Build, validate, and archive four professional resume variants—SWE and MLE, each with Gmail and Georgia Tech headers—while continuing to build and archive the academic CV. Preserve the existing stable SWE Gmail and academic CV paths so current links remain valid.

## Source Architecture

The professional documents share all content except their role-specific experience/skills sections and selected header.

- `professional/main.tex` remains the SWE Gmail entrypoint.
- `professional/main_mle.tex` remains the MLE Gmail entrypoint.
- Both primary entrypoints define a default header path only when a wrapper has not already selected one.
- `professional/main_gatech.tex` is a thin wrapper that selects `sections/header_gatech` and inputs `main.tex`.
- `professional/main_mle_gatech.tex` is a thin wrapper that selects `sections/header_gatech` and inputs `main_mle.tex`.
- `professional/sections/header.tex` remains the Gmail header.
- `professional/sections/header_gatech.tex` contains `hgujarathi3@gatech.edu` as both its displayed address and `mailto:` target.
- `academic/main.tex` remains unchanged as the academic CV entrypoint.

The wrappers must not duplicate resume content. A content edit to the SWE or MLE entrypoint must therefore affect both email variants automatically.

## Published Outputs

### Stable latest paths

- SWE + Gmail: `output/Harsh_Gujarathi_Resume.pdf`
- SWE + Georgia Tech: `output/Harsh_Gujarathi_Resume_GeorgiaTech.pdf`
- MLE + Gmail: `output/Harsh_Gujarathi_MLE_Resume.pdf`
- MLE + Georgia Tech: `output/Harsh_Gujarathi_MLE_Resume_GeorgiaTech.pdf`
- Academic CV: `output/Harsh_Gujarathi_CV.pdf`

The first and fifth paths are existing public interfaces and must retain their current meanings.

### Dated archive paths

- SWE + Gmail: `output/professional/Harsh_Gujarathi_Resume_YYYY_MM_DD.pdf`
- SWE + Georgia Tech: `output/professional/Harsh_Gujarathi_Resume_GeorgiaTech_YYYY_MM_DD.pdf`
- MLE + Gmail: `output/professional/Harsh_Gujarathi_MLE_Resume_YYYY_MM_DD.pdf`
- MLE + Georgia Tech: `output/professional/Harsh_Gujarathi_MLE_Resume_GeorgiaTech_YYYY_MM_DD.pdf`
- Academic CV: `output/academic/Harsh_Gujarathi_CV_YYYY_MM_DD.pdf`

Dates use UTC. Different dates accumulate. A changed repeat build on the same UTC date replaces that date's five files.

## Build And Artifact Flow

- Source or content pull requests and every push to `main` compile all five documents.
- Each LaTeX entrypoint is compiled twice with `pdflatex -interaction=nonstopmode -halt-on-error` from its document directory.
- The build job has only `contents: read` permission.
- The Actions artifact is named `compiled-resumes` and contains exactly the five current dated PDFs.
- Missing output is a hard failure.
- `SOURCE_DATE_EPOCH` continues to derive from the UTC archive date so reconciliation builds are deterministic.

## Archive Publishing Flow

- Only the trusted `main`-push publish job receives `contents: write` and `pull-requests: write`.
- The existing `gh-actions/output` staging branch and PDF-only pull-request flow remain in place.
- The publisher receives five explicit artifact paths from the build job; it does not discover current artifacts using wildcards.
- Before publishing, every dated path must match one of the five canonical archive patterns and every artifact file must exist.
- Previously staged dated archives are preserved only when they match a canonical professional or academic archive pattern and are not already present on the candidate `main` commit.
- Each clean-runner dated artifact is copied to its matching stable latest path.
- The staged-path guard accepts only the five dated archive patterns and five exact stable latest paths. Any other staged path fails the job.
- Existing source-compatibility, ancestry, force-with-lease, and pull-request reconciliation protections remain unchanged.
- The generated pull-request description states that it archives four professional resume variants and one academic CV.

## Compatibility And Migration

- `output/Harsh_Gujarathi_Resume.pdf` continues to mean the SWE Gmail resume.
- `output/professional/Harsh_Gujarathi_Resume_YYYY_MM_DD.pdf` continues to archive the SWE Gmail resume.
- `output/Harsh_Gujarathi_CV.pdf` and its dated archive pattern remain unchanged.
- No existing archive is renamed or removed.
- The new Georgia Tech and MLE paths are additive.
- Generated PDFs remain ignored locally; CI alone force-adds canonical output paths.

## Documentation

Update `README.md` and `AGENTS.md` to document:

- all four professional entrypoints and the academic entrypoint;
- local compilation commands for every entrypoint;
- all five stable latest paths;
- all five dated archive patterns;
- five-file Actions artifacts and generated archive pull requests;
- the meaning of the existing stable SWE path.

## Verification

- Compile `professional/main.tex`, `professional/main_gatech.tex`, `professional/main_mle.tex`, and `professional/main_mle_gatech.tex` twice.
- Confirm all four professional PDFs contain exactly one page.
- Confirm Gmail variants contain `gujarathiharsh02@gmail.com` and not the Georgia Tech address.
- Confirm Georgia Tech variants contain `hgujarathi3@gatech.edu` and not the Gmail address.
- Confirm SWE variants contain the SWE skills and MLE variants contain the MLE skills.
- Compile `academic/main.tex` twice and confirm success.
- Parse the GitHub Actions YAML and run `actionlint` when available.
- Confirm the artifact upload enumerates exactly five dated paths.
- Confirm the publisher validates, copies, stages, and allows exactly five dated variants and five stable variants.
- Confirm the workflow retains the existing stale-source, output-only reconciliation, branch ancestry, and force-with-lease protections.
- Run `git diff --check` and inspect the final diff.

## Alternatives Considered

### Duplicate four full professional entrypoints

Rejected because shared resume content could drift between email variants.

### Select headers only through CI command-line macros

Rejected because local builds would be obscure and the four variants would not have discoverable entrypoint files.

### Parameterized entrypoints with thin wrappers

Selected because it keeps content centralized while making all four variants easy to build locally and in CI.

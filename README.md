# najchris11/resume

LaTeX resume under git version control, built and published automatically by GitHub Actions. Inspired by [resumake.io](https://github.com/saadq/resumake.io) and the desire for an automatic resume build system.

**Latest PDF:** [download here](https://github.com/najchris11/resume/releases/latest/download/najchris11_resume.pdf) (also mirrored on the [`resume-builds`](https://github.com/najchris11/resume/tree/resume-builds) branch).

## How it works

There are three LaTeX files, all sharing one preamble ([`resume-preamble.tex`](resume-preamble.tex)):

| File | Role |
| --- | --- |
| [`master-resume.tex`](master-resume.tex) | The canonical one-page resume. This is what gets released publicly. |
| [`augmented-resume.tex`](augmented-resume.tex) | Archive of all past resume content (multi-page, not meant to be published). Source material for tailoring. |
| [`resume.tex`](resume.tex) | A tailored variant generated per job application from the master + archive. Validated by CI but never released. |

### Workflows

- [`release.yml`](.github/workflows/release.yml) — on any push to `main` that changes `master-resume.tex` or the shared preamble: builds the PDF, fails if it isn't exactly one page, creates an auto-tagged GitHub release (title = commit message) with the PDF attached, and mirrors `master-resume.pdf` to the `resume-builds` branch.
- [`check.yml`](.github/workflows/check.yml) — on pull requests and pushes touching the other `.tex` files: builds every variant and enforces the one-page constraint on `resume.tex` and `master-resume.tex`, so a broken file never lands silently.

### Tailoring

`resume.tex` is generated from the master and archive against a local, untracked `job-posting.txt` using the rules in [`edit-prompt.md`](edit-prompt.md). Job postings are intentionally never committed.

## Building locally

Requires a TeX distribution (e.g. TeX Live / MacTeX):

```sh
latexmk -pdf master-resume.tex
```

The one-page check locally: `pdfinfo master-resume.pdf` (Linux) or `mdls -name kMDItemNumberOfPages master-resume.pdf` (macOS).

## Using this for your own resume

1. Fork the repo.
2. Enable Actions on your fork (Actions tab), and under Settings → Actions → General set workflow permissions to "Read and write permissions".
3. Put your content in `master-resume.tex`.
4. Push to `main`. In a few minutes the release appears with your PDF, and the permanent link `https://github.com/{you}/resume/releases/latest/download/{you}_resume.pdf` always serves the newest build.

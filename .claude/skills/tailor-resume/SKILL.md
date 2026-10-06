---
name: tailor-resume
description: Generate a job-tailored resume.tex from master-resume.tex and augmented-resume.tex against a job posting, then compile and verify it fits one page. Use when the user wants to tailor their resume for a job posting, application, or placement form.
---

# Tailor Resume

Generate `resume.tex` tailored to a specific job posting, following the rules in `edit-prompt.md`, and verify it compiles to exactly one page before finishing.

## Inputs

1. **Job posting** — resolve in this order:
   - Text or a file path passed as the skill argument.
   - Otherwise, the contents of `job-posting.txt` in the repo root.
   - If neither exists, ask the user to paste the posting, then save it to `job-posting.txt`.
   - `job-posting.txt` is untracked by design. Never commit it (`git add` must never include it).
2. **Source content** — read all of:
   - `edit-prompt.md` — the tailoring rules. Follow them strictly; they are the contract.
   - `master-resume.tex` — canonical content, primary source.
   - `augmented-resume.tex` — full archive; pull from it when the posting rewards content the master omits.
   - `resume-preamble.tex` — shared preamble; never modify it.

## Steps

1. Read all inputs above. Strip boilerplate from the posting per the preprocessing section of `edit-prompt.md`.
2. Write `resume.tex`. It must begin:
   ```latex
   \documentclass[letterpaper]{article}
   \input{resume-preamble}
   ```
   Never edit `master-resume.tex`, `augmented-resume.tex`, or `resume-preamble.tex`.
3. Compile and verify (the verification section of `edit-prompt.md`):
   ```sh
   latexmk -pdf -interaction=nonstopmode resume.tex
   grep "Output written" resume.log   # must say "(1 page"
   grep -c "Overfull" resume.log      # must be 0
   ```
   Do NOT use `mdls` for page counts — Spotlight metadata is cached and can report stale values.
   After the log checks pass, ALSO read the rendered PDF and visually confirm the layout
   (headers not colliding with content, no orphaned lines) before declaring success.
   Iterate — trim lowest-signal bullets if over one page, restore relevant content if badly underfilled — until the PDF is exactly one page with a clean log.
4. Summarize for the user: which sections/bullets were kept, cut, or rephrased, and which posting keywords drove those choices.
5. Do not commit or push unless asked. If asked, remember: pushing `resume.tex` triggers the validation workflow only — tailored resumes are never publicly released.

## Hard rules

- No fabrication: never invent or alter facts, dates, titles, metrics, or credentials. Graduation date is whatever `master-resume.tex` says.
- Keyword alignment must stay truthful — match the posting's terminology only where the underlying experience is real.
- One page, verified by compiling, never by estimating.

# Template: modern-onepage

- **Type:** CV
- **Source extension:** .tex
- **Engine/toolchain:** lualatex
- **Page limit:** 1 page(s)
- **Fonts:** Lato (bundled in `fonts/lato/` - Lato-Reg/Bol/RegIta/BolIta for body, Lato-Bla for the name header)
- **Class/packages:** `article` base class + `fontspec`, `xcolor`, `enumitem`, `titlesec`, `hyperref`, `geometry` (all standard, ship with `texlive-latex-extra`)

## Compile command

    cd <output dir> && lualatex -interaction=nonstopmode <file>.tex

## Style rules

- **Single column, ATS-safe.** No real multi-column/table layout. Every entry line is anchored to the same left position with a fixed-width `\datebox{}` (a `\makebox[3.3cm][l]{}`), which reads visually like a left date column but is still one sequential line of text - `pdftotext -layout` extracts it in natural reading order (date, then title, then org/location, then bullets).
- **Colors:** accent teal `#1F5C6B` for the name, section headings, section rules, and date labels; `#555555` for supporting text (org/location, bullet text); `#CCCCCC` for the thin rule under each section heading. Colors are purely visual and do not affect the ATS text layer.
- **Dates:** always the first element of an entry line, left-aligned in the fixed-width box, in the accent color. Use a single ASCII hyphen in date ranges (`2024-2026`, never `2024--2026`) - ligatured en-dashes break ATS date parsing on import, per the framework's known Workday-import failure mode.
- **Section order:** Header -> Profil -> Compétences Clés -> Expérience Professionnelle -> Formation -> Langues. Publications/Distinctions/Références can be appended in the same style if the role calls for them, but the 1-page budget rarely leaves room - cut before adding.
- **Bullets:** 1-2 per role, tightly budgeted (see Page Budget below). `enumitem` keeps `itemsep`/`parsep` at 0 so lists stay compact.
- **Header contact line:** phone, email (as a real `mailto:` link with the address as literal visible text, not icon-only), and LinkedIn - all plain text so they survive text-layer extraction.

## Page Budget (hard 1-page limit)

This template is much tighter than the stock 2-page moderncv budget - treat every section as a hard constraint, not a guideline:

| Section | Max budget |
|---------|-----------|
| Header | Name + tagline + contact line (3 lines) |
| Profile | 2-3 lines |
| Core Competencies | 4 categories, 1-2 lines total |
| Experience | 3 roles max, 1-2 bullets each (cut older/less relevant roles entirely - this is not the place for a full history) |
| Education | 2 entries, one line each |
| Languages | 1 line |

If content doesn't fit, cut roles/bullets by relevance to the target posting (see `05-cv-templates.md`'s "Relevance-weighted cutting") - do not shrink the font size or margins below what's declared here to force-fit.

## Known pitfalls

- **`\datebox` has a fixed width (3.3cm), sized for French month abbreviations** (e.g. "Avr 2026-Présent"). A longer date string (full month names, or a language with longer month words) will overflow into the title text - abbreviate months, or widen `\datebox`'s `\makebox` argument and the matching `\hspace*{}`/`leftmargin` values below if you do.
- **`fontspec` requires `lualatex` or `xelatex`.** Do not compile with `pdflatex` - it will fail on the `\setmainfont`/`Path=` font-loading syntax.
- **Bullet `leftmargin` (3.4cm) must stay slightly wider than `\datebox`'s width (3.3cm)** so bullet text doesn't visually collide with the date column above it. If the date box width changes, adjust `\setlist[itemize]{leftmargin=...}` and every `\hspace*{}` in `\cventry`/`\eduentry` to match - all three must agree or entries misalign.
- **The vertical gap before a `\begin{itemize}` block is controlled by the `\\[...]` skip at the end of `\cventry`, not by `enumitem`'s `topsep`.** Empirically (verified by test-compiling with `topsep` swept from `1pt` to `-10pt` with no visible change to the gap *before* the list, while the same sweep visibly closed - and at `-10pt`, collided - the gap *after* the list), the space between the org/location line and the first bullet comes from the line-break skip, not the list's own spacing parameters. `\cventry` therefore ends its last line with `\\[-8pt]` to pull a following `itemize` tight; `\eduentry` has no list after it and uses `\\[3pt]` instead - do not swap these or blindly reuse `\\[-8pt]` after an entry that isn't immediately followed by `\begin{itemize}`, or the next line will overlap it.
- **ATS reading order was verified empirically** after the test compile in this repo's sandbox (no LaTeX toolchain was pre-installed - `texlive-luatex`/`texlive-latex-extra`/`texlive-fonts-recommended`/`poppler-utils` were installed via apt to run the mandatory compile check) via `pdftotext -layout`: date/title/org/bullets all extract in correct visual order, no `(cid:)` or `�` markers, and date ranges confirmed to use ASCII hyphens (U+002D), not en-dashes. Re-verify with `pdftotext -layout` after any structural change to `\cventry`/`\eduentry`.

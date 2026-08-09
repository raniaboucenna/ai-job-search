# Search Queries for Job Scraper

## Installed portal CLIs (primary for `/scrape`)

`/scrape` discovers every portal skill under `.agents/skills/*/SKILL.md` and runs its CLI first. Shipped country-agnostic CLIs include `linkedin-search` and `freehire-search`; Danish demos ship disabled (not relevant to Rania's France/Canada search) and any skill added with `/add-portal` is included the same way. You do **not** need a matching `site:` line below for those CLIs to run.

The `site:` query templates in this file are the **WebSearch fallback** — for portals without a CLI, company career pages, or when a CLI fails.

**Language scope:** queries below are written in French (primary market and CV language) and English (useful for international/Canadian PhD postings and English-language R&D roles). Arabic and German are declared languages but are not used for query generation - the target job markets (France, Canada) don't run on those languages. See `04-job-evaluation.md`'s Language Gate for how declared languages affect scoring, independent of search-query language.

## Search Sites

Primary (France - scaffold with `/add-portal`):
- **apec.fr** - APEC (Association Pour l'Emploi des Cadres), primary board for ingénieur/cadre-level roles in France
- **linkedin.com/jobs** - LinkedIn job listings (filter: France / Rouen, Lille, Île-de-France); also covered by `linkedin-search` CLI
- **jobs.abg.asso.fr** - ABG (Association Bernard Gregory), the reference board for PhD/doctoral positions in France
- **indeed.fr** - broad secondary board for France

Secondary (company career pages via Google):
- Direct Google searches with `site:` filters for known target companies (UniLaSalle, INRAE, IFPEN, etc.)
- For Canada: university doctoral-position listing pages (e.g. `site:*.ca "doctorat" OR "PhD" biosourced materials`)

## Query Categories

### Priority 1: Thèse / Doctorat - Matériaux Biosourcés & Valorisation de la Biomasse

Rania's primary and most desired career direction: a PhD in biosourced materials or biomass valorization, in France or Canada.

```
site:jobs.abg.asso.fr "matériaux biosourcés" OR "biomasse"
site:jobs.abg.asso.fr "thèse" "procédés" biosourcé
"contrat doctoral" biomasse OR "matériaux biosourcés" France
"PhD" "biosourced materials" OR "biomass valorization" Canada
site:linkedin.com/jobs "doctorat" OR "PhD" biosourced OR biomasse
```

### Priority 2: Alternance - Ingénieur Génie des Procédés

Rania's fallback direction: an alternance to continue professionalizing in process engineering while funding her studies.

```
site:apec.fr "alternance" "ingénieur génie des procédés" Normandie OR Lille OR "Île-de-France"
site:indeed.fr "alternance ingénieur procédés" Rouen OR Lille OR Paris
site:linkedin.com/jobs "alternance" "génie des procédés" France
```

### Priority 3: Ingénieur R&D Matériaux / Procédés Développement Durable

Adjacent roles - non-alternance, non-thèse positions that still match her R&D/sustainability profile, in case a direct hire opportunity fits.

```
site:apec.fr "ingénieur R&D" "matériaux biosourcés" OR "développement durable"
site:apec.fr "ingénieur procédés" "développement durable" France
site:linkedin.com/jobs "process engineer" sustainability OR "biosourced materials" France
```

### Priority 4: Broader Génie des Procédés / QHSE

Wider net across general process-engineering and QHSE roles in France.

```
site:apec.fr "ingénieur génie des procédés" France
site:indeed.fr "ingénieur procédés" OR "ingénieur QHSE" France
site:linkedin.com/jobs "process engineer" OR "QHSE engineer" France
```

## Location Filter

When evaluating results, verify the job location is within Rania's accepted range:
- **Rouen and Normandie** - ideal, home base
- **Lille métropole** - acceptable, priority region
- **Île-de-France / Paris** - acceptable, priority region
- **Rest of France** - accepted (candidate is mobile across all of France)
- **Canada** - accepted, but **PhD/doctoral opportunities only** - requires visa/permit sponsorship, see `04-job-evaluation.md`'s Eligibility Gate

## Language Filter

Rania's working languages and levels are in CLAUDE.md's Languages table (French/Arabic native, English C1, German elementary). When filtering scraped results, apply `04-job-evaluation.md`'s Language Gate: a posting requiring a language she hasn't declared at all is excluded (e.g. German above elementary level); a posting requiring a higher level than declared in a language she does work in is not excluded, flag it clearly instead (see `job-scraper/SKILL.md`'s Step 3 "Quick Fit Assessment" for how the flag surfaces in `/scrape` output). Postings simply *written* in a language she doesn't work in, that don't require it on the job, are fine.

## Date Filter

Only include jobs posted within the last 14 days, or with an application deadline that has not yet passed. If a posting date cannot be determined, include it but flag as "date unknown".

## Adapting Queries

If the user specifies a focus area, select queries from the matching category and also generate 2-3 custom queries for that focus. For example:
- "/scrape thèse" -> Priority 1 category queries + custom queries naming specific labs/universities
- "/scrape alternance" -> Priority 2 category queries + custom queries for named target companies

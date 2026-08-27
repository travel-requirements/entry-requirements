# Entry Requirements Dataset

Entry requirements for **199 destinations** against **199 passports** — visas,
passport validity, vaccinations, mandatory insurance, arrival cards, customs
rules and the practical facts of arriving somewhere — compiled from official
government sources, with **the source URL and the date it was last checked
recorded against every documented fact**.

This is the source of truth behind [TravelRequirements.info](https://travelrequirements.info),
published so it can be reused rather than only read.

**Licence: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)** — see [`LICENSE`](LICENSE) for the legal text and [`NOTICE.md`](NOTICE.md) for what it covers.
Copy it, adapt it, use it commercially, including as training or retrieval data.
The one condition is credit:

```
TravelRequirements.info — Entry Requirements Dataset,
AXG Sp. z o.o., https://travelrequirements.info/data/ (CC BY 4.0)
```

## Layout

| Path | Contents |
| --- | --- |
| `destinations/{slug}.json` | 199 canonical records — one country each. The visa regime for every passport, entry requirements, visa types with official fees, country facts, and a source object behind each documented fact |
| `NOTICE.md` | What the licence covers, and the credit line to use |
| `schema.json` | JSON Schema (draft 2020-12) for a destination record. Generated from the Zod schema the site validates against, so it cannot describe a shape the data does not have |
| `passports.json` | The 199 nationalities in the matrix: ISO 3166-1 alpha-2 code, name, demonym |
| `regions.json` | Region groupings |
| `changelog/YYYY-MM.json` | The public change log: every recorded change, re-verification and source replacement |
| `official-hosts.json` | Non-government hosts explicitly allowed as official application portals, each with its operator recorded |
| `yellow-fever.json` | WHO yellow-fever transmission-risk countries, which is what makes "required when arriving from a risk country" answerable |
| `i18n/{lang}/` | Translation overlays. Strings only — an overlay can never change a fact |
| `exchange-rates.json` | Static rates for the site's currency converter. Not part of the entry-requirements data |

## Derived distributions

Flat files built from these records, published on the site and refreshed with
every deploy:

- **[`/data/index.json`](https://travelrequirements.info/data/index.json)** — the
  manifest: licence, citation line, coverage counts, machine-readable caveats,
  and the URL of every file. Start here.
- **[`/data/visa-matrix.csv`](https://travelrequirements.info/data/visa-matrix.csv)** —
  39,601 rows: requirement, maximum stay, applicable visa types, how the stay is
  counted, and the source behind each row.
- **[`/data/sources.csv`](https://travelrequirements.info/data/sources.csv)** —
  3,144 records: every source with **the path of the exact fact it backs**, its
  type, and its verification dates.

Human documentation: <https://travelrequirements.info/en/data/>

## Using it

Validate a record:

```bash
npx ajv-cli@5 validate --spec=draft2020 -c ajv-formats \
  -s schema.json -d 'destinations/*.json'
```

Every documented fact carries a `source` object:

```json
{
  "url": "https://www.customs.go.jp/english/",
  "name": "Japan Customs",
  "type": "government",
  "lastVerified": "2026-08-25",
  "confidence": "high"
}
```

`lastVerified` moves on every check, whether or not anything changed.
`lastChanged` is set only when the value itself changed, which is what makes the
site's `dateModified` honest rather than a rebuild timestamp.

## What this dataset does not claim

These ship as a machine-readable `caveats` array in
[`/data/index.json`](https://travelrequirements.info/data/index.json) too, so
nothing depends on anyone reading this file.

1. **The matrix is not 39,601 independent citations.** Every row carries a source
   and a date, but `source_level` says which kind: `row` means that cell was
   cited individually (210 rows), `policy` means it rests on the destination's
   policy-level source (39,391 rows).
2. **Some sources are official but second-hand** — one government's page
   describing another country's rules. The source URL always says which
   government published the page.
3. **`confidence` is an internal editorial grade, not a probability**, and it is
   not assigned uniformly across the corpus. Source type and verification date
   are the reliable signals.
4. **A missing optional module means "not researched yet", not "does not
   apply".** Each record's `meta.modules` lists what was actually researched.
5. **The source is the authority, not this dataset.** Immigration rules change
   without notice and the final decision rests with border authorities. Do not
   use this as the sole basis for a travel, boarding or admission decision.

## How it is maintained

Re-verification runs in batches, oldest check date first. Every cited fact is
compared against its official source again; if nothing changed the verification
date moves, and if something changed the fact is corrected, the change date
recorded, and the change published in `changelog/`. Full methodology:
<https://travelrequirements.info/en/methodology/>

Links to apply for a visa or authorisation must resolve to a government or
intergovernmental domain, or be listed by name in `official-hosts.json` with the
operator recorded, or the site does not build. There is no visa service behind
this dataset and no application anyone is being steered toward.

## Corrections

Open an issue, or email <contact@travelrequirements.info> with the record and the
official source that contradicts it. Corrections are logged publicly in
`changelog/`.

## History

This repository carries the full commit history of the data directory, so any
fact can be traced to the day it changed and to the check that changed it.

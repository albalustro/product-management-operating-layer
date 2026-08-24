# signals/

This folder is the evidence entry point for the signal agents in this
repository (like **Radar**). Since these agents only have read tools
(`Read`, `Grep`, `Glob`), they don't fetch data from external systems on
their own — you need to put (or export) the material here before
requesting an analysis.

## Suggested structure

```
signals/
  support/        # support ticket exports, chat transcripts
  sales/          # sales call notes, objections, lost deals
  research/       # user interviews, usability tests, surveys
  analytics/      # product reports/exports (funnels, retention, usage)
  market/         # competitive analysis, public reviews, analyst reports
  misc/           # any other signal that doesn't fit above
```

None of these subfolders is mandatory — create only what makes sense for
the signal you're analyzing. Each file should be readable text (`.md`,
`.txt`, `.csv`, `.json`) so the agent can read it and cross-reference
sources.

## Sensitive data

If files here contain personal customer data (names, emails,
transcripts), consider anonymizing before committing, or keep the folder
(or specific subfolders) out of version control via `.gitignore` and treat
this directory as a local workspace.

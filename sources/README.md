# sources/

The external source obligations this project derives from: standards, regulations, contracts,
stakeholder specifications. `SRC-x.y` numbering follows each source's own section numbering.

- `originals/` — the files as received (PDF, spreadsheet, scan). Never edited.
- One **machine-readable extraction** per original, in this directory, which is what every skill
  reads. Each opens with:

```
## Provenance
original:   originals/<file>
extracted:  <script or method, with the command to regenerate>
date:       <date>
checked:    <what was verified against the original, and how>
feeds:      <artifacts derived from this extraction>

## Could not extract
<every table, figure, diagram or formula lost or flattened — or "none found", explicitly>
```

A gap found downstream is fixed **here, once** — consumers never read the original to work around it
(`CONVENTIONS.md` §3). When an extraction is corrected, every artifact listed under `feeds` is
re-checked.

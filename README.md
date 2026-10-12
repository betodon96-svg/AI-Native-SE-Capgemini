# AI-native systems engineering — starter kit

A self-contained folder for starting a new system project that is engineered by an **orchestrated set
of specialized AI agent skills**, under traceability, independent review and configuration
discipline. Copy the folder, make it the root of a new repository, fill in the placeholders, add
your sources, and run the skills.

The kit carries no product, no client and no source obligations. Everything project-specific is a
placeholder for you to replace.

## What is in it

| File / folder | What it is |
|---|---|
| [`CLAUDE.md`](./CLAUDE.md) | The session entry point: posture, **constraints (placeholders)**, document index, session-start protocol, and the order the skills run in. Thin by design |
| [`CONVENTIONS.md`](./CONVENTIONS.md) | The single shared definition of every convention: traceability, sources, status, change control, logging, review, skill-writing, diagramming |
| [`METHOD.md`](./METHOD.md) | The method and the reasoning behind the conventions |
| [`DECISION_LOG.md`](./DECISION_LOG.md) | Owner rulings, including reversals |
| [`.claude/skills/`](./.claude/skills/) | 18 skills: create / modify / review for requirements, use cases, architecture and eval sets; review for code; create / update for the roadmap; three log writers |
| `sources/` `intermediary/` `requirements/` `use-cases/` `architecture/` `roadmap/` `open-questions/` `evals/eval-sets/` `implementation/` `logs/` | Artifact homes, each with a README saying what goes there and which skill writes it |

## Starting a project from it

1. **Copy the folder** to a new location, and make it the root of a new repository (`git init`).
2. **Fill in `CLAUDE.md`**: the "What this project is" paragraph and the **non-negotiable
   constraints**. `create-architecture-doc` stops while the constraints are placeholders, by design.
3. **Record adoption** in `DECISION_LOG.md` (`DEC-001`), together with any convention you change.
   Set `CONVENTIONS.md`'s header.
4. **Add the source obligations**: originals in `sources/originals/`, and a machine-readable
   extraction of each in `sources/`, with its provenance and "could not extract" blocks
   (`sources/README.md`). Every downstream skill reads the extraction, never the original. A gap is
   fixed in the extraction, once.
5. **Run the skills in order**, as listed in `CLAUDE.md`: requirements, then use cases where flows
   matter, then architecture, then the roadmap, and then per module: eval set **before** code, review
   in a fresh session, fix, re-review until a pass is clean, update the roadmap and log.
6. **Run every `review-…` skill in a fresh session** that did not produce the artifact. In Claude Code
   that means a new session, or a subagent given only the review skill and the pinned revision.

## What to expect

- **The skills will need refining for your project, and that is the method working, not failing.**
  The customization surface is narrow: the sources, the access path to context, and where outputs are
  written. When a skill produces something unusable, log it in the dev log and change the skill.
- **Nothing advances itself.** Every artifact starts `draft`. A reviewer can only say an artifact is
  *eligible* for `reviewed`. The owner rules.
- **Guidance is not control.** The conventions are prose, and prose can be skipped. When a rule is
  skipped twice and must always hold, promote it to a hook, a permission or a test
  (`CONVENTIONS.md` §8).

## Known gaps in the kit

- **No source-ingestion skill.** Extraction depends too much on the source format to template.
  `sources/README.md` defines what an extraction must carry, and the skill for your sources is a good
  first candidate to write, following the lifecycle in `METHOD.md` §2.3.
- **No evals for the skills themselves.** Every artifact class has an independent check except the
  skills (`METHOD.md` §3.8). Log what each run cost and how it performed, so the evidence exists when
  you build one.
- **No hazard-analysis or stakeholder-needs artifact** (`METHOD.md` §4.4). Add them if your domain
  needs them.

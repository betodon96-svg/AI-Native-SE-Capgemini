# Project entry point — conventions and pointers

This file is **thin on purpose**. It holds posture, constraints and pointers, and it **never holds
artifact content**: no requirement, no design decision, no eval case. The reason is adversarial
rather than aesthetic. This is the most frequently read and edited file in the repository, so if it
could hold content, an edit here could overwrite a requirement without appearing in that
requirement's revision history. If you are about to write requirement content into this file, stop
and go to `requirements/`.

---

## What this project is

> *[Replace this block at project start.]* One paragraph: the system being built, who it is for,
> what its source obligations are (a standard, a regulation, a contract, a stakeholder
> specification), and what its core output is. Say whether correctness against the source *is* the
> product or is one quality attribute among several.

## Non-negotiable constraints

> *[Replace at project start. Until these are real, `create-architecture-doc` will stop at its
> constraints gate, by design.]*

- `CONSTRAINT-1` — *(placeholder)* e.g. whether the delivered system may run model inference at
  runtime. If it must not, say so; that is the strongest available boundary on non-determinism.
- `CONSTRAINT-2` — *(placeholder)* Any datastore, protocol or platform the system is obliged to use.
- `CONSTRAINT-3` — *(placeholder)* Any given external interface (existing hardware, an upstream
  system) that is consumed, never redesigned.
- `CONSTRAINT-4` — *(placeholder)* Anything the system is explicitly required **not** to do.

An artifact that violates a stated constraint is wrong regardless of how well-formed it is. Out of
scope and deferred items are recorded in the requirements document with a deferred marker, never only
here.

## Conventions

Traceability (`SRC-x.y → REQ-nnn → EVAL-nnn → MODULE-x`), status and honest marking, change control,
logging, checking and review, skill-writing rules and the diagramming standard are defined **once**,
in [`CONVENTIONS.md`](./CONVENTIONS.md). Every skill and artifact follows it by reference and none
restates it. The reasons behind it are in [`METHOD.md`](./METHOD.md).

## Document index

| Content | Location | Change-control pattern (`CONVENTIONS.md` §5) |
|---|---|---|
| Conventions, **read first** | [`CONVENTIONS.md`](./CONVENTIONS.md) | A, with revision history |
| The method and its rationale | [`METHOD.md`](./METHOD.md) | Reference |
| Owner rulings, including reversals | [`DECISION_LOG.md`](./DECISION_LOG.md) | A |
| Source obligations: originals, and machine-readable extractions | [`sources/`](./sources/) | Generated |
| Intermediary working artifacts | [`intermediary/`](./intermediary/) | C |
| Requirements document, the **master document** | [`requirements/`](./requirements/) | A |
| Open-question dossiers (detail behind requirements open items) | [`open-questions/`](./open-questions/) | A |
| Use-case document | [`use-cases/`](./use-cases/) | A |
| Architecture document (C4 + UML, Mermaid) | [`architecture/`](./architecture/) | B |
| Roadmap (Now / Next / Later) | [`roadmap/`](./roadmap/) | B |
| Eval sets | [`evals/eval-sets/`](./evals/eval-sets/) | A |
| Eval log (test runs **and** review passes) | [`logs/eval-log.md`](./logs/eval-log.md) | A |
| Dev log | [`logs/dev-log.md`](./logs/dev-log.md) | A |
| Bug / change log | [`logs/bug-change-log.md`](./logs/bug-change-log.md) | A |
| The system being built | [`implementation/`](./implementation/) | Git; logs reference it by commit SHA |
| Skills | [`.claude/skills/`](./.claude/skills/) | Revised after each run |

## Session-start protocol

Read these at the start of **every** session, before anything else. The list is short on purpose, so
that it actually gets done:

1. [`CONVENTIONS.md`](./CONVENTIONS.md).
2. The requirements document, **in full, including its open-items section**. It is the master
   document: when it and any other artifact disagree, it wins. Read it whole because the open items
   and cross-module blockers are exactly what a scoped read skips.
3. The roadmap: what is in flight, what is next, and what each module is blocked by.

Read the rest **on demand, scoped to what you are touching**:

- the architecture, when designing or changing a module (it is derived, not a session-start read);
- eval sets, logs and dossiers, when working on the specific thing they cover;
- source extractions, when deriving or reviewing a requirement, always directly and never through
  another document's summary of them;
- `METHOD.md`, when the reason behind a convention is in question.

Reading everything every session produces perfunctory reading. Until the requirements document and
roadmap exist, items 2 and 3 are empty, and the first job is to create them (below).

## Producing the artifacts — the order the skills run in

Skills are not references to consult if remembered. They run in this order, and **skipping a step is
a process defect, not a shortcut.** **Read each skill from its file at the point of use**, not from
memory of what it says (`CONVENTIONS.md` §8).

**Establishing the project** (once, then as sources change):

1. Put the source obligations in `sources/originals/` and their machine-readable extractions, with
   provenance and "could not extract" blocks, in `sources/` (`CONVENTIONS.md` §3).
2. `create-requirements-doc` → `review-requirements-doc` (fresh session) → `modify-requirements-doc`
   → re-review until a pass returns nothing material.
3. `create-use-case-doc` → `review-use-case-doc` → `modify-use-case-doc`, where end-to-end flows
   matter.
4. `create-architecture-doc` → `review-architecture-doc` → `modify-architecture-doc`.
5. `create-roadmap`.

**Building a module** (per `MODULE-x`):

1. **Its requirements exist and are at least `draft`.** If not, go back to step 2 above.
2. **`create-eval-set`**: specify the `EVAL-nnn` cases **before** implementing. The specification
   table comes first, with no test code. Cases written after the code are shaped by what the code does
   and what was convenient to test, and holes in such a set cannot be found by re-reading it.
3. **Implement** the module, then implement the specified tests. There is no `create-code` skill by
   design: writing code is ordinary development, tracked by the roadmap.
4. **`review-eval-set`** and **`review-code-change`**, both in a fresh session with no context shared
   with whoever built the module. **Pin the revision** (`CONVENTIONS.md` §7.4).
5. **Split the findings and route each half.** What will not be fixed here goes upstream, to the
   requirements open items (via `modify-requirements-doc`) and the roadmap (via `update-roadmap`).
   Defects go to step 6.
6. **Apply the fixes and re-review the changes.** Return to step 4, scoped to what changed. Stop when
   a pass returns nothing material, not after a fixed number of rounds.
7. **`update-roadmap`** and **`create-dev-log-entry`**.

**At any point:** `create-eval-log-entry` whenever a check actually runs, `create-bug-log-entry`
whenever a defect is found or a change decision is made, and `create-dev-log-entry` at the end of
every work session.

## Skill index

| Artifact | Create | Modify | Review |
|---|---|---|---|
| Requirements | `create-requirements-doc` | `modify-requirements-doc` | `review-requirements-doc` |
| Use cases | `create-use-case-doc` | `modify-use-case-doc` | `review-use-case-doc` |
| Architecture | `create-architecture-doc` | `modify-architecture-doc` | `review-architecture-doc` |
| Eval sets | `create-eval-set` | `modify-eval-set` | `review-eval-set` |
| Code | *(ordinary development)* | *(ordinary development)* | `review-code-change` |
| Roadmap | `create-roadmap` | `update-roadmap` | *(none: checked for consistency, not re-derived)* |
| Logs | `create-eval-log-entry` · `create-dev-log-entry` · `create-bug-log-entry` | *(append-only: never modified)* | *(none)* |

**One warning, the most important sentence in this file.** Writing a rule down does not make it hold.
Everything here is *guidance*: it is read and weighed, and it can be skipped, most easily by whoever
wrote it. A rule that must always hold and can be checked mechanically belongs in enforcement (a hook,
a permission, a test). When an instruction here is skipped more than once, promote it rather than
wording it more firmly (`CONVENTIONS.md` §8).

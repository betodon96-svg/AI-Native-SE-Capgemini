# Conventions Reference

```
status: draft
last-updated: <date>
reviewer: (none yet)
```

This is the **single, shared definition** of the conventions every artifact and every skill in this
repository follows: traceability, sources and intermediates, status and marking, change control,
logging, checking and review, how skills are written and run, and diagramming. Skills **point to
this document** rather than restating it. A rule found duplicated elsewhere is a defect: delete the
duplicate and point back here.

The reasons behind these rules are in [`METHOD.md`](./METHOD.md). This document says *what holds*;
that one says *why*. When a rule here seems wrong for your project, read the reason before changing
it, and record the change in `DECISION_LOG.md`.

This document follows its own rules (status field, revision history at the end).

---

## 1. Purpose and posture

The project may have one accountable person, or a small team, in place of an organization staffed
with separate authors, reviewers, a change control board and independent verifiers. These
conventions exist to arrange what that structure used to supply, and to leave audit evidence. Every
artifact must be able to answer, later and under scrutiny: *what did this say on date X, why did it
say that, and what shows it is correct?*

- **The owner sets direction and adjudicates. Agents produce and check.** The owner sets the
  question, the scope and the boundaries, supplies and selects the sources, rules wherever sources
  conflict, and accepts or rejects each review finding. None of that is delegated silently to an
  agent.
- **Nothing an agent produces is approved by that agent.** Every artifact starts `draft`. Status
  advances through independent review and an explicit decision by the owner (§4, §7).
- **An absent source is a stop, not a gap to fill.** If the material an artifact derives from is not
  in the repository, say so and stop. Do not draft from recollection of what a source "probably"
  says.
- **The owner's own drafts get the same review as an agent's.** Human authorship is not an
  exemption.

## 2. Traceability

One chain runs through every artifact:

```
SRC-x.y  →  REQ-nnn  →  EVAL-nnn(a/b/…)  →  MODULE-x
                 ↘ UC-nnn (use cases reference REQ IDs)
```

| ID | Means | Format | Lives in |
|---|---|---|---|
| `SRC-x.y` | A clause of an external source obligation: a standard, a regulation, a contract, a stakeholder specification | Matches the source's own section numbering | `sources/` |
| `REQ-nnn` | A specific, testable statement of what the system must do to satisfy one or more source clauses | Three digits, zero-padded, sequential project-wide | `requirements/` |
| `UC-nnn` | An end-to-end interaction flow satisfying one or more requirements | Three digits, zero-padded, sequential | `use-cases/` |
| `EVAL-nnn(a/b/…)` | A case establishing that `REQ-nnn` is met | Same number as its requirement, lowercase suffix when more than one case (`REQ-014` → `EVAL-014a`, `EVAL-014b`) | `evals/eval-sets/` |
| `MODULE-x` | A named component of the system being built | Short kebab-case name, not numbered: components are named, not sequenced (e.g. `MODULE-record-export`) | `implementation/`, named in the architecture's component view |

**Rules:**

- **Many-to-many is normal.** One requirement may rest on several clauses; one module may implement
  several requirements; one requirement may need several modules. Record every link; never assume
  1:1.
- **IDs are never reused, reordered or renumbered.** A retired item is marked retired in its revision
  history, not deleted, and its ID is never reassigned.
- **Reference by ID, never by restating content.** References to implementation changes carry the ID
  *and* a commit SHA (§6).
- **Both directions must work.** Forward: every source clause reaches a requirement. Backward: every
  requirement cites the clause it rests on. A requirement with no source is unexplained scope; a
  source clause with no requirement is a gap. Both are findings.
- **Open items live in the requirements document**, as `REQ-nnn` entries in its open-items section,
  stating what is unknown, what it blocks and who must resolve it. Analysis longer than a few lines
  goes in a dossier in `open-questions/`, pointed to from the entry. No other artifact is a valid
  final home for an open item.
- **`CLAUDE.md` never originates an ID.** It may point at IDs; it never defines a requirement, an
  eval case or a module link.

**A value that appears in two artifacts is generated in at least one of them.** Copying a count, a
table or a list between artifacts commits you to synchronizing them by hand, and that will not
happen. Every derived artifact opens with a **provenance block** naming what it is derived **from**,
what **feeds on** it (so a change has a visible blast radius), and the **regeneration command**, or
an explicit *not generated: rebuilt by re-running <skill>* where it is a judgement rather than a
script output.

## 3. Sources, and the intermediate layer

**Source obligations are ingested once, into a machine-readable form, and every consumer reads that
form.** The original file (a PDF, a scanned standard, a spreadsheet) goes in `sources/originals/`. A
text extraction goes beside it in `sources/`, and opens with:

- a **provenance block**: original file, extraction method or script, date, and what was checked;
- a **"could not extract" block**: everything the extraction lost or flattened, such as a table read
  as prose, a figure, a diagram, or a formula. An empty block must say *none found* explicitly.

**A lossy intermediate is fixed upstream, once.** When a skill finds that its input lost something it
depends on, it **stops and sends the gap back to the extraction**. It does not go and read the
original itself. Two reasons:

- A consumer that reads the original creates a second, divergent reading of the source, which is
  exactly what deriving through a shared intermediate exists to prevent.
- If every consumer works around the loss, the intermediate stops being load-bearing, every
  downstream skill pays the ingestion cost again, and the loss is rediscovered independently each
  time.

Where the gap cannot be closed in time, the consumer **declares the dependency unchecked** in its own
output and proceeds. Declaring it is acceptable. Silently deriving from a known-lossy input is not.

**When an intermediate is corrected, every conclusion drawn from it is re-derived.** A conclusion
reached on the uncorrected input is stale even if it reads as settled, and it does not become right
because someone later "confirms" it from memory. List the artifacts that consumed the intermediate
(its provenance block names them) and re-check each.

**`intermediary/` is a first-class layer, not scratch work.** It holds surveys, extraction notes,
vocabulary reconciliations, coverage matrices and reviewers' independent derivations. Its contents
are structured **for the next machine step, not for a reader**: a skill's output must be consumable
by the next skill without a human in between. It is also where disagreement between sources becomes
visible *before* anything is merged. A pipeline without this layer collapses reconciliation into
assembly, and conflicts get silently resolved by whichever source happened to be read first.

## 4. Status, and honest marking

Every artifact, and every individually-addressable item within it, carries:

```
status: draft | reviewed | approved
reviewer: <who or what reviewed it, and the log entry>
```

| Status | Means |
|---|---|
| `draft` | Written, not independently checked. **Default for everything an agent produces** |
| `reviewed` | An independent review pass found nothing material (§7). Set by the owner on a reviewer's recommendation, never by an agent |
| `approved` | The owner has explicitly accepted it. Only `approved` content goes into a named baseline (§5) |

A `reviewed` or `approved` status with no reviewer identified is invalid and is treated as `draft`.

- **Unreviewed content is marked at the point of the claim** with `[PRELIMINARY]`, not only in the
  header. A reader landing mid-document, or reading a pasted excerpt months later, must be able to
  see that it has not been checked. Remove the marking as part of the review that clears it.
- **Ambiguity is stated inline, never resolved silently:**
  `[PRELIMINARY — ambiguous: the source does not specify X]`. An assumption presented as settled is
  the most expensive defect an artifact can carry.
- **Any value an author had to invent** is marked `[ASSUMPTION]` and listed for the owner to rule on.
- **Never collapse *not yet checked* into *checked and fine*.** Any field recording a verification
  outcome, whether in a document, a data model or code, is three-valued: pass, fail,
  not-yet-verified. That applies wherever not-yet-verified is a reachable state. A boolean whose
  default reads as reassuring is a defect.

## 5. Change control

**What the discipline protects is the ability to discover that a question was already settled. The
text that settled it is not the thing being protected.** So the treatment depends on what the artifact
is, and one question selects it:

> **Is this artifact the record of something, or the current statement of something?**

A record's history *is* its content; editing an entry destroys the evidence it exists to carry. A
statement's history is metadata about how it was reached, and belongs beside it.

**Pattern A: append-only record.** Content is never overwritten. A change appends a dated revision
carrying its rationale. Applies to:

- **Logs**, including every eval and review log, dev log, bug/change log and decision log. A null
  result is evidence and only an append-only log can carry it.
- **Item registers**: the requirements document, the use-case document and eval sets. Each item
  carries its own ID, status and revision history. The file is edited in place at a stable path, but
  **an item's prior revision is never edited away**. A register's items are referenced by ID from the
  moment they exist, so they are records from their first entry.
- **Reversals.** When a question is decided and then decided differently, both decisions survive in
  `DECISION_LOG.md`, or the question gets silently re-litigated.

**Pattern B: living statement with its rationale kept beside it.** The artifact describes *current*
state and is edited in place. Git holds prior states, so there are no archive copies and no numbered
files. What keeps it auditable is an append-only record of what changed and why, kept beside the
statement:

- **Roadmap.** Buckets are edited in place, and every change appends a revision row saying what moved
  and why.
- **Architecture.** This is a single coherent statement whose sections must agree with each other. It
  adds one obligation: a **"Changed since v(n−1)"** section, rewritten each version, recording what
  changed and why, **what was checked for consistency and found still valid**, and **what could not be
  verified**. The middle item is the whole pattern. A diff shows what changed, never what the author
  examined and found still correct, and in a document whose sections must agree, changing one can
  silently invalidate another.

**Pattern C: superseding draft with retained intermediaries.** While an artifact is being drafted
and has not yet been accepted, replacing a passage with better text derived from it **is the work**,
not a loss of history. The superseding chain is retained in `intermediary/` and in version control,
and the artifact under construction stays readable. Appending corrections to an unaccepted draft
instead produces scar tissue: a document that narrates its own editing history to a reader who wanted
the current argument. Applies to `intermediary/` and to any non-register document before its first
acceptance.

**Generated artifacts are none of these.** Their history belongs to their generator. Hand-editing a
generated file does not preserve history; it breaks regeneration. Change the generator or its input
and regenerate (§2).

**Rules that cut across all three patterns:**

- **Choosing wrongly is asymmetric.** Too permissive destroys evidence, which is unrecoverable. Too
  restrictive destroys readability, which is recoverable. When unsure, Pattern A.
- **A superseded revision is not inert.** Until it is marked *at the point of the claim*, not merely
  in its header, it stays a confident, searchable source of the answer that was withdrawn. Keeping
  history and quarantining it are two obligations, not one.
- **Point, never restate.** An artifact that copies another's content becomes a second source of
  truth that drifts. Reference the owning document and ID instead. A copy that is accurate today is
  still a defect, because nothing keeps it accurate.
- **Baselines are a deliberate, named act by the owner**, never a side effect of an edit. A named
  baseline (e.g. `v1.0 — submitted for validation`) is a tag over the revision history, not a forked
  copy, and contains only `approved` content. An agent may say that a change warrants a new
  baseline. It does not cut one.
- **Every revision entry records** date, author (human, or AI-assisted plus who reviewed), what
  changed, and why (§6).

## 6. Logging and rationale

| Log | Holds | Written by |
|---|---|---|
| `logs/eval-log.md` | Every eval run: test-suite executions **and** independent review passes, pass or fail | `create-eval-log-entry`, and the review skills |
| `logs/dev-log.md` | Per work session: what was done, by which skill, what it cost, what is queued next | `create-dev-log-entry` |
| `logs/bug-change-log.md` | Defects found, and change decisions about artifacts not already captured by an artifact's own revision history | `create-bug-log-entry` |
| `DECISION_LOG.md` | Owner rulings that settle a question: source conflicts, scope boundaries, terminology, convention changes, **including reversals** | The owner, or an agent recording a ruling the owner made |

All four are Pattern A: newest first, **a prior entry is never edited**. A correction is a new entry
that references the old one.

**The rationale convention, used everywhere a "why" is recorded:**

- **What** changed or happened, **why** (the triggering event: a defect, a corrected reading of a
  source, a stakeholder request, a scope decision, a review finding), and **what it affects** (every
  ID touched).
- Other artifacts are referenced **by ID**, implementation changes **by commit SHA**, never by
  pasting their content. Example: `Fixes REQ-014 (see MODULE-record-export @ a1b2c3d)`.
- Written for a reader with none of today's conversational context, who has only this entry, the
  IDs it names and the commits it cites.

**Null results are logged.** A review pass that found nothing material still gets an entry. A check
that has never failed is indistinguishable from one that *cannot* fail unless its runs are on record.

**Cost is logged.** Record elapsed time, tokens or tool calls where available, and errors. A correct
artifact is not evidence of a sound skill, and this record is the only instrument that separates the
two (§8).

## 7. Checking, review and evals

### 7.1 The regime depends on whether the artifact has an oracle

An **oracle** is something that objects when the artifact is wrong.

| Artifact class | Oracle | Its eval |
|---|---|---|
| **Executable**: code, scripts, diagram-as-code | Yes: the suite fails, or the renderer fails | Structural checks · a deterministic test suite traced to requirements, run on every change · mutation · independent review |
| **Knowledge**: requirements, use cases, architecture, plans, reviews | **No** | Structural checks · **independent review, which is this class's eval** · provenance and honest marking |

- **Tier 0, structural checks, applies to both classes** and runs first. It covers ID format, status
  field, revision entries and provenance blocks, and **whether the artifact runs or renders against
  its real consumer**, not only whether it parses as text. Some defects are invisible to any amount
  of careful reading and appear only on execution.
- **Where there is no oracle, do not simulate one.** A test-shaped check on a knowledge artifact that
  cannot fail is not a weak check. It is a non-check.
- **Review of a knowledge artifact *is* its eval.** It is a derivation or audit protocol with a
  recorded expectation, and `logs/eval-log.md` is its run record.

### 7.2 Choosing the review tactic

The tactic depends on one question: **do you hold a separable obligation rich enough to re-derive the
artifact from?**

| If | Tactic | Because |
|---|---|---|
| Yes: a separate source written by someone else or at another time, stating enough to determine the artifact (a regulation or standard for requirements; the requirements for an architecture; requirements and use cases for code or eval sets) | **Regenerate-and-diff.** Derive independently from the obligation, **write the derivation down** in `intermediary/` *before* opening the draft, then diff | The highest-value defect is an **absence**, and you cannot find a missing thing by reading the things that are present |
| No: the obligation is descriptive prose, written by the same authors, or too thin to support deriving the whole artifact | **Conformance audit against an external rubric** (for requirements, the INCOSE requirement-quality characteristics), plus anchoring the artifact to its source in both directions | Re-derivation from an insufficient source produces thin output, and the "absences" it reports are artifacts of the derivation, not defects in the artifact |

**Record which tactic was used and why.** Under regenerate-and-diff, writing the derivation first is
what makes independence auditable: a reader can see what the reviewer expected independently of what
it found. If the derivation was not recorded first, the review does not claim independence. Under a
conformance audit, independence comes from the rubric being external: it was not written by the
authors, not derived from the artifact, and not produced by the reviewer.

### 7.3 Independence, and the principle beneath it

> **Any check that draws on the same source as the thing it checks degenerates into a tautology, and
> the degeneration is not visible from inside the check.**

For any verification, ask what source it draws on and whether that is the source of the thing being
verified. Consequences:

1. **If you produced it, you do not review it.** A review invoked in the session that produced the
   artifact stops and asks to be run fresh. A fresh session is necessary but not sufficient: a
   reviewer that reads the draft first inherits its framing.
2. **A test never derives its expectation from the code under test.** It states the value from the
   source obligation, independently.
3. **The author does not choose the mutations that test their own tests.** The reviewer does.
4. **An author's list of what they "checked and found still valid" is a claim to spot-check, not
   evidence.**
5. **More reviewers is not more review.** Agents given identical context and an identical prompt are
   one check reported several times, and the repetition looks like corroboration. A second opinion
   needs **different input**. In ascending strength: a different stance (weakest), a different subset
   of the sources, a different model. Agent count is not a quality dial.

### 7.4 Running a review

- **Pin the revision, not just the tree.** Tell the reviewer the exact commit it must be at, and give
  it a content check it can run: "`git rev-parse --short HEAD` must print X, and `ls <a file that
  exists only at X>` must succeed." A path check passes silently against the wrong revision.
- **Check the artifact, not an account of it.** A claim is verified against its source, not against
  something that describes the source: a draft's summary of itself, a commit message, a notification,
  or a review's own summary. **When a summary and an artifact both exist, read the artifact.**
- **Re-run, do not trust.** An earlier "all passing" claim is re-established by running the suite,
  and running it against the mandated technology, not a convenient substitute.
- **Report; do not repair.** A reviewer lists findings concretely (what it derived or what the rubric
  requires, what the artifact says, why they differ) and routes the fix through the relevant
  `modify-…` skill. A reviewer that silently fixes what it found destroys the evidence that the gate
  worked.
- **Split the findings, then route each half.** What will not be fixed here (an open question, a
  cross-module blocker, a missing input) goes **upstream** to the requirements document's open items
  (§2) and is referenced from the roadmap. What will be fixed here goes to the `modify-…` skill. Say
  which is which; do not leave the routing to whoever reads the log.
- **Review runs to a condition, not to a count**: until a pass returns nothing material. One round is
  the minimum and never the target. A fix is written on the assumption that the reviewer already
  found everything, which is exactly what lets a fix introduce a new defect. The re-review is scoped
  to the delta, so it is cheap; skipping it is what makes the first review's value decay.
- **The reviewer recommends; the owner rules.** A reviewer flags an artifact as *eligible* for
  `reviewed`. It never sets status.

### 7.5 Where the human sits

The owner is **not a sampling tier** in the checking stack. The owner is the direction-setting and
adjudicating function the stack reports into. A human ruling is required where no re-derivation can
supply the answer: a genuine conflict between sources, a scope decision, a value nobody holds. The
reason is that the decision is a judgement, not that a sample came due. Discretionary spot checks are
welcome, but they are not counted as a control.

## 8. Writing and running skills

A **skill** is one unit of the enabling system: a narrowly scoped procedure for one artifact class at
one phase, kept in `.claude/skills/<name>/SKILL.md`, versioned beside the work. Skills come in
create / modify / review sets per artifact class. A create skill is for the first draft only, and
stops if the artifact already exists.

- **Read a skill at the point of use, from the file.** Do not work from memory of what it says. This
  binds hardest on whoever wrote the skill, because authoring one produces a strong sense of knowing
  its contents, and that is exactly when the gap between text and recollection goes unnoticed.
- **State a property and its check, not an action.** "Never import the constant under test" depends on
  recognizing the moment. "The suite must fail when this constant changes; verify by changing it" can
  be checked afterwards, by anyone. Where a rule cannot be given a mechanical check, say so **inside
  the rule**, so that its reliance on judgement is visible.
- **Guidance is not control.** A rule written in prose is read, weighed, and can be skipped, most
  easily by its own author. **Promotion rule:** an instruction that has been skipped more than once,
  must always hold, and can be checked mechanically is promoted to enforcement (a hook, a
  permission, a test, a CI check). It is not re-worded more firmly.
- **Depth is a specification question.** Where one artifact class needs two depths (a shallow survey
  and a deep extraction), use two skills, each with a gate naming the depth it is for and its sibling
  for the other. Do not use one skill with a depth parameter: the parameter is set at dispatch, which
  is where the error happens.
- **Analyse three objects after a run, not one:** the artifact (is it correct), the process (what it
  cost: time, tokens, tool calls, errors) and the skill (how it performed, and why). A good artifact
  is not evidence of a good skill; some skill defects are visible only in cost.
- **Refine the skill after each run, from what that run actually got wrong**, and record the
  correction in the skill's own revision notes. When a skill produces something unusable, the finding
  is about the skill. Change it rather than working around it by hand.
- **Expect first skills to be rewritten.** Where a class of work has not been done before, executing
  it once by hand and then writing the skill tends to beat specifying it first.
- **The customization surface when reusing a skill is narrow:** the sources, the access path to
  context, and where outputs are written. Beyond that, how well it transfers depends on how similar
  the artifact class is. A skill built for an artifact with an oracle does not transfer to one
  without.

## 9. Diagramming

Diagrams are **authored as text and rendered**, never drawn in a tool and exported. A text diagram
diffs, reviews, merges and regenerates under the same change control as everything else; a binary
export does none of those.

- **Notation:** C4 for structure, UML for behaviour and data, written as **Mermaid**. Use fenced
  ```mermaid``` blocks inline by default, and a standalone `.mmd` file only when a diagram would
  clutter the prose around it. SysML is worth its cost when hardware, mechanical and software elements
  are co-modelled with parametrics. Where they are not, the traceability chain (§2) already does what
  SysML's requirements diagrams are for. Choose per project and record the choice in
  `DECISION_LOG.md`.
- **Choose views by system class.** The dominant view is whatever the requirements are organized
  around (see `create-architecture-doc`).
- **Split by audience, do not duplicate.** Context and container views are **stakeholder-facing**, with
  no implementation jargon. Component, state, sequence and class/ER views are **developer-facing**.
- **Name components to match `MODULE-x`.** A component in the architecture, a module in the eval set
  and a directory in `implementation/` are recognizably the same thing under the same name.
- **Render every diagram before considering it done.** Two known Mermaid defects are invisible in the
  source and appear only on rendering:
  - **No semicolons inside sequence-diagram message text.** `;` is a statement terminator in that
    grammar. Use commas.
  - **No nested or escaped double quotes inside C4 labels.** They can crash the C4 renderer. Rephrase
    instead.

  Render with `npx -y @mermaid-js/mermaid-cli -i diagram.mmd -o diagram.svg`. In a sandboxed
  container, pass a puppeteer config `-p pp.json` containing
  `{"args":["--no-sandbox","--disable-setuid-sandbox"]}` and point `PUPPETEER_EXECUTABLE_PATH` at an
  installed Chromium.

---

## Revision history

| Rev | Date | Author | Change | Why |
|---|---|---|---|---|
| 0.1 | `<date>` | `<owner>` | Adopted from the starter kit | Project start |

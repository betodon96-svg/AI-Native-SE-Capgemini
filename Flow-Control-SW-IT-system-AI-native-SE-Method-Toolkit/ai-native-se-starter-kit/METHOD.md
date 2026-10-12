# Orchestrated Agent Systems over Organizational Scale — the Method

*An AI-native approach to systems engineering.*

This document states the method this repository runs on, and why. It is the reasoning behind
[`CONVENTIONS.md`](./CONVENTIONS.md): that document says *what holds*, and this one says *why it
holds*. It is reference material, read when a convention's reason is in question and not at every
session start.

**On the strength of its claims.** This is a proposed methodology supported by early execution, not a
demonstrated result. Where a claim rests on observation it says so, and where it is a design position
it says that. "Over" in the title states a position: no comparison against an organizationally staffed
team has been run. The argument is that the functions organizational scale discharged can be arranged
architecturally, and that doing so is preferable where scale is unavailable. It is not that this has
been shown superior where scale exists.

---

## 1. Motivation

### 1.1 What classical systems engineering obtains from organizational scale

Classical practice does not merely *assume* an organization. It draws four distinct things from one:

- **Specialist knowledge, accessed by employing specialists.** A requirements engineer, a safety
  assessor, a verification lead and a regulatory specialist each hold knowledge the others do not.
  The organization is the mechanism by which a project gains access to knowledge no individual holds,
  and organizational complexity is substantially a consequence of that need.
- **Division of labour, which makes throughput a function of headcount.** Work is partitioned so that
  it can proceed in parallel. More output requires more people.
- **Separation of roles, which is where independence comes from.** The author of a requirement should
  not be its sole approver. Verification (does the system meet the requirement as specified) and
  validation (does the requirement capture the true need) are ideally performed by parties independent
  of the artifact's origin. Independence here is a *by-product of having enough people to keep roles
  apart*. It was never purchased directly.
- **Friction that makes process discipline self-enforcing.** Configuration management assumes that
  baselining a document is meaningful *because* producing a new version has real cost. A change is
  deliberate enough to warrant a change request, an impact analysis and a change control board.

All four are in scope. Treating the third as the whole problem is what makes a framework look like a
review technique.

### 1.2 Three disruptions

- **Agents execute engineering tasks, not just drafting.** An agent performs a task on the basis of
  intelligence, knowledge, context and judgement, the same basis on which a person would. Where an
  organization meets its need for specialist knowledge by employing specialists, an agentic system
  meets it by *specializing agents*, which is a matter of what context each one is given.
  **Productivity stops being a function of headcount.** Output scales with how well the work is
  decomposed, specified and orchestrated. The engineering problem does not disappear; it relocates.
  Access to knowledge and parallel execution now require *systems and orchestration* where they
  required hiring and management.
- **Generative AI collapses authorship cost.** An agent can draft, and just as easily re-draft, a
  requirements document, a test suite or a code module in seconds. That removes the friction that once
  made "don't silently overwrite prior versions" self-enforcing, unless the discipline is imposed
  deliberately.
- **Small teams and individuals take on obligation-heavy work.** Safety-adjacent and regulated
  development is no longer the exclusive domain of organizations with dedicated compliance, QA and
  independent verification functions. One practitioner may be the entire organization, so the change
  control board, the verifier and the validator are formally the same person. Taken with the first
  disruption, this is the method's situation: the practitioner has lost none of the obligations scale
  was supposed to discharge, and has an orchestration available instead of an organization.

### 1.3 The proposal

The systems engineering lifecycle is executed by an **orchestrated set of specialized AI agents
treated as an engineered system in its own right**. That system is modular, has specified interfaces,
is under configuration control, and composes each skill's context deliberately rather than letting it
be inherited. Three properties carry it:

- **Modularity** makes each agent separately specifiable, auditable, testable, modifiable and
  reusable, rather than leaving capability diffused through one general-purpose assistant.
- **Context and memory management** is how an agent is specialized at all, and how state survives the
  session.
- **Agentic specialization** lets a task be executed on knowledge and judgement rather than on a
  practitioner's available hours.

Five contributions follow:

1. **The orchestration architecture and its skills.** The enabling system is decomposed per artifact
   class and per phase (§2.2). Its skills are produced through a defined lifecycle rather than written
   once (§2.3), and the context they draw on is layered explicitly (§2.4).
2. **The eval as the primary engineering artifact.** When the producer is a generator, no inspection
   of a single output says anything about the next one. The eval suite therefore stops being derived
   from the requirement and becomes the artifact the requirement traces to (§2.5). This is the one
   lifecycle activity the method reshapes rather than re-staffs, and the *AI-native* claim rests on it
   (§4.3).
3. **Independence as an arranged property rather than a purchased one.** Because context is composed
   per agent, two agents can be given deliberately disjoint contexts. That recovers the *function* of
   organizational independence without a separate team (§2.7). The principle beneath it, that a check
   drawing on the same source as the thing it checks is a tautology, reaches well beyond review (§2.8).
4. **The enabling system is subject to the limitations it exists to govern.** Its agents have volatile
   memory, stochastic output and attention that drifts, which are the very properties that make
   AI-authored artifacts need governing. The tooling cannot be exempt from the discipline it applies
   (§3).
5. **Configuration management for near-free authorship**, built on a distinction classical CM never
   had to draw: **what must be preserved is the ability to discover that a question was settled, not
   the text that settled it** (§2.10).

---

## 2. Framework

### 2.1 Two systems, jointly engineered

The method engineers two systems, not one: the **target system** (what is being built) and the
**enabling system** (the orchestrated agents and tooling that produce the target system's engineering
artifacts). Both are subject to modularity, traceability and configuration discipline.

**The enabling system's architecture is the primary contribution.** Most AI-assisted development
treats its tooling as an unstructured productivity aid, not as a system with its own components,
interfaces, lifecycle and independence requirements. Treating it as the latter is what lets the
properties classical practice obtained from scale be arranged *by design*.

### 2.2 Decomposition of the enabling system

The enabling system is decomposed into **skills**: narrowly scoped procedures, typically a
create / modify / review set per artifact class, sharing a single-source-of-truth convention layer
that each skill references and none duplicates. The hypothesis is the one that motivates modularity in
any system: narrow, single-purpose skills produce more consistent and more reviewable artifacts than a
general-purpose agent asked to "do whatever the artifact needs".

**The decomposition is keyed on artifact class *and phase*, not class alone.** One artifact class can
need two skills, for example a shallow structural survey and a deep claim-level extraction over the
same sources, distinguished by the phase each serves. A wrong-depth invocation happens at
**dispatch**, not at authoring, so each skill carries a gate naming the depth it is for and its sibling
for the other. A depth *parameter* on a single skill was considered and rejected, because the
parameter is set at dispatch, which is exactly where the error occurs. The cost of getting this wrong
was observed: one misdispatch produced well over a thousand lines of claim-level notes to support
about thirty index entries. All of it was correct, and none of it was usable at that phase.

Two further sub-patterns recur alongside the create / modify / review set. Some artifacts are
**generation-only**: event records such as logs have no review counterpart by design. Some activities
are **review-only**: code is written by ordinary development, but reviewed by a dedicated skill.

### 2.3 The skill lifecycle

A skill does not come into existence in one authoring pass. Execution shows a **five-stage cycle with
a variable entry point.** The obvious reading, that you specify the skill and then run it, is the one
execution contradicted most consistently.

**1. Specification, by human and agent jointly.** It covers what must be accomplished; what good looks
like; what known practice applies; whether the deliverable is standardized and to which standard;
which documents are the sources; where outputs are written; the compute profile; how machine-readable
the output must be for the next step; and how the task breaks down.

- **Depth is a specification question, and the one most often skipped.** The discriminator is what
  the phase's claim actually is. An index asserts *this topic belongs here*, a claim about a source's
  scope settled by its structure. A developed artifact asserts *what the source says*, which needs the
  text.
- **"Good enough" is a real standard, not a concession.** In one execution, the two skills that needed
  no correction after running were the two specified *least* formally, written after the work had been
  done once by hand. The heavily specified skills written first were each wrong. **Specification effort
  did not predict correctness; prior execution did.**

**2. Execution, which may precede stage 1.**

- **Specify first** where the work is of a class already performed: the practitioner is writing down
  what they know how to do.
- **Execute first** where it is not: the practitioner is discovering what the work *is*, and a
  specification written before that encodes a guess carrying the authority of a rule.

What decides the order is **whether this class of work has been done before**, not preference. A
greenfield project's first skills should be expected to be *rewritten*, not merely refined.

**3. Analysis of three objects: the artifact, the process, and the skill.** The second and third are
routinely skipped.

- **The artifact**: is it correct, complete, sound? This is the conventional review.
- **The process**: elapsed time, token consumption, tool-call count, errors.
- **The skill**: how did it perform, and why?

Separating them matters because an artifact can be good while the skill is broken, visibly only in
process telemetry. A survey skill produced a correct, well-formed note while consuming roughly 110,000
tokens to extract about 91,000 characters of text, because its instruction had the agent read saved
web pages as markup rather than extracting text first. **No inspection of the output could have
revealed this.** A second class shows only in tool-call count. Every prior tool result is
re-transmitted on each turn, so an agent's cost grows with roughly the square of its tool calls. One
extractor made 35 calls and produced the shortest note of its cohort: it spent its budget carrying
context rather than acquiring it.

**4. Refinement**, correcting the skill from what stage 3 found.

- **Refine from effort, not only from output.** Checks written from what a review *happened to return*
  must also be judged on what they would *cost to run on purpose*.
- **§2.9 binds here.** Refining the text does not establish the property.

**5. Reuse, always with customization.** No skill transfers untouched. The recurring customization
surface is narrow and nameable: **the sources, the access path to context, and where outputs are
written.** Beyond that, how well a skill transfers depends on how similar the artifact is. A review
skill moved between two knowledge products with changes only to its lenses. A test-specification skill
built for executable code did not transfer to a knowledge product at all, because that class has no
oracle (§2.6).

**Why this is a cycle.** Stages 3–5 return to stage 1. A skill is a converging artifact, and its
revision history is evidence about the work rather than housekeeping. **Skills are revised after each
run, from what that run actually got wrong.** That makes the enabling system's skills **an output of a
project as much as an input to it.**

**Limits of this formulation.** The process tier is partly a black box: time, tokens, tool calls and
errors are observable, but *why* a skill performed as it did is inferred. No controlled comparison of
the two entry points exists. And **skill analysis has no independent form**: the artifact gets an
independent reviewer, but the skill is analysed by the party that wrote and ran it. Whether that
reproduces the self-reference failure of §2.8 at the level of the enabling system is unresolved, and
it is the most significant open question here.

### 2.4 The context architecture

State lives in files, not in an agent's context (§3.1). Those files form **layers**, and each layer
has a different failure mode:

| Layer | Holds | Governing rule |
|---|---|---|
| **Session-entry context** | Posture, constraints, pointers | **Thin by construction.** Never content, so an edit here cannot silently supersede a requirement |
| **Shared-access documents** | Conventions reference, decision log | The rules and the *why*. Append-only for rationale |
| **Knowledge artifacts** | Requirements, use cases, architecture, eval sets | Each with its own revision history and status field |
| **Intermediary working formats** | Surveys, extraction notes, reconciliations, coverage matrices, independent derivations | **Structured for the next machine step, not for a reader** |
| **Skills** | The procedures themselves | Versioned beside the work; revised after each run (§2.3) |

Two rules govern the set, and both were learned by violating them.

**The entry layer must be thin, and the reason is adversarial rather than aesthetic.** If
session-entry context can hold content, an edit to the most frequently touched file in the repository
can overwrite a requirement without appearing in that requirement's revision history.

**The intermediary layer is a first-class artifact, not scratch work.** It is where **cross-source
disagreement becomes visible**, for example in a vocabulary reconciliation that precedes merging.
Without it, two entries get created for one concept and never converge, each with a source behind it.
A pipeline lacking this layer **collapses reconciliation into assembly**, and the conflicts it would
have surfaced are silently resolved by whichever source was read first. It is also what makes modular
composition possible: a skill's output must be consumable by the next skill without a human in
between, which is a machine-readability requirement on the intermediary format.

### 2.5 Traceability, and the primacy of the eval

```
Source obligation → Requirement → Eval → Implementation module
       SRC-x.y    →   REQ-nnn   → EVAL-nnn →   MODULE-x
```

"Source obligation" is deliberately general: a regulation, a standard, a contract, a stakeholder
need. Classical practice would name the third link a *verification or validation case*. The method
names it the **eval**, and the change of name is a change of claim.

In classical practice the requirement is primary and the verification case is derived from it: written
once, run against a build, reported at a gate. That is correct when the thing under scrutiny is a
build, authored deliberately, existing in one state, and inspectable.

**The thing under scrutiny here is a generator, not a build.** An agent does not have a defect; it has
a distribution. It cannot be read, it produces a different artifact on each run, and its next output
has not been written yet. Nothing that inspects a single output tells you what the next one will be.
The only instrument that bears on a generator is a check that runs repeatedly and whose results
accumulate. That makes the eval suite the primary artifact, and the requirement what it traces back to:

- **Primary rather than derived.** The requirement states intent in prose. The eval states it in a
  form that can be executed against any number of generated outputs. Where the two disagree, the eval
  governs, because it is what actually runs. The disagreement is itself the most valuable signal
  either produces, because it means the prose admitted a reading its author did not intend.
- **Continuous rather than gated.** A generator's failure is distributional: it appears as a
  frequency, not an event, so the suite runs on every change or it measures nothing. Here near-free
  authorship works *for* the method: a check that costs nothing to re-run can be run always.
- **A specification of the producer, not of the output.** A passing eval suite states what the
  generator may be relied upon to produce. No classical verification case makes a claim of that shape,
  because no classical producer required one.

Three consequences follow:

1. **For artifacts with no oracle, the eval is a derivation protocol with a recorded expectation.**
   Derive independently from the source obligation, write the expectation down, then diff. That is
   §2.7 exactly. Independence and evaluation are one instrument seen from two sides.
2. **The enabling system's skills have no evals, and that, not a missing reviewer, is why their
   analysis has no independent form** (§2.3). The missing independence is a symptom; the missing
   artifact is the cause.
3. **An eval that has never failed is indistinguishable from a tautology without its record.** Only
   the accumulated history of runs, including the runs that found nothing, separates a good eval from
   a vacuous one. That is why eval logs are append-only (§2.10) and why the stopping rule in §3.6 is
   defined on them.

*What is claimed:* the primacy of the eval is a design position, supported by the chain and by
consequence 2's diagnostic value. It is not yet a measured result.

### 2.6 The oracle: what the checking regime depends on

The checking regime is set by one property of the artifact being checked. That property is not
whether the product ships AI, and not whether the artifact is code. It is whether the artifact has an
**oracle**: something that objects when it is wrong.

| Artifact class | Oracle | Applicable regime |
|---|---|---|
| **Executable**: code, scripts, diagram-as-code | Yes: the suite objects, or the renderer fails | Structural checks · deterministic verification · mutation · independent review |
| **Knowledge**: requirements, architecture, use cases, reviews, plans | **No** | Structural checks · independent review · provenance and honest marking · claim-level audit |

**Where an oracle exists**, two automated checks map onto the verification/validation distinction:

- **Verification**: deterministic tests derived from the traceability chain, checked against the
  *implementation*. "Was it built to meet the stated requirement?" Where the target system is
  deterministic, this is ordinary automated testing run continuously.
- **Validation**: an independent reviewer agent checks the requirement itself, the test or the code
  against the **source obligation directly**, not against the requirement document. "Does the
  requirement capture what the source actually demands?" This catches what verification structurally
  cannot: code that correctly implements a requirement that was itself a misreading of the source.

**Where no oracle exists, verification has no referent and must not be simulated.** A deterministic
product still produces knowledge artifacts, such as its own requirements and architecture, and those
are governed by independent review alone. Mutation testing imported from code review into a knowledge
product degenerated in practice: of four mutations, two survived as usable checks, and two had to be
relabelled as reflection prompts carrying no verdict. **Absent an oracle, a check that cannot fail is
not a weak check but a non-check.** What replaced mutation in practice was confronting the artifact's
claims with the opened source.

**The invariant core**, holding across both classes: **context independence · change control against
prior versions · logging that includes null results · closing the loop** (§3.6).

### 2.7 Independence by arranged context

Independence is a property the orchestration *arranges*, and context composition (§2.4) is where the
arranging happens. Two agents given deliberately disjoint context are independent in the sense that
matters. Review agents instantiate this as **regenerate-and-diff**.

**The failure it addresses.** A review agent asked to **critique a draft in place** ("does this look
correct?") shares the generating agent's interpretive framing and its blind spots, even in a
technically separate session. The correlation lives in the *task framing*, not merely in shared
conversation. That is why a fresh session is necessary but not sufficient.

**The mechanism.** The review agent is not shown the draft first. It is given only the source
obligation and derives independently what the artifact *should* be, and its derivation is **written
down before the draft is opened.** Only then is it diffed against the draft. This approximates what
organizational independence delivers (a second party, uninformed by the first party's work, tasked
with the same derivation) without requiring a second party to exist.

**Why the ordering is the whole mechanism.** Once the draft has been seen, it supplies the framing
within which the source is read. Writing the derivation first makes the diff *checkable rather than
trusted*: a reader can see what the reviewer expected independently of what it found. Where the
derivation is not recorded first, independence cannot be audited and should not be claimed.

**What it does not cover.** Regenerate-and-diff is one instantiation of the architecture, not its
definition. Whether it catches defects critique-in-place misses, and at what false-positive cost, has
not been tested in a controlled comparison.

### 2.8 The self-reference principle

Regenerate-and-diff is one instance of a more general rule:

> **Any check that draws on the same source as the thing it checks degenerates into a tautology, and
> the degeneration is not visible from inside the check.**

Execution exposed three further instances of the identical structure:

1. **A test deriving its expected value from the implementation it tests.** A test of a permission
   matrix computed its expectation *from that matrix*, producing an assertion of the form
   `X ∈ M == X ∈ M`. It could not fail for any `M`, and a privilege-escalation defect passed the suite
   green.
2. **An author certifying their own work.** A document revision listed what its author had "checked
   and found still valid". Independent review found that list wrong on two of five claims.
3. **An author choosing the mutation that tests their own test.** The author of the tautological test
   above ran a non-vacuity check, chose a mutation and saw a test fail, but the mutation happened to
   break a *different, hardcoded* case. Verifying through a hardcoded case and concluding that the
   parametrized case was sound is a reasoning error that feels earned from the inside, because a check
   did run and did pass.

**The design rule: for any verification, ask what source it draws on, and whether that is the source
of the thing verified.** Where it is, the verification's apparent success carries no information. It
follows, among other things, that a mutation set must be chosen by someone other than the author of
the tests, which places it in the reviewer's tooling.

### 2.9 Codified-but-not-loaded: a limit on written process

Codification is not the remedy for process failure. The rule violated in instance 1 above (*never
derive a test's expectation from the code under test*) had been written into the eval-set skill
**ninety minutes earlier, by the same agent that then violated it.** The rule was correct, current,
and located where it applied. It was simply not open at the moment it bound, and the agent worked from
its recollection of a document it had authored.

- **The failure was form without property, not non-compliance.** The instruction was followed in
  shape: the test *was* parametrized over the full matrix, as the rule directs. What was absent was the
  property parametrization exists to deliver. A rule describing an action can be satisfied without
  achieving the thing the action exists to achieve, and the actor cannot see this.
- **Authorship aggravates rather than mitigates.** Writing a rule produces a strong sense of knowing
  its contents, which suppresses re-reading exactly when the gap between text and recollection is
  widest. The rules at highest risk are the ones most recently written by the agent now executing
  them.

Two corrections follow. Rules are stated as **a property and its mechanical check** rather than as an
action to perform or avoid. And skills are **re-read from source at the point of use**, not recalled.
Whether the second is reliable under an agent's own discretion is itself unresolved, which is the
reason for the promotion rule in `CONVENTIONS.md` §8.

### 2.10 Configuration management when authorship is nearly free

Classical CM rests on the cost of producing a new version. AI authorship removes that friction, so the
discipline must be relocated into tooling and into a rule about what is actually being protected.

**What the discipline protects is not prior text.**

> **What must never be lost is the ability to discover that a question was already settled. The text
> that settled it is not the thing being protected.**

Append-only history is one implementation of that guarantee, and the right one when the artifact *is*
the record. Applied indiscriminately, it degrades artifacts whose purpose is to state a current
position. **The selection test:** is this artifact *the record of something*, or *the current statement
of something*? A record's history is its content. A statement's history is metadata, and belongs
beside it.

**Three patterns, selected per artifact:**

- **Pattern A: append-only record.** Content is never overwritten; a change appends a dated,
  rationale-carrying revision, and named baselines are frozen at defined milestones. It is *required*
  for **records of events** (review logs, eval runs, decision logs), where the clearest case is a
  **null result**, which only an append-only log can carry. It is also required for **artifacts
  baselined against an external obligation**, where a later reader must reconstruct what was claimed
  at a given date, and for **reversals**, where both decisions must survive or the question is
  silently re-litigated. That last case taught a necessary addendum: **a superseded revision is not
  inert.** A ruling was made, reversed on better evidence, and then *re-asserted from a superseded
  revision* still sitting unmarked in the working directory. Until it is marked *at the point of the
  claim*, a superseded revision remains a confident, searchable source of the withdrawn answer.
- **Pattern B: living status with immutable rationale.** Some artifacts describe *current* state. A
  roadmap is the clearest example, and a roadmap that only ever grows is not a roadmap. Current-state
  content is edited in place, while a separate append-only record says what changed and why.
- **Pattern C: superseding draft with retained intermediaries.** While an artifact is being drafted,
  superseding a passage with better text derived from it *is the work*, not a loss of history. The
  superseding chain is retained in the intermediary artifacts, and the artifact under construction is
  rewritten freely and stays readable. Applying Pattern A here instead produces **scar tissue**: a
  document that narrates its own editing history to a reader who wanted the current argument. **A
  draft that is still a proposal is a statement, not a record.**

**Generated artifacts** are none of these. Their history belongs to the generator, and hand-editing
one breaks regeneration.

Pattern A remains the default for anything evidentiary. Choosing wrongly in the permissive direction
destroys evidence; choosing wrongly in the restrictive direction destroys readability. Only the first
is unrecoverable, which is why the append-only reflex exists and why it still needs governing.

**Provenance of derived values: the backward half of configuration control.** The patterns govern an
artifact's *own* history. They do not govern what happens when an artifact carries a value **derived
from another** (a count, a table, a structural decision, a settled ruling) and the source moves. Four
defects in one execution had exactly this shape: a fact **copied rather than generated**, then left
behind when its source changed. None was a reasoning error, and none was caught by review. All four
were caught by the practitioner reading the artifact. Two rules follow:

1. **A value appearing in two artifacts is generated in at least one of them.** Copying is a
   commitment to manual synchronization that will not be honoured.
2. **Every derived artifact opens with a provenance block** naming what it is derived **from**, what
   **feeds on** it, and its **regeneration command**, or an explicit *not generated*.

### 2.11 The single accountable owner

Where classical practice assigns the change control board, verification and validation to separate
parties, a single-owner project collapses all three into one accountable person. The method does not
claim to remove that limitation. It makes it explicit and partly compensates for it: the owner's
judgement is applied *after* the automated checks, not instead of them, and **the owner's own drafts
are subject to the same independent review as an agent's**, not exempted by human authorship.

---

## 3. Engineering practices applied to the enabling system

The enabling system is engineered software, held to the same *do not trust an unstructured black box*
discipline the method applies to its outputs. Its practices address five agent limitations:

| Limitation | Practices |
|---|---|
| Memory does not survive a session | §3.1 externalized state |
| Attention degrades as context accumulates | §3.2 context isolation |
| Output is stochastic | §3.3 structural determinism · §3.4 bounding where it matters |
| Judgement is correlated across agents | §3.5 differentiated input · §3.6 review to a condition |
| Uncertainty is easy to represent dishonestly | §3.7 tri-state verification status |

### 3.1 Externalized state

**No persistent state may live only in an agent's context.** Traceability IDs, revision history,
status fields and baseline tags are externalized into version-controlled artifacts, and an agent
starting fresh reconstructs what it needs by reading the repository. This is violated easily and
invisibly: any instruction given in conversation rather than written to a file is state that will not
survive, and the loss is silent. Moving project state between environments by an explicit,
self-contained artifact rather than a conversation is the same rule at the largest scale.

### 3.2 Context isolation

Rather than one long-lived agent accumulating context across tasks, the enabling system is composed of
narrowly scoped skills, each invoked fresh, given only the inputs its task requires, and discarded on
completion. This serves two separate purposes. Keeping context small and on task mitigates attention
degradation. More importantly, isolation is **the mechanism that makes independence structural rather
than instructed**: a review agent with no access to the generating agent's context cannot be
influenced by it. Asking an agent to disregard what it has already seen is an instruction, and §2.8
explains why an instruction is not a control.

### 3.3 Structural determinism as a guardrail on stochastic generation

Everything that *can* be checked deterministically is pushed out of the model and into cheap
automatic validation, run before any content-level review: schema conformance, IDs present and well
formed, status fields populated, revision entries present where the artifact's pattern requires them.

This is a **Tier 0** beneath whatever regime the artifact's class permits, and it is the one tier that
applies to *both* classes: a knowledge artifact has no oracle for its content, but its structure is
still mechanically checkable.

**Tier 0 must include *does it run or render*, not merely *does it parse*.** Diagram-as-code was
generated twice with defects invisible on a plain read of the source. In one, a punctuation character
was an alternate statement terminator in the rendering grammar. In the other, nested quotation marks
crashed one diagram type's renderer. Both were detectable only by rendering. **Some generation defects
are invisible to any amount of careful reading, human or agent, and are caught only by execution
against the artifact's real consumer.** Ask of each artifact class what its real consumer is, and run
the check against that.

### 3.4 Bounding where non-determinism is allowed to matter

Stochastic behaviour is confined to development-time authorship and review. Every stochastic output is
a **proposal** subject to a downstream deterministic check, an independent re-derivation or a human
ruling, and is never a final answer in its own right.

The strongest available boundary is a delivered system with **no runtime model**, so that no
stochastic process can affect its outputs at all. That is a product decision and a luxury, not a
general rule. A system that ships model inference must draw the boundary somewhere narrower, which is a
harder governance problem this method does not address. The transferable principle is weaker and
wider: **identify where stochastic output becomes irreversible, and put a non-stochastic check
immediately before that point.**

### 3.5 Mitigating correlated judgement

Context isolation addresses shared-conversation bias. It does not address agents drawing on the same
model carrying blind spots in common. The intuitive mitigation is an ensemble of reviewers. **The
binding variable is not model identity.** A five-reviewer panel was designed and abandoned before
running, because the five were to receive **identical context and an identical prompt**. Shared
*input*, not shared weights, is what collapses independent checks into one.

This is §2.8 one level up. **Five agents sharing a context are not five checks; they are one check
reported five times, and the repetition presents as corroboration**, which manufactures confidence
rather than merely failing to add it.

**The lever is differentiation of input**, in ascending strength:

- **a differentiated stance** (an adversarial role, a named lens), which is weakest, because an agent
  told to disagree still reasons over the same evidence;
- **differentiated context**, giving each reviewer a *different subset of the sources*, so that it
  **reaches** a different conclusion rather than being told to voice one;
- **a different provider or model**, an escalation rather than the mitigation itself.

**Agent count is not a quality dial.**

### 3.6 Review to a condition; the human is not a tier

**The stopping rule.** Review runs **to a condition**: until a pass returns nothing material. One round
is the minimum and never the target. A fix is written on the assumption that the reviewer found
everything, which is exactly what lets a fix introduce a new defect. Many early cycles ran with no
re-review step at all.

This replaces a phase-based human-sampling policy (full review early, risk-based sampling later). That
policy was **specified and never operated**, while the convergence rule was operated everywhere without
being written down. This is §2.9's pattern: a codified rule nothing loaded, beside a practice that
worked while uncodified. The correction is to write down what is actually done, and to treat a
specified-but-unoperated policy as evidence about the specification.

**The reframing.** A sampling rate makes the human a quality-assurance backstop whose dial setting is
the policy question. That is not the role the human occupied. The practitioner set the question, the
audience and the boundaries; obtained and selected the sources; **ruled on each point where sources
genuinely conflicted**; and accepted or rejected each finding, including reversing earlier rulings when
evidence arrived. None of that is inspection, and none of it has a percentage. **The human is not a
tier in the verification stack. The human is the direction-setting and adjudicating function the stack
reports into.**

*Open:* a condition is harder to evaluate than a rate. What establishes that a pass returning nothing
reflects a clean artifact rather than a shallow review is unresolved.

### 3.7 Tri-state verification status

Status keeps `draft`, `reviewed` and `approved` distinct rather than collapsing them into a pass/fail
bit. The same shape recurred at the implementation level: a component recorded a verification outcome
as a boolean, defaulting to a passing value in the common case where the comparison **had not yet been
possible to run.** That is the method's own failure mode, collapsing *not yet checked* into *checked
and fine*, appearing in a data model, where it is easy to introduce and easy to miss.

**The principle:** any field recording a verification outcome is tri-state (verified-pass,
verified-fail, not-yet-verified) wherever not-yet-verified is reachable. It is load-bearing for §2.5:
an eval that has never failed and one that cannot fail report the same thing, and only a record that
can represent *not yet established*, and that accumulates runs including nulls, separates them.

### 3.8 What these practices do not yet cover

The practices are applied by the practitioner *to* the enabling system. None is applied *by* the
enabling system to itself. Every artifact class the method governs has evals and an independent
reviewer, but the skills themselves have neither. Each practice above is therefore currently verified
by inspection and by whether the work came out right, which is the standard §2.8 says cannot be
relied on. This is the method's largest internal inconsistency. It is named rather than resolved.

---

## 4. Relation to INCOSE practice

### 4.1 Systems engineering as a found tool

The method was not derived from INCOSE practice. Its goal was a product that could be **trusted and
audited**, with agents doing the work. Two constraints followed, and neither was a choice:

1. **An agent cannot hold the whole problem**, so the work must be decomposed into units small enough
   for one agent to execute.
2. **Context does not survive a handoff**, so each boundary between units must be crossed by a written
   artifact rather than by shared understanding.

Pursued on their own terms, these produce named units of work with defined scope, an artifact at every
boundary, a record of what each unit was supposed to accomplish, and a check that it did. Those are
requirements, interfaces, baselines and verification cases. **Systems engineering already had rigorous
names, structures and failure literature for all four, so it was adopted as a found tool.**

This changes what the correspondences are evidence of. A method arriving at the same structures from
different pressures (context limits and handoff loss, rather than headcount and coordination cost)
suggests that **the constraints on multi-agent work are substantially the constraints on multi-person
work**, and that the discipline developed for the latter is load-bearing for the former. The claim is
bounded: the practitioner knew systems engineering existed, so this is not evidence of independent
reinvention. What is supported is narrower: each structure was adopted at the point where a concrete
failure demanded it.

### 4.2 Direct correspondences

| Method element | INCOSE equivalent | Fit |
|---|---|---|
| Source → requirement → eval → module chain (§2.5) | Requirements traceability matrix | Direct, provided the chain supports backward traversal as well as forward |
| Decomposition of the enabling system per artifact class and phase (§2.2) | Architectural decomposition; modularity and interface definition | Direct in structure, with no classical counterpart in *subject*: INCOSE decomposes the product, not the tooling that produces it |
| Named baselines, immutable where the artifact is evidentiary (§2.10) | Configuration management: functional, allocated and product baselines | Direct for evidentiary artifacts. The method adds two patterns classical CM did not need |
| Eval suites traced to requirements (§2.5) | Verification and validation cases | Same in structure, **different in status and cadence** (§4.3) |
| Deterministic test tier (§2.6) | **Verification**: built to the stated requirement? | Direct |
| Independent re-derivation and diff (§2.7) | **Validation**: was the right thing specified, checked against the external source? | Direct, and the strongest alignment with classical theory |
| Owner approving changes (§2.11) | Change control board | Corresponding, with a compression: a board of one removes the check a multi-member board provides over any single member's judgement |

### 4.3 Where the eval diverges from the verification case

| | Classical verification case | Eval |
|---|---|---|
| **Status** | Derived from the requirement | Primary; the requirement is what it traces back to |
| **Subject** | A build: one artifact, authored deliberately, inspectable | A generator: a distribution, not inspectable, whose next output does not yet exist |
| **Cadence** | Run per baseline, reported at a gate | Run on every change; a distributional failure appears as a frequency, not an event |
| **What passing means** | This build meets this requirement | This producer may be relied upon to produce artifacts of this kind |
| **Failure of the instrument** | A case that does not exercise the requirement | A check that cannot fail at all, indistinguishable from a passing one without its run history |

Classical practice had no reason for this distinction: its producers were people, and a person's
output is checked by inspecting it. Once the producer is stochastic, inspecting any single output
stops being informative about the next, and the instrument must change in status, cadence and meaning
at once. **That is the substance of the AI-native claim: one lifecycle activity changes shape rather
than changing staffing.** The rest of the method is, honestly, re-staffed rather than reshaped, which
bounds the claim to this one activity.

### 4.4 Deliberate compressions relative to full INCOSE practice

- **No separate stakeholder-requirements layer.** The method goes directly from source obligation to
  requirement. That is defensible only while the source *is* the sole stakeholder input. Once
  requirements enter that are not derived from it, a second, parallel lineage
  (stakeholder need → requirement) is needed alongside the source-derived one.
- **No hazard analysis or safety case.** A system can conform fully on paper while failing in a way
  that matters physically, for example an output correctly generated from wrong underlying state.
  Hazard analysis is not yet a first-class artifact type in the method.
- **No organizational IV&V.** Even with arranged independence, final sign-off is by the person
  accountable for the requirements.
- **No operational validation.** Every eval checks an artifact against a source obligation. None
  checks the delivered system in its operational environment, which is what validation ultimately
  means, and which no architectural independence can substitute for.

### 4.5 Where the method extends beyond either tradition

These elements have no direct precedent in classical systems engineering, which predates AI-authored
artifacts, or in current AI-assisted development, which has not adopted SE-grade traceability,
baselining or the verification/validation distinction:

- **The enabling system as an engineered system under configuration control**, decomposed per artifact
  class and phase, its skills produced through a defined lifecycle.
- **The eval as a specification of a producer rather than a check on a build.**
- **Context as the designed interface between skills**, including deliberate exclusion as a design
  act.
- **Independence obtained by arranging context rather than by separating people**, and the
  self-reference principle that generalizes it.
- **CM for artifacts whose authorship cost is near zero**, preserving the discoverability of a settled
  question rather than the text that settled it.

The question these answer is how the functions an organization discharged (knowledge access, division
of labour, separation of roles, process friction) are reconstructed when the "organization" is one
practitioner and the co-author is a stochastic process. Neither tradition has had reason to ask it.

---

## 5. Refinements adopted in practice since the method was stated

These were learned by running the skills after the method above was written. They are carried in
`CONVENTIONS.md` and the skills, and noted here so that this document and the conventions do not
disagree silently.

- **Regenerate-and-diff applies where a separable obligation is held** (§2.5, consequence 1). It works
  when the reviewer holds a source written separately and rich enough to re-derive the artifact from.
  Where the source is descriptive prose by the same authors, or too thin, re-derivation produces thin
  output whose "absences" are artifacts of the derivation. There, the eval is a **conformance audit
  against an external rubric** (for requirements, the INCOSE requirement-quality characteristics),
  whose independence comes from the rubric being external. → `CONVENTIONS.md` §7.2.
- **Lossy intermediates are fixed upstream, and consumers never re-read the original** (§2.4). A
  skill that works around a gap in its input by opening the original source creates a divergent second
  reading and leaves the defect in place for every other consumer. → `CONVENTIONS.md` §3.
- **When an intermediate is corrected, every conclusion drawn from it is re-derived.** A conclusion
  reached on the uncorrected input, and later "confirmed" without re-derivation, was observed to
  survive into two downstream artifacts until an independent reviewer caught it. →
  `CONVENTIONS.md` §3.
- **Guidance is not control; the promotion rule** (§2.9). An instruction skipped more than once that
  must always hold and can be checked mechanically is promoted to enforcement (a hook, a permission, a
  test), not re-worded. → `CONVENTIONS.md` §8.

---

## Where each part of the method lives operationally

| Method | Conventions | Skills |
|---|---|---|
| §2.2–2.3 skills and their lifecycle | §8 | all |
| §2.4 context architecture | §3, and the index in `CLAUDE.md` | — |
| §2.5–2.6 evals and the oracle | §7.1 | `create-eval-set`, `review-*`, `create-eval-log-entry` |
| §2.7–2.8 independence and self-reference | §7.2–7.3 | every `review-*` skill |
| §2.9 codified-but-not-loaded | §8 | read-at-point-of-use in `CLAUDE.md` |
| §2.10 configuration management | §2, §5 | every `modify-*` skill, `update-roadmap` |
| §3.6 review to a condition | §7.4–7.5 | the module sequence in `CLAUDE.md` |
| §3.7 tri-state status | §4 | `review-code-change`, `create-eval-set` |

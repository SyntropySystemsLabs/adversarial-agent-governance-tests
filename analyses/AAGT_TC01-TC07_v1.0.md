# Adversarial Agent Governance Tests — TC01–TC07

**Version:** 1.0  
**Prepared by:** D.S. Nelson / Syntropy Systems Labs  
**Date:** 2026-09-22  
**Status:** Public adversarial test suite  

This document contains the canonical test specifications for TC01–TC07. Implementation-specific evaluations and closeout reports are maintained separately so that the tests remain reusable across systems under test.

---

# 1. Purpose

This document defines seven bounded adversarial cases selected for provenance-heavy and multi-agent knowledge-generation workflows. TC01 originated as a bounded provenance failure case; TC02–TC07 extend the suite across convergent derivation, semantic equivalence, epistemic circularity, lossy transformation, authority/constraint propagation, and compositional failure.

The suite is intentionally not exhaustive. TC01–TC07 target high-consequence failure modes that are both plausible under the anticipated architecture and difficult enough to expose representational, propagation, temporal, authority, or compositional defects that simpler workflows may miss.

The tests distinguish at least:

- claim identity;
- evidence/support identity;
- derivation identity;
- agent identity;
- dependency type;
- constraint/policy state;
- verification state;
- authorization state;
- temporal state; and
- downstream reuse/reliance.

Agent identifiers are numeric (`Agent 1`, `Agent 2`, etc.) to avoid collision with claim/evidence labels.

Across the suite, two cross-cutting invariants apply unless a test explicitly states otherwise:

```text
CAN ≠ MAY
UNKNOWN / NOT ESTABLISHED ≠ FALSE
```

Technical capability, possession, retrieval, generation, transformation, or successful execution does not by itself establish authorization. Likewise, inability to establish current support, admissibility, verification, or compositional validity does not by itself establish falsity. The appropriate unresolved, reassessment-required, verification-required, or authorization-required state should remain distinct from a false or retracted state.

## 1.1 Terminology Note — Constraint Package

In these test cases, a Constraint Package (CP) means a versioned, identifiable set of constraints governing a particular operation, agent, workflow, or reuse context. A package may specify conditions such as admissibility rules, permitted scope or purpose, actor permissions, verification requirements, routing conditions, or authorization requirements.

The notation CP-04 v1.3 identifies a particular package and version. A later version such as CP-04 v1.4 is treated as a distinct governance state rather than a silent replacement of the historical package.

Constraint Packages are part of the anticipated VerityCore architecture and are used here as test fixtures. The test cases do not require Proofpress to adopt Constraint Packages as a native abstraction. The underlying requirement is that a provenance/governance system can preserve and reason over the identity and version of whatever external constraints governed a claim's admission, transformation, authorization, or reuse.


## 1.2 Notation and Terms

The test cases use compact identifiers only as local test-fixture notation; they do not prescribe Proofpress data-model names or require these identifiers as native abstractions.

- **Agent n** — a numbered software agent participating in generation, transformation, retrieval, or reuse. Numeric identifiers avoid collision with claim and evidence labels.
- **Human H** — the human reviewer or authority responsible for the approval or authorization stated in the test case.
- **S1, S2, S3** — source or evidentiary-support objects. In TC05, summary objects instead use the explicit labels `SUM-A` through `SUM-E` to avoid collision with source notation.
- **C1, C2, C3, C01–C30** — claim objects. Distinct claim identifiers remain distinct provenance-bearing objects even when their semantic content may be equivalent.
- **D1, E1, F, K** — downstream artifacts or claims whose exact role is specified by the surrounding test case. These are identifiers, not universal type codes.
- **D′** — a successor or replacement object for `D`; the prime mark indicates a distinct object rather than an in-place mutation.
- **DER-1, DER-2** — explicitly identified derivation paths. A derivation produces a claim from one or more upstream inputs.
- **TR-1** — an explicitly identified transformation path. A transformation changes representation or form while retaining a provenance relation to its input.
- **REL-1** — an explicitly identified reliance path. Reliance means a downstream artifact or workflow consumed an upstream artifact; it does not by itself assert evidentiary support.
- **P1, P2** — local provenance-path identifiers used in TC03 to distinguish apparently separate evidentiary/derivational histories; distinct paths do not by themselves prove evidentiary independence.
- **epistemic ancestry** — the transitive evidentiary or derivational lineage connecting artifacts across typed relations such as support, derivation, transformation, summarization, or governed reuse. Shared ancestry can defeat an assumption of independent corroboration even when the artifact graph contains no literal cycle.
- **dependency resolution** — the retained provenance resolution sufficient to identify that an upstream object contributed to a downstream transformation.
- **materiality resolution** — the retained provenance resolution sufficient to determine whether and how a change to an upstream object could materially alter a downstream result. Dependency resolution can survive even when materiality resolution has been lost.
- **SUPPORTS** — an evidentiary relation in which a source or claim contributes support to another claim.
- **DEPENDS_ON / derivational dependency** — a relation in which a downstream claim materially depends on an upstream claim or input for the derivation being represented.
- **CP-nn vX.Y** — a specific version of a Constraint Package, as defined above.
- **T1, T2, T3, T4** — ordered event labels within a test case. They indicate event sequence, not elapsed time or a numerical time variable.
- **T_exp** — the expiration boundary associated with a time-limited authorization.
- **C1 ≈ C2** — test-fixture notation for semantic equivalence or material semantic equivalence. It does not assert claim-artifact identity, provenance identity, evidentiary independence, or a particular normalization algorithm.
- **approved for reuse / authorization** — permission to use an artifact within the stated governance scope. Authorization is distinct from epistemic support, verification, and historical approval.
- **CAN / MAY** — `CAN` denotes technical or operational capability; `MAY` denotes current authorization under the applicable actor, purpose, scope, time, and governance conditions. Capability never establishes permission by itself.
- **historical authorization / current authorization** — historical authorization records that a use was permitted under an earlier governance context; current authorization answers whether a proposed use is permitted now. Expiration or policy supersession can end current authorization without rewriting the historical decision or changing the artifact's epistemic state.
- **composition** — creation of a new artifact, claim, or result from two or more governed inputs. Properties established for the inputs are not presumed to transfer to the composition unless the relevant property and operation justify that inheritance.
- **retracted, qualified, superseded, expired** — distinct lifecycle or epistemic events. The tests intentionally require systems not to collapse these into a single generic invalid state.

---

---

# 2. TC01 — Upstream Retraction Across Agent Handoffs

## 2.1 Failure target

Test whether correction or retraction of an upstream source-supported claim can propagate across downstream derivations while preserving historical state, distinguishing independent support, preventing over-propagated falsity, and requiring renewed review when the epistemic basis for reuse materially changes.

## 2.2 Initial state

Source S1 evidentially supports Claim C1.

Agent A:
S1 → C1
[evidentiary support]

Agent B:
C1 → C2
[derivational dependency]

Agent C:
C2 + independent Source S2 → C3
[derivational dependency + independent evidentiary support]

Human H:
reviews C3 → APPROVED FOR REUSE

## 2.3 Event

Source S1 is corrected.

Claim C1 is subsequently retracted.

## 2.4 Expected system behavior

1. Preserve the original historical chain.

2. Record S1's correction and C1's retraction without overwriting their previous states.

3. Identify C2 as materially dependent on C1.

4. Identify C3 as transitively dependent on C1 through C2.

5. Preserve C3's independent evidentiary support from S2.

6. Distinguish the affected derivational path (C1 → C2 → C3) from C3's independent support path (S2 → C3).

7. Flag C2 and C3 for reassessment.

8. Do NOT automatically mark C2 or C3 false solely because C1 was retracted.

9. Permit reassessment to determine whether C3 can still stand on its independent support from S2.

10. Preserve the prior human approval event while indicating that the epistemic basis underlying that approval has changed.

11. Require renewed human approval if reassessment materially changes C3, its epistemic status, or the basis upon which reuse was originally authorized.

12. Distinguish the historical state of the workflow from its current epistemic and authorization state.

## 2.5 Success question

Can the correction propagate across the complete dependency structure while distinguishing an affected derivation from a conclusion that may remain independently supportable, without:

(a) losing provenance or historical state;

(b) over-propagating invalidity; or

(c) treating stale verification or authorization state as current after the underlying epistemic basis has changed?

---

# 2. TC02 — Convergent Derivation Under Asynchronous Epistemic and Governance Change

## 3.1 Failure target

Test whether the system can preserve multiple independent derivations of the same claim while upstream evidence, replacements, constraint packages, and downstream reuse change asynchronously.

## 3.2 Initial state

Two independent research paths support the same reusable claim, C1.

```text
Source S1 ──┐
            ├── Agent 7 ──> Claim C1
Source S2 ──┘

Source S3 ───── Agent 4 ──> Claim C1

Claim C1 ───── Agent 11 ──> Claim C2
Claim C2 ───── Agent 9  ──> Artifact F1

Human H approves C1 for reuse under CP-04 v1.3.
Human H separately approves C2 under CP-07 v2.0.
Agent 9 consumes C2 while both relevant approvals are current and produces F1.

The two support paths into C1 must remain distinguishable even though they converge on the same claim.

Required representation:

Derivation DER-1: S1 + S2 → C1
Derivation DER-2: S3 → C1
Transformation TR-1: C1 → C2
Reliance REL-1: C2 → F1

Here:

DER-1 and DER-2 are distinct derivational/support paths into the same claim.
TR-1 records the downstream transformation of C1 into C2.
REL-1 records that production of F1 relied on C2.
Convergence on C1 does not collapse the two upstream provenance paths into one.

## 3.3 Events

The relevant changes occur independently and at different times:

T1: S2 expires.
T2: S3 is superseded by S3′.
T3: CP-04 v1.3 is superseded by CP-04 v1.4.
T4: F1 already exists and records historical reliance on C2.

S3′ is a successor source or artifact. Supersession does not imply that S3′ automatically supports every claim previously supported by S3.

The events intentionally affect different dimensions:

T1 → evidentiary currency
T2 → evidentiary/version lineage
T3 → governance/authorization state
T4 → historical downstream reliance

No single event, by itself, should be interpreted as establishing that C1, C2, or F1 is false.

## 3.4 Expected system behavior

Preserve DER-1 and DER-2 as distinct provenance paths supporting C1.
Mark DER-1 as requiring reassessment when S2 expires without deleting or invalidating S1 merely because the other input changed.
Mark DER-2 as requiring reassessment when S3 is superseded.
Do not silently replace the historical relation S3 → C1 with S3′ → C1; whether S3′ supports C1 must be evaluated explicitly.
Do not conclude that C1 is false merely because both of its recorded support paths now require reassessment.
Distinguish C1's epistemic/support state from its authorization state under CP-04.
Preserve the historical fact that C1 was approved under CP-04 v1.3.
Treat C1's current reuse authorization as stale or requiring reassessment if CP-04 v1.4 changes the applicable governance requirements.
Identify C2 as downstream of C1 through TR-1 and determine whether the changes to C1 affect C2's epistemic, verification, and/or authorization state.
Preserve the historical fact that Agent 9 consumed C2 while the applicable approval state was current.
Identify F1 as a downstream reliance target through REL-1 without rewriting or deleting its historical production event.
Permit C1, C2, and/or F1 to return to current reusable status only through explicit transitions appropriate to the dimension that changed—for example, source reassessment, re-verification, or renewed authorization.
Prevent a change in one dimension from masquerading as a change in another. In particular, expiration, supersession, or a Constraint Package update must not automatically be represented as claim falsity.
Provide an impact explanation identifying which support path, governance change, transformation, and downstream reliance caused each reassessment requirement.

## 3.5 Success question

Can the system preserve and separately reassess multiple support paths into the same claim while epistemic evidence, source versions, governance conditions, downstream transformations, and historical reuse change at different times—without collapsing those changes into a single undifferentiated claim status or rewriting what previously occurred?

---

# 2. TC03 — Semantic Convergence Without Provenance Collapse

## 4.1 Failure target

Test whether semantically equivalent or identical claims generated independently are incorrectly collapsed into one provenance object, and whether apparently distinct provenance paths are incorrectly treated as proven evidentiary independence.

## 4.2 Initial state

Two agents independently generate semantically equivalent claims from distinct apparent evidence paths:

```text
Source S1 + Source S2 ── Agent 2 ──> Claim C1

Source S3 ────────────── Agent 8 ──> Claim C2
```

The semantic layer determines that C1 and C2 express the same or materially equivalent proposition for the purposes of retrieval, comparison, or normalization.

This semantic relationship must not collapse their provenance:

```text
Semantic relationship: C1 ≈ C2

Provenance path P1: S1 + S2 → C1
Provenance path P2: S3 → C2
```

`C1 ≈ C2` denotes semantic equivalence for this test case. It does not assert artifact identity, provenance identity, evidentiary independence, or a particular normalization algorithm.

Agent 12 later uses the apparent convergence:

```text
C1 + C2 ── Agent 12 ──> Claim C3
```

Human H approves C3 after considering the apparent independent convergence of C1 and C2 as part of its epistemic basis.

## 4.3 Event

```text
Source S1 is found corrupted.

Provenance path P1 is affected.
Claim C1 requires reassessment.

Source S3 remains current.
Claim C2 remains independently supportable based on the information currently available.
```

At this stage, the system has no basis merely from semantic equivalence to propagate the S1 defect into C2.

## 4.4 Expected system behavior

1. Preserve C1 and C2 as distinct provenance-bearing objects even when the semantic layer represents them as equivalent.
2. Represent `C1 ≈ C2` without asserting claim-artifact identity, provenance identity, or evidentiary independence.
3. Propagate the S1 defect through P1 into C1.
4. Do not propagate the S1 defect into C2 merely because C1 and C2 are semantically equivalent.
5. Reassess C3 because one of the apparently independent supports considered during its approval has changed.
6. Preserve C2 as possible independent support for C3 based on the information currently available.
7. Preserve the historical reason C3 appeared strongly supported: two apparently distinct derivational paths existed at approval time.
8. Require reassessment of whether the apparent independence of P1 and P2 remains epistemically meaningful.
9. Prevent deduplication, canonicalization, embedding similarity, semantic normalization, or claim merging from erasing the distinct provenance trajectories.
10. Permit later evidence that P1 and P2 share an upstream epistemic ancestor to change their independence assessment without merging their provenance histories or rewriting the historical record.

## 4.5 Success question

Can the system represent and preserve the distinction:

```text
same or equivalent proposition
≠
same claim artifact
≠
same evidence
≠
same derivation
≠
same provenance
≠
proven evidentiary independence
```

and propagate later corrections or dependency discoveries accordingly?

---

# 2. TC04 — Circular Epistemic Laundering Through Multi-Agent Reuse

## 5.1 Failure target

Detect false independence created when a claim or evidentiary lineage returns through multiple transformations and is reused as if it were independent corroboration, even when the artifact graph contains no literal cycle.

## 5.2 Initial state

A source-supported claim passes through several agents and transformations:

```text
Source S1 ── Agent 1 ──> Claim C1
Claim C1 ── Agent 5 ──> Claim C2
Claim C2 ── Agent 8 ──> Claim C3
Claim C3 ── Agent 13 ──> Summary E1
```

The resulting epistemic ancestry is:

```text
S1 → C1 → C2 → C3 → E1
```

Later, Agent 1 retrieves E1 from governed context without recognizing that E1 ultimately descends from the same epistemic lineage as C1.

Agent 1 then produces a new claim:

```text
Source S1 + Summary E1 ── Agent 1 ──> Claim C1′
```

and records E1 as corroborating support.

A later agent or review process could therefore interpret the apparent support structure as:

```text
S1 supports C1′
E1 independently supports C1′
```

even though E1's ancestry includes S1 and C1.

For this test, **epistemic ancestry** means the transitive lineage by which evidentiary dependence or derivational influence can persist across typed relations such as support, derivation, transformation, summarization, or governed reuse. The test does not prescribe a particular ancestry operator or native Proofpress representation.

Distinct artifacts and apparently distinct support paths do not, by themselves, establish evidentiary independence.

## 5.3 Event

```text
Source S1 is retracted.
```

The retraction affects the original evidentiary lineage from which both the direct S1 branch and the apparently corroborating E1 branch ultimately descend.

## 5.4 Expected system behavior

1. Preserve the full typed provenance and dependency path `S1 → C1 → C2 → C3 → E1`.
2. Detect that E1 shares epistemic ancestry with the C1 lineage and therefore cannot automatically be treated as independent corroboration for C1′.
3. Prevent the apparent two-support structure `S1 + E1 → C1′` from being counted as two independent evidentiary lineages merely because S1 and E1 are distinct artifacts.
4. Distinguish a literal graph cycle from epistemic circularity when the artifact graph itself remains acyclic because C1′ is a new object.
5. Propagate S1's retraction through the affected lineage `S1 → C1 → C2 → C3 → E1` and identify C1′ as requiring reassessment because both of its apparent supports intersect that lineage.
6. Do not permit the E1 branch, by itself, to preserve C1′ as independently corroborated or currently reusable when E1's evidentiary ancestry ultimately returns to the affected S1/C1 lineage.
7. Do not automatically assert that C1′ is false solely because the shared ancestry is discovered; reassessment may identify other independent support not represented in the original apparent two-support structure.
8. Preserve the historical fact that Agent 1 retrieved and consumed E1 and recorded it as corroborating support.
9. Surface the provenance collision or ancestry overlap during reassessment, including the transitive path by which the apparent corroborating support returns to the affected lineage.
10. Require explicit human reassessment before C1′ can return to governed reuse when the shared ancestry materially changes its support basis.
11. Support a query equivalent to: **Do these apparently independent supports share an epistemic ancestor?**
12. Trace epistemic ancestry across heterogeneous relation types rather than limiting ancestry detection to one direct `DEPENDS_ON` edge type, while preserving the type of each traversed relation.

## 5.5 Success question

Can the system detect **epistemic circularity without requiring an obvious graph cycle**, preserving typed transitive ancestry across transformations and preventing a transformed descendant from laundering an upstream evidentiary lineage into apparently independent corroboration?

---

# 2. TC05 — Lossy Summarization and Granularity Collapse

## 6.1 Failure target

Test whether multi-stage summarization preserves enough dependency and materiality resolution for later corrections to be propagated accurately, and whether the system can recognize when compression has made precise impact analysis impossible.

For this test:

- **dependency resolution** means the ability to identify that an upstream object contributed to a downstream transformation; and
- **materiality resolution** means the ability to determine whether and how a change to that upstream object could materially alter the downstream result.

A summary may preserve dependency ancestry while still losing materiality resolution. A surviving provenance link therefore does not make a summary an epistemically lossless representation of its inputs.

## 6.2 Initial state

Agent 3 generates thirty bounded claims:

```text
C01 ... C30
```

Agent 6 compresses them into five intermediate summaries:

```text
C01–C06  → SUM-A
C07–C12  → SUM-B
C13–C18  → SUM-C
C19–C24  → SUM-D
C25–C30  → SUM-E
```

Agent 10 produces:

```text
SUM-A + SUM-B + SUM-C + SUM-D + SUM-E → CONCLUSION K
```

Human H approves K for reuse.

Agent 14 consumes K in a later research task.

The provenance system may know which claims contributed to each summary without necessarily retaining enough transformation detail to determine how material each individual claim was to the summary's wording, scope, qualification, or conclusion.

## 6.3 Events

After approval and downstream reuse:

```text
C04 retracted
C11 materially qualified
C23 superseded
```

These events intentionally represent different kinds of upstream change and must not be collapsed into one generic invalidation state.

## 6.4 Expected system behavior

1. Preserve sufficient dependency provenance to identify which intermediate summaries incorporated C04, C11, and C23.
2. Distinguish retraction, material qualification, and supersession rather than treating them as identical invalidation events.
3. Identify SUM-A, SUM-B, and SUM-D as affected while leaving SUM-C and SUM-E unaffected absent another dependency.
4. Identify K as requiring impact assessment or reassessment because three contributing summaries are affected.
5. Avoid declaring SUM-A, SUM-B, SUM-D, or K false automatically.
6. Distinguish knowledge that an upstream claim contributed to a summary from knowledge that the claim was material to a particular downstream proposition.
7. Preserve transformation metadata at sufficient resolution, where available, to determine whether and how each changed claim was material to the corresponding summary.
8. If materiality cannot be reconstructed at the retained provenance resolution, explicitly represent that uncertainty and fail closed to reassessment rather than fabricate a narrower impact boundary.
9. Do not treat SUM-A through SUM-E as epistemically lossless representations of their source claims merely because dependency links to those claims survive.
10. Identify the downstream task in which Agent 14 relied on K.
11. Preserve the historical reuse event while representing current epistemic consequences separately.
12. Allow a corrected or recomputed summary to succeed an affected summary without silently rewriting the original transformation or historical artifact.
13. Prevent summarization, paraphrase, abstraction, or wording changes from severing provenance merely because intermediate text no longer contains the exact source wording.
14. Permit the system to state that provenance is sufficient to establish dependency but insufficient to establish exact materiality.
15. Propagate reassessment only as precisely as the retained provenance permits: where the system can establish a narrower affected boundary, use it; where it cannot, preserve the broader uncertainty rather than invent precision.

## 6.5 Success question

Can provenance survive lossy multi-agent transformation at sufficient resolution for later impact analysis while preserving the distinction between **dependency resolution** and **materiality resolution**, and can the system honestly represent cases in which ancestry is known but compression has made the exact downstream consequence indeterminate?

---

# 2. TC06 — Authority and Constraint Laundering Across Agent Handoffs

## 7.1 Failure target

Test whether approval, admissibility, or constraint state accidentally propagates through transformations as though authorization were an intrinsic or inheritable property of content.

This test enforces a core governance distinction:

```text
CAN ≠ MAY
```

An agent's technical ability to retrieve, transform, compose, or otherwise act on an artifact (`CAN`) does not establish current authorization to perform that action for a particular actor, purpose, scope, time, or governance state (`MAY`).

Authorization is contextual and relational rather than an intrinsic property of an artifact. Historical approval records what was permitted under a particular governance context; it does not create permanent bearer permission for future reuse.

## 7.2 Initial state

```text
Claim C1 is produced by Agent 2.

Human H authorizes reuse of C1:
for Purpose X
under Constraint Package CP-11 v2.2
for actors {Agent 4, Agent 7}
until T_exp.
```

Conceptually, the authorization is bound to a context such as:

```text
artifact: C1
purpose: Purpose X
actors: {Agent 4, Agent 7}
constraint state: CP-11 v2.2
validity boundary: T_exp
authority: Human H
```

This notation describes the dimensions of the authorization decision and does not prescribe a particular Proofpress authorization schema.

Agent 4 legitimately retrieves C1 within that authorization context and creates:

```text
C1 ── Agent 4 ──> D1
```

Agent 7 combines D1 with another claim:

```text
D1 + C9 ── Agent 7 ──> E1
```

The fact that Agent 4 was authorized to use C1 does not automatically establish authorization of D1 for every future use. Likewise, the production of E1 does not automatically inherit every authorization applicable to C1, D1, or C9.

Agent 15 later retrieves E1 for **Purpose Y**, which is outside the original authorization scope.

Agent 15 may be technically capable of retrieving or processing E1. That capability does not establish permission to use E1 for Purpose Y.

## 7.3 Complicating events

Before Agent 15's attempted reuse:

```text
CP-11 v2.2 → superseded by CP-11 v2.3
T_exp passes
C1 itself remains epistemically supportable
```

These changes affect governance state, not necessarily epistemic support.

The historical use by Agent 4 occurred while the relevant authorization was current. Expiration or policy supersession must not retroactively rewrite that legitimate historical action as unauthorized.

If C1, D1, or E1 is later proposed for another purpose, actor, workflow, or time period, the system must evaluate that proposed reuse against the governance state applicable to the new request. Expiration does not require deletion of the claim and does not prohibit future reuse categorically; it means the old authorization can no longer, by itself, establish current permission. A new or renewed authorization may be issued where the current policy permits reuse.

## 7.4 Expected system behavior

1. Preserve C1's historical authorization for Purpose X, including the actor, Constraint Package version, validity boundary, and authorizing human associated with that decision.
2. Preserve the historical fact that Agent 4's use of C1 occurred while the applicable authorization was current; later expiration or policy supersession must not retroactively convert that action into an unauthorized event.
3. Do not infer that authorization to use C1 automatically authorizes D1 for every downstream purpose, actor, or context.
4. Do not infer that authorization applicable to D1 automatically authorizes E1.
5. Treat authorization as contextual rather than as an intrinsic boolean property of C1, D1, or E1.
6. Distinguish epistemic dependency or support propagation from authorization propagation. Epistemic consequences may travel through dependency paths; permission does not automatically inherit through those paths.
7. Prevent Agent 15 from treating E1 as authorized for Purpose Y merely because its ancestors were historically approved or authorized.
8. Preserve and enforce actor restrictions associated with the relevant authorization context.
9. Recognize expiration of authorization without asserting that C1 became false, unsupported, deleted, or permanently unusable.
10. Permit an expired claim or artifact to be proposed for a new use, but require the new request to establish current authorization under the actor, purpose, scope, time, and governance conditions applicable to that reuse.
11. Recognize supersession of CP-11 v2.2 without rewriting the historical authorization event.
12. Require explicit evaluation of whether CP-11 v2.3 changes current admissibility or authorization requirements for a proposed reuse.
13. Detect scope drift from Purpose X to Purpose Y rather than treating purpose as incidental metadata.
14. Record attempted, blocked, or denied reuse without converting technical capability into authorization.
15. Prevent a downstream summary, paraphrase, transformation, new claim identifier, or other representational change from laundering restricted content into an unrestricted authorization state.
16. Prevent composition with a less-restricted or unrestricted input from silently washing restrictions off another input. In particular, `D1 + C9 → E1` must not imply that E1 is unrestricted merely because C9 is unrestricted or differently governed.
17. Keep epistemic state, verification state, constraint state, authorization state, and historical authority events independently queryable.
18. Require fresh or renewed human authorization where the currently applicable governance policy requires it.
19. At the point of consequential reuse or execution, evaluate `MAY` independently of `CAN`: successful retrieval, transformation, tool access, model capability, or possession of an artifact must not itself satisfy the authorization decision.
20. Provide an explanation for a blocked or permitted reuse that identifies the relevant actor, purpose, scope, Constraint Package/version, validity state, and authority basis rather than returning only a generic allowed/denied status.

## 7.5 Success question

Can the system prevent **authority and constraint laundering** across transformations, composition, agents, purposes, time, and constraint-package versions while preserving all of the following distinctions?

```text
CAN ≠ MAY

epistemically supportable
≠
historically authorized
≠
currently authorized
≠
authorized for this actor
≠
authorized for this purpose
```

Can an expired artifact remain available for legitimate future reassessment and reauthorization without allowing its historical authorization to function as permanent bearer permission?

---


# 2. TC07 — Compositional Failure Under Individually Admissible Inputs

## 8.1 Failure target

Test whether a system incorrectly assumes that epistemic support, admissibility, verification, or authorization established for individual inputs necessarily transfers to a new artifact produced by composing those inputs.

This test enforces the general distinction:

```text
properties of components
≠
properties of their composition
```

unless the relevant property and composition operation specifically justify inheritance.

The test does not prescribe a composition algorithm, constraint-resolution mechanism, inference engine, or internal VerityCore implementation. It specifies observable governance and provenance requirements only.

## 8.2 Initial state

Three claims are individually available within governed context:

```text
Claim C1: currently epistemically supportable
Claim C2: currently epistemically supportable
Claim C3: currently epistemically supportable
```

Each claim has its own provenance, verification state, and applicable authorization context.

Agent 16 is technically capable of retrieving all three and is permitted to use them as inputs to a bounded synthesis task:

```text
C1 ──┐
C2 ──┼── Agent 16 ──> Claim C4
C3 ──┘
```

No upstream source has expired, been retracted, or been superseded. No provenance path has collapsed. No circular ancestry is known. The inputs themselves remain individually supportable.

However, those facts do not establish that C4 is epistemically supported, admissible, verified, or authorized for downstream reuse.

For example, the inputs may state:

```text
C1: Under condition A, intervention X increases Y.
C2: Under condition B, increased Y is associated with reduced Z.
C3: Population P exhibits condition B.
```

Agent 16 composes them into:

```text
C4: Therefore intervention X reduces Z in population P.
```

C1, C2, and C3 may each remain individually supportable while C4 fails because the composition introduces an unsupported bridge, scope mismatch, causal inference, omitted condition, interaction, or other relation not established by the component claims.

The composition may therefore introduce latent intermediate propositions or assumptions that were not established by the inputs themselves:

```text
C1 + C2 + C3
      ↓
new bridge assumption(s) / scope transfer(s) / relational claim(s)
      ↓
C4
```

Those newly introduced bridges are part of the epistemic burden of C4 even if they are not emitted as separately named claims.

## 8.3 Composition event

```text
C1 + C2 + C3 ── Agent 16 ──> C4
```

The composition itself creates a new epistemic and governance event.

Conceptually:

```text
supported(C1)
AND supported(C2)
AND supported(C3)

does not by itself imply

supported(C4)
```

Likewise:

```text
MAY use C1 as input
AND MAY use C2 as input
AND MAY use C3 as input

does not by itself imply

MAY admit or reuse C4
```

The ability to perform the composition also does not establish permission:

```text
CAN compose ≠ MAY admit ≠ MAY reuse
```

## 8.4 Expected system behavior

1. Preserve the independent provenance, epistemic state, verification state, and authorization context of C1, C2, and C3.
2. Record the composition `C1 + C2 + C3 → C4` as a new derivational event rather than treating C4 as a passive inheritance of its inputs' states.
3. Do not infer that C4 is epistemically supported merely because each input is individually supportable.
4. Require newly introduced inferential bridges, assumptions, scope transfers, or relational claims to remain distinguishable from the verified input claims, whether represented explicitly as separate objects or otherwise recoverable at sufficient resolution for the applicable verification process.
5. Detect or permit review of scope mismatches, missing conditions, unsupported causal transitions, incompatible populations, contradictory assumptions, or other composition-specific defects where the retained representation makes them observable.
6. Do not retroactively mark C1, C2, or C3 false merely because C4 fails verification or is rejected.
7. Distinguish failure of the composition from failure of its components.
8. Do not infer that authorization to retrieve or use C1, C2, and C3 as inputs automatically authorizes admission, publication, execution, or downstream reuse of C4.
9. Evaluate the authorization and admissibility of C4 under the governance conditions applicable to the newly produced artifact and proposed use.
10. Prevent a less-restricted input from laundering restrictions associated with another input into an unrestricted output.
11. Preserve the historical fact that Agent 16 was technically capable of and, where applicable, authorized to perform the synthesis task even if C4 is subsequently rejected, withheld, or denied downstream reuse.
12. Enforce `CAN ≠ MAY` at the composition boundary: successful generation of C4 must not itself establish permission to admit or reuse C4.
13. Do not assume that a property established independently for all components is closed under composition unless the relevant property and composition operation justify that inference.
14. Where retained information is insufficient to determine whether a property composes safely, do not infer inheritance. Route the resulting artifact to the applicable unresolved, verification-required, or authorization-required state rather than treating either the composition or its inputs as false solely because compositional validity is undetermined.
15. Provide an impact explanation that distinguishes which inputs remained acceptable from which newly introduced relation, inference, scope transition, or governance condition caused C4 to require rejection, qualification, verification, or reassessment.

## 8.5 Success question

Can the system preserve individually valid or admissible inputs while independently evaluating the artifact produced by their composition, without assuming that epistemic support, constraint satisfaction, verification, or authorization is automatically closed under composition?

Can it preserve the distinctions:

```text
input support
≠
derivational validity
≠
compositional admissibility
≠
output verification
≠
output authorization

CAN compose
≠
MAY admit
≠
MAY reuse
```

without falsely invalidating the component claims when the composition itself fails?

---

# 8. Why TC02–TC07 Were Selected

These six cases are deliberately concentrated on anticipated high-risk multi-agent knowledge-generation patterns:

| Test | Primary failure pressure |
|---|---|
| TC02 | Convergent derivation + asynchronous epistemic/governance changes + downstream reliance |
| TC03 | Semantic equivalence incorrectly collapsing distinct provenance |
| TC04 | False independence / epistemic circularity across agent reuse |
| TC05 | Provenance loss through lossy summarization and abstraction |
| TC06 | Authorization and constraint state being laundered across transformations |
| TC07 | Compositional failure despite individually acceptable inputs; invalid inheritance of support, admissibility, verification, or authorization |

Together they pressure the system along different dimensions without attempting to enumerate every possible failure mode.

The next test cases should preferably emerge from observed behavior once the actual multi-agent workflow exists.


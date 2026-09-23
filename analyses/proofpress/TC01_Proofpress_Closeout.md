# TC01 Proofpress Closeout
## Implementation Review Against Adversarial Test Case 01 

**Prepared by:** D.S. Nelson  
**Research context:** Constrained Informational Systems (CIS) / anticipated VerityCore multi-agent workflows  
**Date:** 2026-09-22  
**Status:** Implementation-specific closeout analysis; architectural review complete, operational hardening remains

---

# 1. Purpose

This document records the Proofpress-specific implementation review and closeout analysis for Adversarial Test Case 01 (TC01). The canonical TC01 test specification is maintained separately in the system-neutral Adversarial Agent Governance Tests suite. This closeout document records observed implementation coverage, residual risks, and the conditions required for stronger operational and representational closeout.


---

# 2. TC01 Reference Scenario — Upstream Retraction Across Agent Handoffs

## 2.1 Original failure target

Initial structure:

```text
S1 ──supports──> C1
                  │
                  │ derivational dependency
                  ▼
                 C2
                  │
                  │ derivational dependency
                  ▼
                 C3 <──supports── S2

Agent 1: S1 → C1
Agent 2: C1 → C2
Agent 3: C2 + S2 → C3
Human H: C3 → APPROVED FOR REUSE
```

Event:

```text
S1 corrected
    ↓
C1 retracted
```

The required behavior is correction propagation without historical erasure, over-propagated falsity, or stale authorization.

## 2.2 PR #185 implementation evaluation

| # | TC01 expected behavior | PR #185 mechanism | Assessment |
|---|---|---|---|
| 1 | Preserve original historical chain | Append-only event history remains authoritative | IMPLEMENTED |
| 2 | Record S1 correction / C1 retraction without overwriting previous state | `claim_withdrawn` models C1 withdrawal while preserving prior admission/history; explicit source-correction semantics are not independently demonstrated by this mechanism | PARTIAL |
| 3 | Identify C2 as materially dependent on C1 | Governed `depends_on` relations + dependency impact projection | IMPLEMENTED |
| 4 | Identify C3 as transitively dependent on C1 through C2 | Reverse dependency propagation distinguishes direct/transitive impact | IMPLEMENTED |
| 5 | Preserve C3 independent support from S2 | Evidence and claim history remain intact during dependency invalidation/reassessment, but the implementation does not by itself establish that S2 is independently sufficient support | PARTIAL — HISTORY PRESERVED; SUFFICIENCY NOT MECHANIZED |
| 6 | Distinguish affected derivation C1→C2→C3 from S2→C3 support | Individual relations can remain, become invalid, or be retired without deleting history; richer support-logic semantics such as independent sufficiency or AND/OR structure are not established | PARTIAL |
| 7 | Flag C2 and C3 for reassessment | Derived `dependency_invalidated` state and reassessment workflow | IMPLEMENTED |
| 8 | Do not automatically declare C2/C3 false | Invalidated claims are blocked from governed reuse rather than asserted false | IMPLEMENTED |
| 9 | Permit C3 to stand if independent support remains sufficient | `claim.reassess` permits a human to retain/re-admit C3 after invalid dependencies are resolved, but the system does not independently prove S2's sufficiency | PARTIAL / PROCEDURAL |
| 10 | Preserve prior human approval while indicating epistemic basis changed | Review/admission history preserved; current state projected separately | IMPLEMENTED |
| 11 | Require renewed human approval after material epistemic change | Reassessment is owner-only and produces fresh Human Review + Admission | IMPLEMENTED |
| 12 | Distinguish historical workflow state from current epistemic/authorization state | Immutable event history + derived current projections | IMPLEMENTED |

## 2.3 Residual implementation risks observed in PR #185

These do not negate the architectural implementation of TC01, but they should be closed before treating the implementation as operationally hardened:

1. **Atomic optimistic concurrency:** validation of `expected_head` and append must be atomic so concurrent owner operations cannot commit decisions derived from stale projections.
2. **Staged traversal exclusion:** withdrawn or dependency-invalidated claims must not be recoverable through a staged traversal using preserved pre-withdrawal receipts.
3. **Terminal-state precedence:** withdrawn/superseded/expired claims should retain their terminal state rather than being incorrectly surfaced as merely `dependency_invalidated`.
4. **Relation reapproval in Owner UI:** a still-valid dependency that becomes unresolved because of policy change should be revalidatable rather than forcing retirement.
5. **Mutually exclusive summary counts:** dependency-invalidated claims should not be double-counted under both canonical state and a reassessment alias.

## 2.4 TC01 closeout criterion

TC01's **governance/lifecycle failure mode is substantially implemented**, while full representational satisfaction of the independent-support clauses remains partial and residual implementation hardening is still required.

A final operational closeout would require the residual defects above to be resolved and regression-tested, especially the concurrency and traversal-state defects. Full representational closeout would also require demonstrating the support semantics needed to distinguish preserved support history from independently sufficient support.


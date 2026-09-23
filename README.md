# Adversarial Agent Governance Tests

**Adversarial test cases for provenance, epistemic state, authority, constraint propagation, and compositional failure in multi-agent knowledge systems.**

## Overview

Adversarial Agent Governance Tests (AAGT) is an evolving collection of implementation-neutral test cases for examining how agentic knowledge systems behave when provenance, evidence, dependencies, transformations, constraints, authorization, and epistemic state change across multi-agent workflows.

The tests focus on failure modes that can emerge even when individual agents or components behave as designed.

They are intended to ask questions such as:

- What happens when an upstream source or claim changes after downstream agents have already consumed it?
- Can independent derivation paths remain distinguishable after semantic convergence?
- Can a system detect apparent corroboration that ultimately descends from the same epistemic ancestry?
- What happens when summarization preserves ancestry but destroys information needed to determine material impact?
- Can authorization or constraint state be accidentally laundered across agent handoffs?
- Can individually acceptable inputs produce an unacceptable or unsupported composition?

The suite specifies **observable adversarial conditions and expected governance properties**. It does not prescribe a particular internal architecture or implementation.

## Current Release

**v1.0 — TC01–TC07**

Canonical test suite:

- [AAGT_TC01-TC07_v1.0.md](tests/AAGT_TC01-TC07_v1.0.md)

System-specific analyses are maintained separately:

- [TC01 Proofpress Closeout](analyses/proofpress/TC01_Proofpress_Closeout.md)

## Current Test Suite

**TC01 — Upstream Retraction Across Agent Handoffs**  
Tests whether upstream correction or withdrawal can propagate through downstream dependencies without destroying history, over-propagating falsity, or treating stale authorization as current.

**TC02 — Convergent Derivation Under Asynchronous Epistemic and Governance Change**  
Tests multiple derivation paths when evidence and governance conditions change independently over time.

**TC03 — Semantic Convergence Without Provenance Collapse**  
Tests whether semantically equivalent claims retain distinct provenance trajectories and whether distinct provenance is incorrectly treated as proof of evidentiary independence.

**TC04 — Circular Epistemic Laundering Through Multi-Agent Reuse**  
Tests whether information derived from a source can return through downstream artifacts and be mistakenly treated as independent corroboration.

**TC05 — Lossy Summarization and Granularity Collapse**  
Tests whether systems can preserve dependency while recognizing that summarization or compression may destroy sufficient resolution for materiality analysis.

**TC06 — Authority and Constraint Laundering Across Agent Handoffs**  
Tests whether historical approval, actor permissions, scope, or constraint state are incorrectly treated as intrinsic and automatically inheritable properties of an artifact.

**TC07 — Compositional Failure Under Individually Admissible Inputs**  
Tests whether individually supportable, verified, admissible, or authorized inputs are incorrectly assumed to produce a supportable, verified, admissible, or authorized composition.

## Cross-Cutting Invariants

Several distinctions recur throughout the suite.

### Capability does not establish authorization

`CAN ≠ MAY`

Technical or operational capability to retrieve, transform, compose, generate, or execute does not establish current authorization to perform or rely upon that action.

### Uncertainty does not establish falsity

`UNKNOWN / NOT ESTABLISHED ≠ FALSE`

Failure to establish current support, admissibility, verification, authorization, or compositional validity does not by itself establish that a claim is false.

### History and current state are distinct

Systems should preserve historically accurate decisions and provenance while allowing current epistemic or governance state to change.

### Provenance and independence are distinct

Distinct artifacts or provenance paths do not necessarily establish independent evidentiary support.

### Component properties do not automatically survive composition

For an operation:

`y = f(x1, ..., xn)`

the suite does not assume:

`P(x1) ∧ ... ∧ P(xn) ⇒ P(y)`

unless there is an independent basis for establishing that property `P` is preserved under the relevant operation.

## Test Philosophy

These tests are intentionally architecture-neutral.

A system may use event sourcing, provenance graphs, relational storage, append-only ledgers, policy engines, human review, deterministic validation, model-based verification, or other mechanisms. The suite concerns itself primarily with **observable behavior and preserved distinctions**, rather than requiring a particular implementation.

The suite also does not assume that every unresolved condition should cause permanent rejection. Reassessment, renewed verification, qualification, renewed authorization, or other explicit state transitions may be appropriate depending on the system.

## Systems Under Test

TC01 originated during informal technical discussion around [Proofpress](https://github.com/chenmingtang830/proofpress) and was subsequently used to evaluate changes introduced in that project's claim-withdrawal and dependency-reassessment architecture.

The broader TC02–TC07 suite was developed from anticipated failure modes in provenance-heavy multi-agent research workflows.

Reference to a system in this repository does not imply endorsement, affiliation, certification, or a claim that the system currently fails a given test unless an accompanying analysis explicitly demonstrates that result.

## Repository Structure

```text
.
├── README.md
├── LICENSE
├── CITATION.cff
├── CONTRIBUTING.md
├── tests/
│   └── AAGT_TC01-TC07_v1.0.md
└── analyses/
    └── proofpress/
        └── TC01_Proofpress_Closeout.md
```

Future system-specific execution reports should be added under `analyses/<system>/` rather than modifying the canonical test specification.

## Status

The suite is deliberately bounded. Additional tests should preferably arise from observed implementation behavior, adversarial evaluation, or newly identified architectural failure modes rather than from indefinitely expanding a theoretical catalog.

## Authorship

Developed by **D.S. Nelson / Syntropy Systems Labs**.

## License

This repository is licensed under the **Apache License 2.0**. See [LICENSE](LICENSE).

The license applies to the materials in this repository. It does **not** grant rights to separate unpublished or proprietary Syntropy Systems Labs architectures, implementations, schemas, constraint-resolution mechanisms, orchestration logic, or other material not included in this repository.

## Citation

Citation metadata is provided in [CITATION.cff](CITATION.cff).

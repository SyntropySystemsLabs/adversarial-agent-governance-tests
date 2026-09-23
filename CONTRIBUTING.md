# Contributing

Contributions that improve the rigor, reproducibility, or coverage of the Adversarial Agent Governance Tests are welcome.

## Useful Contributions

Useful contributions include:

* new adversarial test cases derived from observed system behavior;
* refinements that make existing expectations more implementation-neutral;
* reproducible execution results against specific systems;
* counterexamples showing that an expected behavior is underspecified or incorrect;
* additional failure conditions;
* terminology corrections;
* references to relevant research or prior art.

## New Test Cases

Proposed test cases should identify, where applicable:

1. the failure target;
2. initial system state;
3. relevant agents, claims, sources, constraints, or artifacts;
4. triggering events;
5. expected observable behavior;
6. prohibited or unsafe state collapses;
7. success criteria;
8. unresolved questions.

Tests should describe **what a system must preserve or distinguish** without unnecessarily prescribing how the system must implement that behavior.

## Implementation Results

Results against a particular system should distinguish among:

* implemented;
* partially implemented;
* not implemented;
* not established;
* not applicable;
* unable to determine.

`NOT ESTABLISHED` must not be silently interpreted as failure.

## Scope

This repository concerns adversarial epistemic and governance behavior in agentic knowledge systems.

Implementation-specific architectural proposals may be useful as discussion material, but test requirements should remain implementation-neutral wherever possible.

## Contributions and Licensing

By submitting a contribution to this repository, you agree that your contribution may be distributed under the repository's license.

Please identify external sources, prior work, or third-party material where applicable.

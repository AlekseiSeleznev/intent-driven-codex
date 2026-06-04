## TDD Strategy

<!-- State how the project-local `tdd` skill will be applied. Tests/checks should verify observable behavior through public interfaces, not implementation details. -->

## Vertical TDD Slices

<!-- One RED -> GREEN -> REFACTOR slice per behavior. Do not plan horizontal all-tests-first/all-code-later work. -->
| Slice | Public interface / behavior | RED command and expected failure | GREEN command and expected pass | Refactor criteria |
| --- | --- | --- | --- | --- |
| <!-- slice --> | <!-- interface/behavior --> | <!-- command + expected failure --> | <!-- command + expected pass --> | <!-- refactor guardrails --> |

## Mocking / Boundary Policy

<!-- Mock only system boundaries when unavoidable. Do not mock internal collaborators you control. -->

## Required Checks

<!-- Checks required before marking apply complete and before archive. Include OpenSpec validation and project-specific commands. -->

## Evidence Log

<!-- Fill during apply/verify with RED/GREEN command outputs or references. -->

## TDD Exceptions

<!-- Explicit, pre-approved exceptions only. If none, write `None`. -->

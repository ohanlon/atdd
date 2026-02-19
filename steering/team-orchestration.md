---
name: Team Orchestration
description: Orchestrates an agent team for ATDD workflow
inclusion: manual
---

# ATDD Team Orchestration

Guide for orchestrating a team of agents to follow the ATDD workflow for larger features.

## Team Roles

### Team Lead (You or a general-purpose agent)
- Orchestrates workflow
- Reviews specs
- Approves all work
- Enforces discipline

### Spec Writer (general-purpose agent)
- Writes Given/When/Then specs from feature requirements
- Uses domain language only
- Follows ATDD workflow strictly

### Implementer (general-purpose agent)
- Builds code using TDD
- Unit tests first, then implementation
- Works until both test streams pass

### Spec Guardian (general-purpose agent)
- Reviews specs for implementation leakage
- Checks before and after implementation

## Workflow Phases

### Phase 1: Spec Writing
Delegate to spec-writer with clear instructions about domain language and external observables.

### Phase 2: Spec Review
Delegate to spec-guardian to check for implementation leakage.

### Phase 3: Pipeline Generation
Generate the test pipeline (parser → IR → generator).

### Phase 4: Implementation
Delegate to implementer with TDD discipline.

### Phase 5: Post-Implementation Review
Final review by spec-guardian and code quality check.

## Key Principles

- Never let implementers modify specs
- Run spec-check twice (before and after implementation)
- Keep specs portable
- Scope tightly (one feature per cycle)
- Verify both test streams pass

Refer to the atdd-workflow steering file for detailed instructions for each phase.

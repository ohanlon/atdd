# ATDD Power for Kiro

A Kiro power that implements Acceptance Test Driven Development (ATDD) methodology for building software with AI assistance.

## Installation

This power is located in `.kiro/powers/atdd/`. To use it with Kiro, the power system will automatically detect it.

## What's Included

### Steering Files

- **atdd-workflow.md** - Core 7-step ATDD workflow guide
- **spec-guardian.md** - Reviews specs for implementation leakage
- **pipeline-builder.md** - Generates test pipelines (parser → IR → generator)
- **team-orchestration.md** - Orchestrates agent teams for ATDD

### Hooks

- **atdd-pre-code-write.json** - Warns when writing code without acceptance specs
- **atdd-verify-tests.json** - Reminds to verify both test streams pass

## Quick Start

1. Load the ATDD workflow:
   ```
   Use the #atdd-workflow steering file to start building a feature with ATDD
   ```

2. Write your specs in `specs/[feature-name].txt`:
   ```
   ;===============================================================
   ; User can register with email and password.
   ;===============================================================
   GIVEN no registered users.
   
   WHEN a user registers with email "bob@example.com" and password "secret123".
   
   THEN there is 1 registered user.
   THEN the user "bob@example.com" can log in.
   ```

3. Follow the workflow to generate tests and implement with TDD

## Key Concepts

### Two Test Streams

1. **Acceptance Tests** - Define WHAT (external behavior in domain language)
2. **Unit Tests** - Define HOW (internal structure and implementation)

Both must pass. The tension between them forces better design.

### External Observables Only

Specs describe what users see, not how the system works internally:

❌ Bad: "GIVEN the UserService has an empty userRepository"
✅ Good: "GIVEN there are no registered users"

### The Pipeline

Generate a bespoke test pipeline for your project:
- Parser reads specs
- IR captures domain operations
- Generator produces runnable tests with deep codebase knowledge

## Attribution

Based on Robert C. Martin's (Uncle Bob) ATDD methodology from empire-2025.
Converted from the Claude Code plugin at https://github.com/swingerman/atdd

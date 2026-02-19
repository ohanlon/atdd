---
name: ATDD Workflow
description: Core 7-step ATDD workflow for building features with acceptance tests
inclusion: manual
---

# ATDD Workflow

You are guiding the user through the Acceptance Test Driven Development (ATDD) workflow. Follow these steps strictly:

## Step 1: Understand the Feature

Ask clarifying questions about the feature to understand:
- What external behavior needs to be implemented?
- What are the observable outcomes from a user's perspective?
- What domain language does the project use?

CRITICAL: Avoid implementation details. Focus on WHAT the system should do, not HOW.

## Step 2: Write Given/When/Then Specs

Create a spec file in `specs/[feature-name].txt` with this format:

```
;===============================================================
; [Description of the behavior being specified]
;===============================================================
GIVEN [precondition in domain language].

WHEN [action the user/system takes].

THEN [observable outcome].
```

### Rules for Writing Specs:

1. Use ONLY external observables — no class names, API endpoints, database tables, or framework terms
2. Each spec file starts with a semicolon comment block describing the behavior
3. Use periods at the end of each statement
4. Use domain language that a non-developer could understand
5. Each spec should test ONE behavior
6. Specs should be portable — could work for a different implementation language

### Examples of Domain Language vs Implementation Leakage:

❌ BAD (Implementation Leakage):
- "GIVEN the UserService has an empty userRepository"
- "WHEN a POST request is sent to /api/users"
- "THEN the database contains 1 row in the users table"

✅ GOOD (Domain Language):
- "GIVEN there are no registered users"
- "WHEN a new user registers with email 'bob@example.com'"
- "THEN there is 1 registered user"

## Step 3: Review Specs for Leakage

Before proceeding, review the specs and flag ANY of these:
- Class names, function names, method names
- Database tables, columns, queries
- API endpoints, HTTP methods, status codes
- Framework terms (controller, service, repository, middleware)
- Internal state or data structures
- File paths or module names

If found, rewrite using domain language only.

## Step 4: Generate Test Pipeline

Analyze the project to create a 3-stage pipeline:

1. **Parser** — reads `specs/*.txt`, outputs IR to `acceptance-pipeline/ir/`
2. **Generator** — reads IR, produces runnable tests in `generated-acceptance-tests/`
3. **Runner script** — `run-acceptance-tests.sh` (or appropriate for the platform)

The generator must have DEEP knowledge of the codebase internals. Generated tests should call directly into the system — no manual fixture glue.

### Pipeline Requirements:

- Analyze the project's language and test framework
- Study existing test patterns in the codebase
- Generate tests that match the project's testing style
- Ensure tests can run independently

## Step 5: Run Acceptance Tests (Red)

Execute the acceptance tests. They MUST fail (red).

If they pass, either:
- The behavior already exists, or
- The generator isn't testing the right thing

## Step 6: Implement with TDD (Red → Green → Refactor)

Follow strict TDD discipline:

1. Run acceptance tests first — confirm they FAIL
2. Pick the simplest failing acceptance test
3. Write a unit test for the smallest piece needed
4. Write minimal code to make the unit test pass
5. Refactor
6. Repeat steps 3-5 until that acceptance test passes
7. Move to the next failing acceptance test
8. Continue until ALL acceptance tests AND unit tests pass

### Implementation Rules:

- NEVER modify spec files (`specs/*.txt`) — they are the contract
- NEVER modify generated test files — only regenerate via the pipeline
- If a spec seems wrong or ambiguous, STOP and ask the user
- Run both test streams before reporting done:
  - `./run-acceptance-tests.sh` (acceptance tests)
  - Project's unit test command (unit tests)

## Step 7: Post-Implementation Review

Perform two reviews:

### 1. Spec Review
Check if any implementation details leaked into specs during development. Look for:
- Any terms that reference internal implementation
- Any language that wouldn't make sense to a non-developer
- Any specs that are too tightly coupled to the current implementation

### 2. Code Review
Review the implementation for:
- Test quality (are unit tests testing the right things?)
- Code structure (does it match what the specs describe?)
- Missing edge cases (any specs that should be added?)

## Iteration

After completing a feature, return to Step 1 for the next feature. Keep the cycle tight:
- Spec just enough for the current feature
- Implement it
- Review it
- Move to the next feature

## Two Test Streams Philosophy

The power of ATDD comes from maintaining TWO independent test streams:

1. **Acceptance Tests** (WHAT) — Define external behavior in domain language
2. **Unit Tests** (HOW) — Define internal structure and implementation details

Both streams must pass. The tension between them forces better design decisions and prevents arbitrary code placement.

"The two different streams of tests cause the AI to think much more deeply about the structure of the code. It can't just willy-nilly plop code around and write a unit test for it. It is also constrained by the structure of the acceptance tests."
— Robert C. Martin

# ATDD Power - Acceptance Test Driven Development

A Kiro power that enforces the Acceptance Test Driven Development (ATDD) methodology when building software with AI. Write human-readable Given/When/Then specs before code, generate project-specific test pipelines, and maintain the discipline of two-stream testing.

Inspired by Robert C. Martin's (Uncle Bob) acceptance test approach. The ideas, methodology, and key insights come from his work on Spec Driven Design (SDD) and ATDD.

## Why ATDD with AI?

When using AI to write code, two problems emerge:

1. **AI writes code without constraints** — without acceptance tests anchoring behavior, AI can write code arbitrarily and write unit tests that pass but don't verify the right behavior.
2. **Implementation details leak into specs** — AI naturally tries to fill Given/When/Then statements with class names, API endpoints, and database tables instead of domain language.

This power solves both problems by enforcing the ATDD workflow and guarding against implementation leakage. The result: two test streams (acceptance + unit) that constrain AI development, producing better-structured, more reliable code.

## How It Works

1. Write Given/When/Then specs (natural language, domain-only)
2. Generate test pipeline (parser → IR → test generator)
   - Pipeline has DEEP knowledge of your codebase internals
3. Run acceptance tests → they FAIL (red)
4. Implement with TDD (unit tests + code) until BOTH streams pass
5. Review specs for implementation leakage
6. Iterate — next feature, back to step 1

The generated pipeline is NOT like Cucumber. It's a hybrid where the parser/generator has deep knowledge of your system's internals and produces complete, runnable tests. No manual fixture code needed.

## Getting Started

Use the `atdd-workflow` steering file to start the ATDD process for a new feature.

## GWT Spec Format

Specs use an opinionated, human-readable Given/When/Then format:

```
;===============================================================
; Description of the behavior being specified.
;===============================================================
GIVEN [precondition in domain language].

WHEN [action the user/system takes].

THEN [observable outcome].
```

## The Golden Rule: External Observables Only

Specs must describe what the system does, not how it does it:

| Bad (implementation leakage) | Good (domain language) |
|------------------------------|------------------------|
| GIVEN the UserService has an empty userRepository. | GIVEN there are no registered users. |
| WHEN a POST request is sent to /api/users with JSON body. | WHEN a new user registers with email "bob@example.com". |
| THEN the database contains 1 row in the users table. | THEN there is 1 registered user. |

## Key Principles

- **Just enough specs for this sprint** — Don't write all specs upfront. Spec the current feature, implement it, iterate.
- **Two test streams constrain development** — Acceptance tests define WHAT (external behavior), unit tests define HOW (internal structure). Both must pass.
- **Specs describe only external observables** — No class names, API endpoints, database tables, or framework terms in specs. Domain language only.
- **Co-authored by humans and AI, ferociously defended by humans** — AI proposes specs, you approve them. Always.
- **Small steps** — Whether your sprint is a day, an hour, or a microsecond, the same rules apply.

## Attribution

This power is an implementation of Robert C. Martin's (Uncle Bob) Acceptance Test Driven Development and Spec Driven Design methodology. The approach, insights, and principles come from his work on empire-2025 and his public writings on ATDD and SDD.

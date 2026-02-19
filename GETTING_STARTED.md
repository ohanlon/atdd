# Getting Started with ATDD Power

This guide will walk you through using the ATDD power in Kiro to build a feature with acceptance test driven development.

## Prerequisites

- A project with a test framework set up (pytest, Jest, JUnit, etc.)
- Basic understanding of test-driven development

## Step-by-Step Guide

### 1. Start with a Feature

Decide what feature you want to build. For this example, let's say "User Registration".

### 2. Load the ATDD Workflow

In Kiro, reference the ATDD workflow steering file:

```
I want to build user registration using ATDD. Use #atdd-workflow to guide me.
```

### 3. Write Your First Spec

Create a file `specs/user-registration.txt` in your project root:

```
;===============================================================
; User can register with email and password.
;===============================================================
GIVEN no registered users.

WHEN a user registers with email "bob@example.com" and password "secret123".

THEN there is 1 registered user.
THEN the user "bob@example.com" can log in.
```

### 4. Review for Implementation Leakage

Ask Kiro to review your spec:

```
Review specs/user-registration.txt using #spec-guardian
```

The spec guardian will check for any implementation details that leaked in.

### 5. Generate the Test Pipeline

Ask Kiro to build the pipeline:

```
Generate the acceptance test pipeline for this project using #pipeline-builder
```

This will create:
- Parser (reads specs)
- IR format (intermediate representation)
- Generator (creates runnable tests)
- Runner script

### 6. Run Tests (They Should Fail)

```
Run the acceptance tests - they should fail since we haven't implemented anything yet
```

### 7. Implement with TDD

Now implement the feature using test-driven development:

```
Implement user registration using TDD:
1. Write unit tests first
2. Write minimal code to pass
3. Refactor
4. Repeat until acceptance tests pass
```

### 8. Verify Both Streams

Before finishing, verify both test streams pass:

```bash
# Acceptance tests
./run-acceptance-tests.sh

# Unit tests  
npm test  # or pytest, mvn test, go test, etc.
```

### 9. Final Review

Do a final review:

```
Review the implementation:
1. Check specs for any leakage that crept in
2. Review code quality
3. Check for missing edge cases
```

## Example Workflow in Kiro

Here's a complete conversation flow:

```
You: I want to build user authentication with ATDD. Use #atdd-workflow

Kiro: I'll guide you through the ATDD workflow. First, let me understand 
the feature. What authentication methods do you want to support?

You: Email and password registration, then login

Kiro: Great. Let's write the acceptance specs. I'll create 
specs/user-authentication.txt with Given/When/Then statements...

[Kiro creates the spec file]

You: Review the specs using #spec-guardian

Kiro: Reviewing specs/user-authentication.txt for implementation leakage...
[Provides review]

You: Generate the test pipeline using #pipeline-builder

Kiro: Analyzing your project... I see you're using Python with pytest.
I'll generate a pipeline that creates pytest tests...
[Generates parser, IR format, generator, and runner]

You: Run the acceptance tests

Kiro: Running ./run-acceptance-tests.sh...
All tests failed (as expected - red phase). Ready to implement?

You: Yes, implement with TDD

Kiro: Starting TDD implementation...
[Writes unit tests, then code, iteratively until acceptance tests pass]

You: Verify both test streams

Kiro: Running both streams...
✓ Acceptance tests: 3 passed
✓ Unit tests: 12 passed
Both streams are green!
```

## Tips

1. **Start Small** - Begin with one simple behavior, not the entire feature
2. **Domain Language** - Use terms your users would understand
3. **One Behavior Per Spec** - Each spec should test one thing
4. **Defend Your Specs** - Don't let implementation details creep in
5. **Both Streams Must Pass** - Never skip either acceptance or unit tests

## Common Pitfalls

### ❌ Writing Implementation Details in Specs

```
BAD: GIVEN the UserRepository is empty
GOOD: GIVEN there are no registered users
```

### ❌ Skipping the Red Phase

Always verify acceptance tests fail before implementing. If they pass immediately, something's wrong.

### ❌ Modifying Generated Tests

Never manually edit generated acceptance tests. Always regenerate through the pipeline.

### ❌ Only Running One Test Stream

Both acceptance AND unit tests must pass. Don't skip either.

## Next Steps

- Check out the example specs in `examples/user-registration.txt`
- Read the full ATDD workflow in `steering/atdd-workflow.md`
- Learn about team orchestration in `steering/team-orchestration.md`

## Need Help?

Ask Kiro to load the relevant steering file:

- General workflow: `Use #atdd-workflow`
- Spec review: `Use #spec-guardian`
- Pipeline generation: `Use #pipeline-builder`
- Team coordination: `Use #team-orchestration`

---
name: Pipeline Builder
description: Generates bespoke parser → IR → test generator for your project
inclusion: manual
---

# Pipeline Builder

You are the Pipeline Builder. Your job is to generate a project-specific test pipeline that transforms Given/When/Then specs into runnable acceptance tests.

## Pipeline Architecture

Create a 3-stage pipeline:

```
specs/*.txt → [Parser] → IR (JSON/YAML) → [Generator] → runnable tests
```

### Stage 1: Parser
- Reads spec files from `specs/` directory
- Parses Given/When/Then statements
- Outputs Intermediate Representation (IR) to `acceptance-pipeline/ir/`

### Stage 2: Generator
- Reads IR files
- Has DEEP knowledge of the codebase internals
- Generates complete, runnable tests in `generated-acceptance-tests/`
- No manual fixture glue needed

### Stage 3: Runner
- Script to execute all generated tests
- Platform-appropriate (`.sh` for Unix, `.bat` or `.ps1` for Windows)
- Reports results clearly

## Key Principle: Deep Knowledge

This is NOT Cucumber. The generator must have deep knowledge of your system's internals:

- How to set up test data
- How to invoke system operations
- How to query system state
- How to clean up after tests

The generated tests should call directly into the system, not through a generic fixture layer.

## Analysis Phase

Before generating the pipeline, analyze:

### 1. Project Language & Framework
- What language is the project written in? (Python, TypeScript, Java, Go, Ruby, etc.)
- What test framework is used? (pytest, Jest, JUnit, Go testing, RSpec, etc.)
- What's the project structure?

### 2. Existing Test Patterns
- How are tests currently organized?
- What test utilities or helpers exist?
- How is test data set up and torn down?
- What assertions are commonly used?

### 3. Domain Model
- What are the core domain entities? (User, Order, Product, etc.)
- How are they created and manipulated?
- What operations are available?
- How is state queried?

### 4. Spec Requirements
- What Given/When/Then statements are in the specs?
- What domain operations do they reference?
- What state needs to be set up and verified?

## IR Format

Design an Intermediate Representation that captures:

```json
{
  "spec_file": "user-registration.txt",
  "description": "User can register with email and password",
  "scenarios": [
    {
      "given": [
        {
          "statement": "no registered users",
          "domain_operation": "clear_users"
        }
      ],
      "when": [
        {
          "statement": "a user registers with email \"bob@example.com\" and password \"secret123\"",
          "domain_operation": "register_user",
          "parameters": {
            "email": "bob@example.com",
            "password": "secret123"
          }
        }
      ],
      "then": [
        {
          "statement": "there is 1 registered user",
          "domain_query": "count_users",
          "expected": 1
        },
        {
          "statement": "the user \"bob@example.com\" can log in",
          "domain_operation": "login_user",
          "parameters": {
            "email": "bob@example.com",
            "password": "secret123"
          },
          "expected": "success"
        }
      ]
    }
  ]
}
```

The IR should:
- Preserve the original spec statements
- Map domain language to system operations
- Extract parameters from statements
- Define expected outcomes

## Generator Implementation

The generator must:

### 1. Map Domain Operations to Code

For each domain operation in the IR, generate code that:
- Sets up necessary state
- Invokes the appropriate system functions/methods
- Captures results for verification

Example mapping:
- "no registered users" → Clear database/storage, reset state
- "a user registers" → Call registration function/API
- "there is 1 registered user" → Query user count, assert equals 1

### 2. Generate Complete Test Functions

Each scenario becomes a complete test function:

```python
# Example for pytest
def test_user_can_register_with_email_and_password():
    """User can register with email and password."""
    
    # GIVEN no registered users
    clear_all_users()
    
    # WHEN a user registers with email "bob@example.com" and password "secret123"
    result = register_user(email="bob@example.com", password="secret123")
    
    # THEN there is 1 registered user
    assert count_users() == 1
    
    # THEN the user "bob@example.com" can log in
    login_result = login_user(email="bob@example.com", password="secret123")
    assert login_result.success == True
```

### 3. Include Test Utilities

Generate or reference utilities for:
- Test data setup and teardown
- Common assertions
- Helper functions for domain operations

### 4. Handle Test Isolation

Ensure tests can run independently:
- Set up required state in each test
- Clean up after each test
- Don't rely on test execution order

## Language-Specific Examples

### Python/pytest

```python
# generated-acceptance-tests/test_user_registration.py
import pytest
from app.domain import register_user, login_user, count_users, clear_all_users

def test_user_can_register_with_email_and_password():
    """User can register with email and password."""
    # GIVEN no registered users
    clear_all_users()
    
    # WHEN a user registers
    register_user(email="bob@example.com", password="secret123")
    
    # THEN there is 1 registered user
    assert count_users() == 1
    
    # THEN the user can log in
    result = login_user(email="bob@example.com", password="secret123")
    assert result.success
```

### TypeScript/Jest

```typescript
// generated-acceptance-tests/user-registration.test.ts
import { registerUser, loginUser, countUsers, clearAllUsers } from '../src/domain';

describe('User Registration', () => {
  test('user can register with email and password', async () => {
    // GIVEN no registered users
    await clearAllUsers();
    
    // WHEN a user registers
    await registerUser({ email: 'bob@example.com', password: 'secret123' });
    
    // THEN there is 1 registered user
    expect(await countUsers()).toBe(1);
    
    // THEN the user can log in
    const result = await loginUser({ email: 'bob@example.com', password: 'secret123' });
    expect(result.success).toBe(true);
  });
});
```

### Java/JUnit

```java
// generated-acceptance-tests/UserRegistrationTest.java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

class UserRegistrationTest {
    @Test
    void userCanRegisterWithEmailAndPassword() {
        // GIVEN no registered users
        Domain.clearAllUsers();
        
        // WHEN a user registers
        Domain.registerUser("bob@example.com", "secret123");
        
        // THEN there is 1 registered user
        assertEquals(1, Domain.countUsers());
        
        // THEN the user can log in
        LoginResult result = Domain.loginUser("bob@example.com", "secret123");
        assertTrue(result.isSuccess());
    }
}
```

## Runner Script

Generate a runner script appropriate for the platform:

### Unix/Linux/Mac (run-acceptance-tests.sh)

```bash
#!/bin/bash
set -e

echo "Running acceptance tests..."

# Run based on project type
if [ -f "pytest.ini" ] || [ -f "pyproject.toml" ]; then
    pytest generated-acceptance-tests/ -v
elif [ -f "package.json" ]; then
    npm test -- generated-acceptance-tests/
elif [ -f "pom.xml" ]; then
    mvn test -Dtest="generated-acceptance-tests/**"
elif [ -f "go.mod" ]; then
    go test ./generated-acceptance-tests/...
fi

echo "Acceptance tests complete!"
```

### Windows (run-acceptance-tests.bat or .ps1)

Adapt for Windows command line or PowerShell.

## Verification

After generating the pipeline:

1. Run the parser on existing specs
2. Verify IR is generated correctly
3. Run the generator on the IR
4. Verify tests are generated
5. Run the tests — they should FAIL (red)
6. If tests pass, investigate why (behavior might already exist)

## Regeneration

The pipeline should be regeneratable:
- If specs change, rerun parser and generator
- If domain model changes, update generator mappings
- Never manually edit generated tests — always regenerate

## Documentation

Generate a README in `acceptance-pipeline/` explaining:
- How the pipeline works
- How to run it
- How to add new domain operation mappings
- How to regenerate tests

## Key Insight

The generator is NOT a generic tool. It's bespoke for this project, with deep knowledge of:
- How this system works
- How to test this system
- What operations are available
- How to verify outcomes

This is what makes it powerful — and different from Cucumber.

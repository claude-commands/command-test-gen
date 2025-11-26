---
argument-hint: "<file|function>"
description: "Generate comprehensive tests for code"
model: claude-opus-4-5-20251101
allowed-tools: ["Bash", "Read", "Write", "Glob", "Grep", "AskUserQuestion"]
---

**If `$ARGUMENTS` is empty or not provided:**

Display usage information and ask for input:

**Usage:** `/test-gen <target>`

**Examples:**

- `/test-gen src/auth/login.ts` - Generate tests for a file
- `/test-gen handleAuthentication` - Generate tests for a function
- `/test-gen src/utils/` - Generate tests for a module

**Workflow:**

1. Detect your project's testing framework
2. Analyze the target code structure
3. Generate comprehensive test cases (happy path, edge cases, errors)
4. Create test file following project patterns
5. Self-review for coverage gaps

Ask the user: "What file or function would you like me to generate tests for?"

---

**If `$ARGUMENTS` is provided:**

Generate comprehensive tests for the specified code. Follows project testing patterns and includes
edge cases, boundary conditions, and error scenarios.

## Configuration

- **Target**: `$ARGUMENTS` (file path or function name)

## Steps

1. **Detect Testing Environment**

   Identify the project's testing setup:
   - **Go**: Look for `_test.go` files, check for testify, gomock
   - **TypeScript/JavaScript**: Check for jest.config, vitest.config, mocha, or test scripts in package.json
   - **Python**: Look for pytest.ini, conftest.py, unittest patterns
   - **Other**: Examine existing test files to infer patterns

   Also detect:
   - Test file naming convention (`*.test.ts`, `*_test.go`, `test_*.py`)
   - Test directory structure (`__tests__/`, `tests/`, alongside source)
   - Assertion library (expect, assert, should)
   - Mocking patterns (jest.mock, gomock, unittest.mock)

2. **Analyze Target Code**

   Read the target file/function and extract:
   - Function signatures and parameters
   - Return types and possible values
   - Dependencies and external calls
   - Error conditions and exceptions
   - Side effects (mutations, I/O, network)
   - Conditional branches and logic paths

3. **Identify Test Cases**

   Generate tests for:

   **Happy Path**
   - Basic functionality with typical inputs
   - Expected return values
   - Standard use cases

   **Edge Cases**
   - Empty inputs (null, undefined, "", [], {})
   - Boundary values (0, -1, MAX_INT, empty string)
   - Single element vs multiple elements
   - Unicode and special characters

   **Error Scenarios**
   - Invalid input types
   - Out-of-range values
   - Missing required parameters
   - Network/I/O failures (for async code)
   - Permission errors

   **Concurrency** (if applicable)
   - Race conditions
   - Deadlock scenarios
   - Parallel execution

4. **Generate Test Code**

   Follow project conventions for:
   - Test file location and naming
   - Import/require statements
   - Test suite organization (describe/it, t.Run)
   - Setup and teardown (beforeEach, t.Cleanup)
   - Assertion style

   Structure tests as:

   ```text
   describe('FunctionName', () => {
     describe('happy path', () => {
       it('should handle typical input', () => {...})
       it('should return expected output', () => {...})
     })

     describe('edge cases', () => {
       it('should handle empty input', () => {...})
       it('should handle boundary values', () => {...})
     })

     describe('error handling', () => {
       it('should throw on invalid input', () => {...})
       it('should handle network errors', () => {...})
     })
   })
   ```text

5. **Add Mocking** (when needed)

   For dependencies:
   - Mock external services/APIs
   - Stub database calls
   - Fake timers for time-dependent code
   - Mock file system operations

   Follow project's mocking patterns.

6. **Generate Test Data**

   Create:
   - Fixtures for complex objects
   - Factory functions for repeated patterns
   - Realistic sample data (not just "test", "foo", "bar")
   - Data that exercises edge cases

7. **Self-Review**

   Before outputting, check:
   - [ ] Are all public functions/methods covered?
   - [ ] Are edge cases covered?
   - [ ] Are error paths tested?
   - [ ] Do tests follow project conventions?
   - [ ] Are assertions meaningful (not just `toBeDefined`)?
   - [ ] Is there anything I'm not testing?

8. **Output**

   Provide:
   - Complete test file ready to save
   - Explanation of test strategy
   - List of any assumptions made
   - Suggestions for additional tests that require manual setup

## Output Format

```typescript
// filepath: src/__tests__/auth.test.ts

import { authenticate } from '../auth'

describe('authenticate', () => {
  // Happy path
  it('should return user for valid credentials', async () => {
    const result = await authenticate('user@example.com', 'password123')
    expect(result).toMatchObject({
      id: expect.any(String),
      email: 'user@example.com'
    })
  })

  // Edge cases
  it('should handle empty email', async () => {
    await expect(authenticate('', 'password'))
      .rejects.toThrow('Email is required')
  })

  // Error scenarios
  it('should throw for invalid credentials', async () => {
    await expect(authenticate('user@example.com', 'wrong'))
      .rejects.toThrow('Invalid credentials')
  })
})
```text

## Notes

- Prioritize testing behavior over implementation details
- Tests should be deterministic and independent
- Use descriptive test names that explain expected behavior
- Avoid testing private/internal functions directly
- If existing tests exist, extend rather than replace
- Ask before overwriting existing test files

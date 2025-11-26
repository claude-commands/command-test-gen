# command-test-gen

A Claude Code slash command for generating comprehensive tests for any code.

## Installation

```bash
# Clone to your preferred location
git clone git@github.com:claude-commands/command-test-gen.git <clone-path>/command-test-gen

# Symlink (use full path to cloned repo)
ln -s <clone-path>/command-test-gen/test-gen.md ~/.claude/commands/test-gen.md
```

## Usage

```text
/test-gen src/auth/login.ts         # Generate tests for a file
/test-gen handleAuthentication      # Generate tests for a function
```

## What it does

1. Detects your project's testing framework and conventions
2. Analyzes the target code structure and logic paths
3. Generates comprehensive test cases including edge cases
4. Creates test file following project patterns
5. Self-reviews for coverage gaps

## Output Format

```typescript
// src/__tests__/auth.test.ts

describe('authenticate', () => {
  describe('happy path', () => {
    it('should return user for valid credentials', async () => {
      const result = await authenticate('user@example.com', 'pass')
      expect(result.email).toBe('user@example.com')
    })
  })

  describe('edge cases', () => {
    it('should handle empty email', async () => {
      await expect(authenticate('', 'pass'))
        .rejects.toThrow('Email is required')
    })
  })

  describe('error handling', () => {
    it('should throw for invalid credentials', async () => {
      await expect(authenticate('user@example.com', 'wrong'))
        .rejects.toThrow('Invalid credentials')
    })
  })
})
```

## Test Categories

| Category | What's Tested |
|----------|---------------|
| Happy Path | Normal inputs, expected outputs |
| Edge Cases | Empty inputs, boundaries, special chars |
| Error Handling | Invalid inputs, exceptions, failures |
| Concurrency | Race conditions, parallel execution |

## Supported Frameworks

| Language | Frameworks |
|----------|------------|
| Go | testing, testify, gomock |
| TypeScript/JS | Jest, Vitest, Mocha |
| Python | pytest, unittest |

## Features

- **Auto-detection**: Finds your testing framework automatically
- **Pattern matching**: Follows your existing test conventions
- **Edge case discovery**: Identifies cases humans often miss
- **Mock generation**: Creates appropriate mocks for dependencies
- **Self-review**: Checks for coverage gaps before output

## Requirements

- Git repository with source code
- Existing tests (for pattern detection) - optional but recommended
- Claude Code with Opus 4.5 model access

## Updates

```bash
cd <clone-path>/command-test-gen && git pull
```

---
name: dfds-tdd
description: Test-Driven Development workflow. Use this skill for ALL implementation tasks — features, bug fixes, new domain models, new endpoints, new UI components. TDD is the default development approach.
argument-hint: <description of what to implement>
metadata:
  author: dfds
  version: 1.0.0
---

# Test-Driven Development (TDD)
Implement `$ARGUMENTS` using strict RED-GREEN-REFACTOR TDD.

No production code without a failing test first.

## RED-GREEN-REFACTOR Cycle

### RED: Write a Failing Test First

- Write a test that describes the desired **behavior**, not the implementation
- Run the test — confirm it **fails for the right reason** (not a syntax error or missing import)
- The failing test defines the next increment of work

### GREEN: Minimum Code to Pass

- Write **only** enough production code to make the failing test pass
- Resist adding functionality not demanded by a test
- Run the test — confirm it passes

### REFACTOR: Clean Up (Only If Valuable)

- Assess after every GREEN
- All tests must still pass after refactoring

### Repeat

Continue the cycle until the full behavior described in `$ARGUMENTS` is implemented.

## Project Test Conventions

Before writing the first test, detect the project's language, test framework, and conventions:
- Look for config files (`*.csproj`, `package.json`, `pyproject.toml`, `go.mod`, `Makefile`, etc.)
- Check for existing test files to match naming conventions, folder structure, and assertion style
- Follow any project-specific testing docs (e.g., `CONTRIBUTING.md`, `docs/testing.md`, `README.md`)

### What to Test

| Layer | Test Focus | Example |
|-------|------------|---------|
| **Value objects / models** | Validation, equality, immutability | Creating an Email with `""` returns a validation error |
| **Domain / business logic** | State transitions, rules, event emission | Approving an order changes status and emits event |
| **Application services** | Orchestration, use cases, command handling | CreateUser service hashes password and persists user |
| **API / controllers** | HTTP status codes, response shape, routing | `POST /orders` returns `201` with `Location` header |
| **UI components** | User interactions, conditional rendering | Click "Delete" shows confirmation dialog |
| **Utility / helper functions** | Pure input/output, edge cases | `calculateDiscount(0)` returns `0` |

### What NOT to Test

- Type definitions, interfaces, DTOs, imports
- Framework wiring (router setup, DI container config)
- Code that simply delegates to another tested function
- Implementation details (internal state, private methods)

## Steps

### 1. Understand the requirement

Parse `$ARGUMENTS`. If the requirement is unclear, ask the user before writing any code.

Identify:
- What **behavior** needs to exist (not what code to write)
- Which **layer(s)** are involved (domain, application, infrastructure, frontend)
- What **existing code** to read first (related aggregates, handlers, components)

### 2. Plan the test sequence

Break the requirement into small, incremental behavior steps. Each step = one RED-GREEN cycle.

Order from inside out:
1. **Domain / core logic** first (value objects, models, domain services, business rules)
2. **Application layer** next (use cases, services, orchestration)
3. **Infrastructure / API** last (controllers, handlers, repositories, external integrations)
4. **Frontend / UI** after backend is solid (if applicable)

Present the sequence to the user as a brief numbered list before starting.

### 3. Execute RED-GREEN-REFACTOR cycles

For each behavior step:

1. **RED**: Write the failing test
2. Run the test — show the failure
3. **GREEN**: Write minimum production code
4. Run the test — show it passes
5. **REFACTOR**: Assess and improve
6. Run the test again after refactoring

Show progress: `[3/8] GREEN | NewEmail rejects empty string`

### 4. Verify the full build

After all cycles are complete:

Run the project's build and full test suite using the detected toolchain:
- **C#**: `dotnet build` and `dotnet test`
- **TypeScript/JavaScript**: `npm run build` and `npm test` (or equivalent)
- **Python**: `pytest` (or `python -m unittest`)
- **Go**: `go build ./...` and `go test ./...`
- **Other**: Use the project's standard build/test commands
- Fix any issues before reporting success

### 5. Report

```
## TDD Implementation Complete

**Implemented**: <what was built>
**Cycles**: <N> RED-GREEN-REFACTOR cycles

### Tests Written
| # | Test | Layer | Status |
|---|------|-------|--------|
| 1 | description | domain/app/infra/frontend | PASS |

### Production Code Created/Modified
- <file>: <what was added/changed>

### Checklist
- [ ] Every production code line has a failing test that demanded it
- [ ] All tests pass
- [ ] Build passes (backend and/or frontend)
- [ ] No speculative code ("just in case" logic without tests)
```

## Anti-Patterns to Avoid

- Writing production code without a failing test first
- Testing implementation details (internal state, call counts on internal methods)
- Over-mocking — prefer real objects, use fakes/mocks only at system boundaries
- Tests that only assert "no error" without checking the actual result
- Identity test values: `0` for `+/-`, `1` for `*`, empty string, `true/true`
- Speculative code not demanded by any test
- Rewriting an entire file wholesale — use incremental edits

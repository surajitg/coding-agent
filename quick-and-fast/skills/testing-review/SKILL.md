---
name: testing-review
description: Deep Roslyn AST analysis of .NET Clean Architecture compliance
---


# Testing Review (Deep)

## Unit Tests
- Business logic fully isolated
- Mock external dependencies correctly

## Integration Tests
- DB + API integration covered
- Use test containers or in-memory DB carefully

## Edge Cases
- Null, boundary, failure scenarios covered
- Race conditions tested

## Test Quality
- No flaky tests
- Deterministic outcomes
- Fast execution

## Coverage
- Focus on critical paths, not % vanity
```

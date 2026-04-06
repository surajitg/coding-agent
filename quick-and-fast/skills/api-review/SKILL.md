---
name: api-review
description: Deep Roslyn AST analysis of .NET Clean Architecture compliance
---

# api-review.md
```md
# API Review (Deep)

## Design
- Resource-oriented design
- Avoid RPC-style endpoints

## Contracts
- DTO versioning strategy
- Backward compatibility ensured

## Behavior
- Idempotent endpoints where needed
- Proper error models (ProblemDetails)

## Security
- Rate limiting
- Input size limits
```

---
name: layered-architecture-pr-review
description: Review pull requests for layered architecture violations
---

# Layered Architecture PR Review

## Purpose

Review pull request changes and identify layered architecture violations.

---

# Review Areas

Controllers

Controllers should only orchestrate requests.

Controllers must not contain business logic.

---

Application Layer

Business logic should exist in application services.

---

Domain Layer

Domain must not reference frameworks.

---

Infrastructure Layer

Infrastructure should only implement persistence or external integrations.

---

# Output

Provide PR comment including:

Violation summary  
Affected files  
Recommended refactoring

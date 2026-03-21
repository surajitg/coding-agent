---
name: layered-architecture-fix-suggestions
description: Provide refactoring suggestions for layered architecture violations
---

# Layered Architecture Fix Suggestions

## Move Business Logic to Application Services

Incorrect:

Controller contains business logic.

Correct:

Controller → Application Service → Repository

---

## Remove Infrastructure Dependencies from Domain

Domain should only contain business logic.

Remove references to:

Entity Framework  
HTTP frameworks  

---

## Enforce Dependency Flow

Ensure dependency direction:

Presentation → Application → Domain

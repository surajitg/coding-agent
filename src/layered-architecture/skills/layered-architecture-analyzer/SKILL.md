---
name: layered-architecture-analyzer
description: Analyze repository for layered architecture compliance
---

# Layered Architecture Analyzer

## Purpose

Evaluate whether the system follows layered architecture principles.

Typical layers:

Presentation  
Application  
Domain  
Infrastructure  

---

# Allowed Dependencies

Presentation → Application  
Application → Domain  
Infrastructure → Application or Domain  

---

# Forbidden Dependencies

Domain → Infrastructure  
Domain → Presentation  
Application → Presentation  

---

# Violations to Detect

Controllers accessing database directly.

Example:

Controller → DbContext

Business logic inside controllers.

Domain referencing frameworks:

Entity Framework  
ASP.NET  

Circular dependencies between layers.

Infrastructure leaking into application layer.

---

# Output

Provide:

Violation summary  
File path  
Class name  
Description of violation  
Recommended improvement

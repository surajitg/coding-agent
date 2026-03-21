---
name: solid-pr-review-commenter
description: Add SOLID design review comments to pull requests
version: 1.0
---

# SOLID PR Review

Posts comments on pull requests hosted on :contentReference[oaicite:2]{index=2}.

---

# Example PR Comment

SOLID Review Summary

Score: 72/100

Issues detected:

- UserManager violates SRP
- PaymentService violates OCP
- OrderService tightly coupled to SQL repository

Recommendations:

- Split responsibilities
- Introduce strategy pattern
- Introduce repository abstraction

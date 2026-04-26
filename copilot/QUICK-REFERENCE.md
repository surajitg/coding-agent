# Enterprise Code Analysis Agent - Quick Reference

## 🚀 Quick Start Commands

```bash
# Detect architecture
/detect-architecture <solution_path>

# Full analysis (all dimensions)
/full-review <solution_path>

# Architecture only
/review-architecture <solution_path>

# Design/SOLID only
/review-design <solution_path>

# Code quality only
/review-code <solution_path>

# Get compliance score
/score <solution_path>

# Get recommendations
/recommendations <solution_path>

# Get specific fix
/fix <violation_id>
```

---

## 🏗️ Architecture Types & Skills

| Architecture | Analyzer | Scorer | PR Review | Fixer |
|--------------|----------|--------|-----------|-------|
| **Clean** | `clean-architecture-analyzer` | `clean-architecture-score` | `clean-architecture-pr-review` | `clean-architecture-fix-suggestion` |
| **Layered** | `layered-architecture-analyzer` | `layered-architecture-score` | `layered-architecture-pr-review` | ✓ |
| **Component** | `component-architecture-analyzer` | `component-architecture-score` | `component-architecture-pr-review` | `component-architecture-fix-suggestions` |
| **Microservices** | `microservices-architecture-analyzer` | `microservices-architecture-score` | `microservices-architecture-pr-review` | `microservices-architecture-fix-suggestions` |
| **Monolith** | `monolith-architecture-analyzer` | `monolith-architecture-score` | `monolith-architecture-pr-review` | `monolith-architecture-fix-suggestions` |
| **Modular Monolith** | `modular-monolith-architecture-analyzer` | `modular-monolith-architecture-score` | `modular-monolith-architecture-pr-review` | `modular-monolith-architecture-fix-suggestions` |
| **Custom Layered** | `custom-layered-analyzer` | `custom-layered-score` | `custom-layered-pr-review` | `custom-layered-fix-suggestions` |

---

## 🎯 Code Quality Analyzers

| Area | Skill | Common Issues |
|------|-------|---------------|
| **Performance** | `performance-analyzer` | N+1 queries, inefficient LINQ, allocations |
| **Security** | `security-analyzer` | SQL injection, hardcoded secrets, validation |
| **Memory** | `memory-analyzer` | LOH allocations, boxing, excessive allocs |
| **Concurrency** | `concurrency-analyzer` | Blocking async, deadlocks, race conditions |
| **Reliability** | `reliability-analyzer` | Missing retries, timeouts, exception gaps |
| **Observability** | `observability-analyzer` | Logging gaps, tracing, metrics coverage |

---

## 📊 SOLID Principles Quick Guide

| Principle | What It Is | Common Violation | Skill |
|-----------|-----------|------------------|-------|
| **SRP** | One reason to change | Class with multiple responsibilities | `solid-analyzer` |
| **OCP** | Open for extension, closed for modification | Switch statements on types | `solid-analyzer` |
| **LSP** | Derived classes substitutable for base | Contracts violated in overrides | `solid-analyzer` |
| **ISP** | Don't depend on interfaces you don't use | Fat interfaces | `solid-analyzer` |
| **DIP** | Depend on abstractions, not implementations | Direct concrete dependencies | `solid-analyzer` |

**Skills:**
- `solid-analyzer` — Find violations
- `solid-score` — Calculate score
- `solid-pr-review` — PR feedback
- `solid-fix-suggestion` — Fixes

---

## 📈 Score Interpretation

```
95-100 ✅ EXCELLENT  → Production ready
85-94  ✓ GOOD        → Minor improvements
75-84  ⚠️ FAIR        → Address before deploy
50-74  ❌ POOR        → Significant refactoring
< 50   🚨 CRITICAL   → Blocking issues
```

---

## ✅ Pre-Deployment Checklist

```
Architecture:     ≥ 80% ☐
SOLID:            ≥ 75% ☐
Security:         ≥ 95% ☐
Performance:      ≥ 70% ☐
Memory:           No critical ☐
Concurrency:      ≥ 80% ☐
Reliability:      ≥ 80% ☐
Overall Score:    ≥ 75% ☐
No high-priority violations ☐
Team review complete ☐
```

---

## 🔍 Analysis Pipeline

```
Phase 1: ARCHITECTURE
├─ Detect style
├─ Run analyzer
├─ Score
└─ PR review

Phase 2: DESIGN
├─ Analyze SOLID
├─ Score
├─ PR review
└─ Get fixes

Phase 3: CODE QUALITY
├─ Performance check
├─ Security check
├─ Memory check
├─ Concurrency check
├─ Reliability check
└─ Generate report
```

---

## 🛠️ Common Issues & Fixes

### N+1 Queries
```csharp
// ❌ BAD
foreach(var user in users) {
    var orders = repo.GetOrders(user.Id);
}

// ✅ GOOD
var orders = repo.GetOrdersForUsers(users.Select(u => u.Id));
```

### Blocking Async
```csharp
// ❌ BAD
var result = asyncMethod().Result;

// ✅ GOOD
var result = await asyncMethod();
```

### SQL Injection
```csharp
// ❌ BAD
var query = "SELECT * FROM Users WHERE id=" + userId;

// ✅ GOOD
var query = "SELECT * FROM Users WHERE id=@id";
```

### Hardcoded Secrets
```csharp
// ❌ BAD
var apiKey = "sk-1234567890abcdef";

// ✅ GOOD
var apiKey = Environment.GetEnvironmentVariable("API_KEY");
```

### SRP Violation
```csharp
// ❌ BAD - Multiple responsibilities
class UserService {
    public void CreateUser() { }
    public void SaveToDatabase() { }
    public void SendEmail() { }
}

// ✅ GOOD - Single responsibility
class UserService { public void CreateUser() { } }
class UserRepository { public void SaveToDatabase() { } }
class UserNotifier { public void SendEmail() { } }
```

---

## 📋 Skill Reference Matrix

### Detection Skills (Analyzers)
```
clean-architecture-analyzer ............. Find arch violations
solid-analyzer ........................... Detect SOLID issues
performance-analyzer ..................... Find perf issues
security-analyzer ........................ Detect security gaps
memory-analyzer .......................... Find memory issues
concurrency-analyzer ..................... Detect threading issues
reliability-analyzer ..................... Find reliability gaps
observability-analyzer ................... Check logging/metrics
enterprise-architecture-style-detector .. Identify architecture
```

### Quantification Skills (Scorers)
```
clean-architecture-score ................ Score Clean Arch
solid-score ............................. Score SOLID
component-architecture-score ............ Score Components
microservices-architecture-score ........ Score Microservices
code-compliance-score ................... Overall score
enterprise-architecture-scoring-engine .. Enterprise metrics
```

### Feedback Skills (PR Review)
```
clean-architecture-pr-review ............ PR comments
solid-pr-review ......................... SOLID feedback
component-architecture-pr-review ........ Component feedback
pr-review ............................... General review
```

### Fix Skills (Remediation)
```
clean-architecture-fix-suggestion ....... Fix recommendations
solid-fix-suggestion .................... SOLID fixes
code-autofix-suggestion ................. Auto-fixable issues
component-architecture-fix-suggestions .. Component fixes
microservices-architecture-fix-suggestions Service fixes
```

---

## 🎓 Architecture Validation Rules

### Clean Architecture
```
Domain ...................... no dependencies
Application ................. Domain only
Infrastructure .............. Domain + Application
Presentation ................. Application only
Domain Purity ............... No EF, ASP.NET, Dapper, HttpClient
Repository Pattern .......... Required
```

### Microservices
```
Service Autonomy ............ Independent deployment
Service Boundaries .......... Clear, enforced
API Contracts ............... Well-defined
Data Ownership .............. Per-service
Communication ............... Async when possible
```

### Modular Monolith
```
Module Autonomy ............. Clear boundaries
Module Communication ........ Via APIs only
Circular Dependencies ....... None
Shared Kernel ............... Minimal
```

---

## 🔗 Integration Quick Links

### GitHub Copilot
- Add to: `.github/copilot-instructions.md`
- Chat: `@copilot /full-review <path>`
- PR: Enable code review in settings

### Claude (Web/API)
- Add as custom instruction
- Start with: "I need a code analysis"
- Reference skill by name

### VS Code
- File: `.copilot-instructions.md`
- Settings: Enable Copilot chat

---

## 📊 Sample Report Output

```
QUICK COMPLIANCE DASHBOARD
════════════════════════════
Architecture:    92% ✓
SOLID Principles: 88% ✓
Performance:     78% ⚠
Security:        98% ✓
Memory:          85% ✓
Concurrency:     90% ✓
Reliability:     92% ✓
────────────────────────────
OVERALL SCORE:   89% ✓

🎯 TOP PRIORITIES:
1. Fix N+1 queries (Performance)
2. Split UserService (SRP)
3. Add timeouts (Reliability)
```

---

## 🚦 Status Guide

| Status | Action |
|--------|--------|
| ✅ 95-100 | Deploy immediately |
| ✓ 85-94 | Deploy with monitoring |
| ⚠️ 75-84 | Plan improvements |
| ❌ 50-74 | Schedule refactoring |
| 🚨 < 50 | Block deployment |

---

## 💡 Pro Tips

1. **Start with detection** → `/detect-architecture` before analysis
2. **Focus on impact** → Fix high-impact items first
3. **Use scoring** → Track improvement over time
4. **Batch analysis** → Analyze once per sprint
5. **Automate** → Add to CI/CD pipeline
6. **Share results** → Present to team with context
7. **Iterate** → Analyze, fix, repeat
8. **Celebrate wins** → Track score improvements

---

## 📞 When to Use Each Skill

| Need | Use This Skill |
|------|---|
| Know my architecture? | `enterprise-architecture-style-detector` |
| Validate Clean Arch? | `clean-architecture-analyzer` |
| SOLID check? | `solid-analyzer` |
| Fast performance audit? | `performance-analyzer` |
| Security holes? | `security-analyzer` |
| PR review? | `pr-review` |
| Get my score? | `*-score` skill for dimension |
| Fix recommendations? | `*-fix-suggestion` skill |
| Full analysis? | `/full-review` command |

---

## 🔐 Security Checks Overview

```
✓ SQL Injection ................. Parameterized queries
✓ Hardcoded Secrets ............ Environment variables
✓ Insecure Deserialization .... Safe serializers
✓ Input Validation ............. Validation attributes
✓ XSS Protection ............... Output encoding
✓ CSRF Protection .............. Token validation
```

---

**Quick Reference v1.0 | Updated: 2026-04-26**

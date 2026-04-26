# Enterprise Code Analysis Agent

**Version:** 1.0  
**Platform:** Claude | GitHub Copilot  
**Language:** C# / .NET  
**Purpose:** Enterprise-grade code analysis for architecture, design principles, code quality, and best practices

---

## 📋 Table of Contents

- [Overview](#overview)
- [Core Capabilities](#core-capabilities)
- [Architecture Analysis](#architecture-analysis)
- [Design Principles](#design-principles)
- [Code Quality Analysis](#code-quality-analysis)
- [Non-Functional Requirements](#non-functional-requirements)
- [Usage Commands](#usage-commands)
- [Skill Reference](#skill-reference)
- [Integration Guide](#integration-guide)

---

## Overview

The Enterprise Code Analysis Agent provides comprehensive, multi-layered analysis of .NET codebases. It evaluates code against enterprise standards including Clean Architecture, SOLID principles, security best practices, performance optimization, and reliability patterns.

The agent uses Roslyn-based AST analysis and pattern matching to detect violations, score compliance, and generate actionable recommendations with code suggestions.

### Key Characteristics

- **AST-Based Analysis:** Deep syntax tree analysis using Roslyn compiler platform
- **Multi-Architecture Support:** Analyzes layered, component-based, microservices, monolith, and modular monolith architectures
- **Automated Scoring:** Generates compliance scores for each analysis dimension
- **PR Review Integration:** Provides contextual code review comments with fix suggestions
- **Enterprise Standards:** Enforces industry best practices and non-functional requirements

---

## Core Capabilities

### 1. **Architectural Style Detection**
Automatically detects the architectural style implemented in your codebase.

**Supported Styles:**
- ✅ Layered Architecture
- ✅ Component-Based Architecture
- ✅ Microservices Architecture
- ✅ Monolithic Architecture
- ✅ Modular Monolith Architecture
- ✅ Hybrid Architectures

### 2. **Architecture Compliance Analysis**
Analyzes each architecture style for violations and best practices.

**For Each Style:**
- Layer/service boundary validation
- Dependency direction enforcement
- Module encapsulation verification
- Service autonomy checks
- API contract analysis

### 3. **Design Principles Enforcement**
Validates SOLID principles and design patterns.

**Principles Covered:**
- Single Responsibility Principle (SRP)
- Open/Closed Principle (OCP)
- Liskov Substitution Principle (LSP)
- Interface Segregation Principle (ISP)
- Dependency Inversion Principle (DIP)

### 4. **Code Quality Assessment**
Multi-dimensional code quality evaluation.

**Dimensions:**
- Performance optimization
- Security vulnerability detection
- Memory management and GC pressure
- Concurrency issues
- Reliability and resilience patterns

---

## Architecture Analysis

### Available Architectures

#### **1. Clean Architecture** 
Enforces strict layer separation: Presentation → Application → Domain ← Infrastructure

**Analyzer Skills:**
- `clean-architecture-analyzer` — Detects layer violations
- `clean-architecture-score` — Generates compliance score
- `clean-architecture-pr-review` — Provides PR feedback
- `clean-architecture-fix-suggestion` — Suggests corrections

**Key Checks:**
```
✓ Dependency direction (Domain → none, App → Domain, Infra → App+Domain)
✓ Domain layer purity (no EF, ASP.NET, Dapper, HttpClient)
✓ Repository pattern implementation
✓ Dependency injection configuration
✓ Cross-cutting concerns handling
```

#### **2. Layered Architecture**
Traditional n-tier architecture with horizontal layer separation.

**Analyzer Skills:**
- `layered-architecture-analyzer` — Analyzes layer structure
- `layered-architecture-score` — Scores compliance
- `layered-architecture-pr-review` — PR review comments

**Key Checks:**
```
✓ Layer boundaries and responsibilities
✓ Cross-layer dependencies
✓ Data flow direction
✓ Layer isolation
```

#### **3. Component-Based Architecture**
Feature-centric modular organization with encapsulated components.

**Analyzer Skills:**
- `component-architecture-analyzer` — Evaluates component structure
- `component-architecture-score` — Component compliance score
- `component-architecture-pr-review` — Component boundary feedback
- `component-architecture-fix-suggestions` — Component refactoring suggestions

**Key Checks:**
```
✓ Component boundaries and cohesion
✓ Component communication contracts
✓ Circular dependency detection
✓ Component-level separation of concerns
```

#### **4. Microservices Architecture**
Distributed service-based architecture with independent deployment.

**Analyzer Skills:**
- `microservices-architecture-analyzer` — Service boundary analysis
- `microservices-architecture-score` — Microservices compliance score
- `microservices-architecture-pr-review` — Service design feedback
- `microservices-architecture-fix-suggestions` — Service restructuring suggestions

**Key Checks:**
```
✓ Service autonomy and independence
✓ API contract design
✓ Data consistency patterns
✓ Inter-service communication
✓ Deployment independence
```

#### **5. Monolithic Architecture**
Single deployment unit with modular or layered internal structure.

**Analyzer Skills:**
- `monolith-architecture-analyzer` — Monolith structure analysis
- `monolith-architecture-score` — Monolith compliance score
- `monolith-architecture-pr-review` — Monolith design feedback
- `monolith-architecture-fix-suggestions` — Monolith refactoring suggestions

**Key Checks:**
```
✓ Module organization and cohesion
✓ Internal layer separation
✓ Shared state management
✓ Deployment considerations
```

#### **6. Modular Monolith Architecture**
Single deployment with feature modules, emphasizing modularity.

**Analyzer Skills:**
- `modular-monolith-architecture-analyzer` — Module structure analysis
- `modular-monolith-architecture-score` — Modular monolith compliance
- `modular-monolith-architecture-pr-review` — Module feedback
- `modular-monolith-architecture-fix-suggestions` — Module organization suggestions

**Key Checks:**
```
✓ Module autonomy and boundaries
✓ Module communication via contracts
✓ Circular dependency prevention
✓ Shared kernel management
```

#### **7. Custom Layered Architecture**
Supports user-defined layer structures and dependencies.

**Analyzer Skills:**
- `custom-layered-analyzer` — Analyzes custom layer structure
- `custom-layered-score` — Custom layer compliance score
- `custom-layered-pr-review` — Custom layer feedback
- `custom-layered-fix-suggestions` — Custom layer refactoring suggestions

**Configuration:**
```yaml
layers:
  - name: "Layer Name"
    folders: ["pattern1/**", "pattern2/**"]
    allowedDependencies: ["Layer1", "Layer2"]
```

---

## Design Principles

### **SOLID Design Principles**

#### **1. Single Responsibility Principle (SRP)**
A class should have only one reason to change.

**Skill:** `solid-analyzer`
- Detects classes with excessive methods
- Identifies mixed concerns (business + persistence + API)
- Flags god objects

**Example Violations:**
```csharp
// ❌ Violates SRP
class UserService {
    public void CreateUser() { }
    public void SaveToDatabase() { }
    public void SendEmail() { }
    public void GenerateReport() { }
}

// ✅ Follows SRP
class UserService { public void CreateUser() { } }
class UserRepository { public void SaveToDatabase() { } }
class UserNotifier { public void SendEmail() { } }
```

#### **2. Open/Closed Principle (OCP)**
Classes should be open for extension but closed for modification.

**Skill:** `solid-analyzer`
- Detects switch statements on types
- Identifies type-checking patterns
- Suggests strategy/factory patterns

**Example Violations:**
```csharp
// ❌ Violates OCP
switch (userType) {
    case "Admin": return new AdminService();
    case "User": return new UserService();
}

// ✅ Follows OCP
dictionary[userType].CreateService()
```

#### **3. Liskov Substitution Principle (LSP)**
Derived classes must be substitutable for base classes.

**Skill:** `solid-analyzer`
- Detects violations in inheritance chains
- Checks contract compliance in overrides

#### **4. Interface Segregation Principle (ISP)**
Clients should not depend on interfaces they don't use.

**Skill:** `solid-analyzer`
- Detects fat interfaces
- Suggests interface splitting

#### **5. Dependency Inversion Principle (DIP)**
Depend on abstractions, not concrete implementations.

**Skill:** `solid-analyzer`
- Detects direct concrete dependencies
- Suggests abstraction layers

**Skills:**
- `solid-analyzer` — Detects SOLID violations using AST
- `solid-score` — Calculates SOLID compliance score
- `solid-pr-review` — Provides SOLID-focused PR comments
- `solid-fix-suggestion` — Recommends SOLID fixes

---

## Code Quality Analysis

### **1. Performance Analysis**

**Skill:** `performance-analyzer`

**Checks:**
```
✓ N+1 database query detection
✓ Inefficient LINQ patterns (ToList().Where() vs Where().ToList())
✓ Unnecessary allocations
✓ Suboptimal collection usage
✓ String concatenation in loops
✓ ASYNC/AWAIT anti-patterns
```

**Example Violations:**
```csharp
// ❌ N+1 Query
foreach(var user in users) {
    var orders = repo.GetOrders(user.Id);  // Repeated queries!
}

// ✓ Batched Query
var orders = repo.GetOrdersForUsers(users.Select(u => u.Id));
```

### **2. Security Analysis**

**Skill:** `security-analyzer`

**Checks:**
```
✓ SQL Injection vulnerabilities
✓ Hardcoded secrets (password, apiKey, connectionString)
✓ Insecure deserialization
✓ Missing input validation
✓ XSS vulnerabilities
✓ CSRF protection gaps
```

**Example Violations:**
```csharp
// ❌ SQL Injection
var query = "SELECT * FROM Users WHERE name=" + username;

// ✓ Parameterized Query
var query = "SELECT * FROM Users WHERE name=@name";
```

### **3. Memory & GC Analysis**

**Skill:** `memory-analyzer`

**Checks:**
```
✓ Large Object Heap (LOH) allocations (> 85 KB)
✓ Excessive allocations in loops
✓ Boxing/unboxing inefficiencies
✓ String pooling opportunities
✓ Unreferenced object detection
```

### **4. Concurrency Analysis**

**Skill:** `concurrency-analyzer`

**Checks:**
```
✓ Blocking calls in async methods (task.Result, task.Wait())
✓ Missing ConfigureAwait(false)
✓ Deadlock risks from nested locks
✓ Race conditions
✓ Thread safety violations
```

**Example Violations:**
```csharp
// ❌ Blocks async thread
var result = asyncMethod().Result;

// ✓ Properly awaits
var result = await asyncMethod();

// ❌ Missing ConfigureAwait
await SomeLibraryMethodAsync();

// ✓ Library code should use ConfigureAwait(false)
await SomeLibraryMethodAsync().ConfigureAwait(false);
```

### **5. Reliability Analysis**

**Skill:** `reliability-analyzer`

**Checks:**
```
✓ Missing retry logic on external calls
✓ Missing timeout configuration
✓ Empty catch blocks
✓ Resource leaks (unclosed streams/connections)
✓ Missing exception handling
✓ Improper disposal patterns
```

**Example Violations:**
```csharp
// ❌ No retry or timeout
var response = httpClient.GetAsync(url).Result;

// ✓ With timeout and retry
using var cts = new CancellationTokenSource(TimeSpan.FromSeconds(30));
var response = await RetryPolicy.ExecuteAsync(
    async () => await httpClient.GetAsync(url, cts.Token)
);
```

### **6. Observability Analysis**

**Skill:** `observability-analyzer`

**Checks:**
```
✓ Logging coverage in critical paths
✓ Trace correlation ID usage
✓ Metrics instrumentation
✓ Health check implementations
✓ Structured logging patterns
```

---

## Non-Functional Requirements

### **Code Compliance Scoring**

**Skill:** `code-compliance-score`

Generates multi-dimensional compliance score:
```
📊 Compliance Report
├── Architecture Compliance: 92%
├── Design Principles: 88%
├── Security Score: 95%
├── Performance Score: 78%
├── Memory Management: 85%
├── Concurrency Safety: 90%
└── Overall Score: 88%
```

### **Enterprise Architecture Scoring**

**Skill:** `enterprise-architecture-scoring-engine`

Comprehensive enterprise architecture assessment:
```
✓ Scalability Score
✓ Maintainability Score
✓ Testability Score
✓ Deployment Complexity
✓ Team Autonomy Index
```

---

## Usage Commands

### **Quick Start**

#### 1. **Detect Architecture Style**
```
/detect-architecture <solution_path>
```
Automatically identifies the architecture pattern used in your codebase.

**Uses Skill:** `enterprise-architecture-style-detector`

#### 2. **Run Full Code Review**
```
/full-review <solution_path>
```
Executes complete analysis pipeline across all dimensions.

**Pipeline:**
```
Stage 1: Architecture Analysis (Analyzer → Score → PR Review)
Stage 2: Design Principles (Analyzer → Score → PR Review)
Stage 3: Code Quality (All analyzers → Scores → Feedback)
```

#### 3. **Architecture-Only Review**
```
/review-architecture <solution_path>
```
Focuses exclusively on architectural compliance.

#### 4. **Design-Only Review**
```
/review-design <solution_path>
```
Analyzes SOLID principles and design patterns only.

#### 5. **Code Quality Review**
```
/review-code <solution_path>
```
Runs performance, security, memory, concurrency, and reliability checks.

#### 6. **Generate Architecture Diagram**
```
/architecture-diagram <solution_path>
```
Creates visual representation of detected architecture.

#### 7. **Get Recommendations**
```
/recommendations <solution_path>
```
Lists priority-ordered improvement recommendations with estimated effort.

---

## Skill Reference

### **Analyzer Skills** (Detection Phase)

| Skill | Purpose | Input | Output |
|-------|---------|-------|--------|
| `clean-architecture-analyzer` | Detects Clean Architecture violations | solution_path | Violations list, violation locations |
| `layered-architecture-analyzer` | Validates layered architecture | solution_path | Layer violations, boundary issues |
| `component-architecture-analyzer` | Evaluates component structure | solution_path | Component boundary issues |
| `microservices-architecture-analyzer` | Analyzes service boundaries | solution_path | Service autonomy issues |
| `monolith-architecture-analyzer` | Examines monolith structure | solution_path | Module organization issues |
| `modular-monolith-architecture-analyzer` | Validates module architecture | solution_path | Module boundary violations |
| `custom-layered-analyzer` | Custom layer validation | solution_path, config | Custom layer violations |
| `solid-analyzer` | SOLID principle violations | solution_path | SRP, OCP, LSP, ISP, DIP violations |
| `performance-analyzer` | Performance anti-patterns | solution_path | N+1 queries, inefficient LINQ, allocations |
| `security-analyzer` | Security vulnerabilities | solution_path | SQL injection, hardcoded secrets, validation gaps |
| `memory-analyzer` | Memory pressure patterns | solution_path | LOH allocations, excessive allocations, boxing |
| `concurrency-analyzer` | Concurrency issues | solution_path | Blocking calls, deadlock risks, race conditions |
| `reliability-analyzer` | Reliability problems | solution_path | Missing retries, timeouts, exception handling |
| `observability-analyzer` | Observability gaps | solution_path | Logging, tracing, metrics coverage issues |

### **Scoring Skills** (Quantification Phase)

| Skill | Purpose | Input | Output |
|-------|---------|-------|--------|
| `clean-architecture-score` | Clean Architecture compliance % | violations, architecture | Score 0-100, breakdown by category |
| `layered-architecture-score` | Layered architecture compliance % | violations | Score 0-100, layer compliance |
| `component-architecture-score` | Component architecture compliance % | violations | Score 0-100, component metrics |
| `microservices-architecture-score` | Microservices compliance % | violations | Score 0-100, service autonomy |
| `monolith-architecture-score` | Monolith compliance % | violations | Score 0-100, monolith metrics |
| `modular-monolith-architecture-score` | Modular monolith compliance % | violations | Score 0-100, module metrics |
| `custom-layered-score` | Custom layer compliance % | violations, config | Score 0-100, custom metrics |
| `solid-score` | SOLID compliance % | violations | Score 0-100, per-principle breakdown |
| `code-compliance-score` | Overall code compliance % | all violations | Comprehensive compliance dashboard |
| `enterprise-architecture-scoring-engine` | Enterprise scoring | architecture, metrics | Multi-dimensional enterprise score |

### **PR Review Skills** (Feedback Phase)

| Skill | Purpose | Input | Output |
|-------|---------|-------|--------|
| `clean-architecture-pr-review` | Clean Architecture review comments | violations, PR context | Contextual PR feedback with line numbers |
| `layered-architecture-pr-review` | Layer architecture review | violations, PR context | Layer-specific feedback |
| `component-architecture-pr-review` | Component boundary review | violations, PR context | Component-specific feedback |
| `microservices-architecture-pr-review` | Microservices design review | violations, PR context | Service design feedback |
| `monolith-architecture-pr-review` | Monolith structure review | violations, PR context | Monolith feedback |
| `modular-monolith-architecture-pr-review` | Module architecture review | violations, PR context | Module-specific feedback |
| `custom-layered-pr-review` | Custom layer review | violations, config, PR context | Custom layer feedback |
| `solid-pr-review` | SOLID principles review | violations, PR context | Principle-specific feedback |
| `pr-review` | General code review | all findings, PR context | Comprehensive PR feedback |

### **Fix Suggestion Skills** (Remediation Phase)

| Skill | Purpose | Input | Output |
|-------|---------|-------|--------|
| `clean-architecture-fix-suggestion` | Clean Architecture fixes | violations, code | Code samples for fixes |
| `component-architecture-fix-suggestions` | Component refactoring | violations, code | Component restructuring suggestions |
| `microservices-architecture-fix-suggestions` | Service refactoring | violations, code | Service separation suggestions |
| `monolith-architecture-fix-suggestions` | Monolith restructuring | violations, code | Monolith improvement suggestions |
| `modular-monolith-architecture-fix-suggestions` | Module restructuring | violations, code | Module refactoring suggestions |
| `custom-layered-fix-suggestions` | Custom layer fixes | violations, config, code | Custom layer improvements |
| `solid-fix-suggestion` | SOLID principle fixes | violations, code | Principle-specific refactoring |
| `code-autofix-suggestion` | Automatic code fixes | violations, code | Executable code transformations |

### **Utility Skills**

| Skill | Purpose | Input | Output |
|-------|---------|-------|--------|
| `enterprise-architecture-style-detector` | Architecture style detection | solution structure | Detected architecture, confidence |
| `enterprise-architecture-scoring-engine` | Comprehensive enterprise scoring | full analysis | Multi-dimensional metrics |

---

## Integration Guide

### **For GitHub Copilot Chat**

1. **Add to Copilot Instructions**

Place in `.github/copilot-instructions.md` or user settings:

```markdown
# Code Analysis Agent

You are an Enterprise Code Analysis Agent with expertise in:
- Clean Architecture validation
- SOLID principles enforcement
- Code quality assessment
- Security vulnerability detection
- Performance optimization analysis

When a user requests code analysis, follow this pipeline:

1. Ask for solution path or accept code snippet
2. Detect architecture style using enterprise-architecture-style-detector
3. Run full analysis: architecture → design → code quality
4. Generate compliance score
5. Provide prioritized recommendations with code examples
```

2. **Use in PR Reviews**

```markdown
@copilot /review-pr

Analyze this PR for:
- Architecture violations
- SOLID principle breaches
- Security issues
- Performance problems
```

### **For Claude**

1. **Add as Custom Instructions**

```markdown
# Code Analysis Agent Role

You are an expert Enterprise Code Analysis Agent specializing in:
- .NET/C# architecture analysis
- SOLID principles enforcement
- Code quality evaluation
- Security assessment
- Performance optimization

You have access to the following analysis tools/skills:
[Insert complete skills list]

For any code analysis request:
1. Clarify the architecture style or detect it
2. Run appropriate analyzers based on user request
3. Generate compliance scores
4. Provide actionable recommendations
```

2. **Example Requests**

```
Analyze my .NET solution for:
1. Architecture compliance
2. SOLID principle violations
3. Security issues
4. Performance bottlenecks

Solution path: /src/MyApplication
```

### **Setup in VS Code**

Create `.vscode/settings.json`:

```json
{
  "github.copilot.codeReview.enabled": true,
  "github.copilot.codeReview.reviewOnPush": true,
  "copilot.instructions": [
    "Use code-analysis-agent.md for code reviews"
  ]
}
```

Create `.copilot-instructions.md`:

```markdown
---
name: Enterprise Code Analyzer
description: Multi-dimensional code analysis agent
---

[Include content from CODE-ANALYSIS-AGENT.md]
```

---

## Output Format

### **Compliance Report**

```
📊 CODE ANALYSIS REPORT
═══════════════════════════════════════

🏗️ ARCHITECTURE ANALYSIS
─────────────────────────
Detected Style: Clean Architecture
Compliance Score: 92%

Violations Found: 3
├─ Layer violation in UserService.cs:42
├─ Domain dependency on EF in User.cs:15
└─ Circular dependency: Domain → Infrastructure

✓ Recommendations:
  1. Extract EF context to Infrastructure layer
  2. Implement Repository pattern for data access
  3. Remove circular dependency using DIP

─────────────────────────

🎯 SOLID PRINCIPLES ANALYSIS
────────────────────────────
Compliance Score: 88%

SRP Violations: 2
├─ UserService violates SRP (Create, Save, Email)
└─ OrderProcessor has mixed concerns

OCP Violations: 1
├─ Switch statement on order types

ISP Violations: 0
DIP Violations: 1
├─ Direct dependency on concrete DbContext

✓ Fixes:
  1. Split UserService into (CreateUser, SaveUser, EmailUser)
  2. Replace switch with strategy pattern
  3. Inject IUserRepository interface

─────────────────────────

🔒 CODE QUALITY ANALYSIS
──────────────────────────
Performance Score: 78%
Security Score: 95%
Memory Score: 85%
Concurrency Score: 90%
Reliability Score: 92%

Performance Issues: 2
├─ N+1 query in GetUserOrders() [HIGH]
└─ String concatenation in loop [MEDIUM]

Security Issues: 0

✓ Overall: GOOD
  Next Steps: Optimize queries, then refactor string handling
```

### **Score Interpretation**

```
95-100: ✅ Excellent   - Production ready
85-94:  ✓  Good        - Minor improvements suggested
70-84:  ⚠️  Fair        - Review needed before production
50-69:  ❌ Poor        - Significant refactoring required
< 50:   🚨 Critical    - Major issues blocking deployment
```

---

## Best Practices

### **Code Analysis Workflow**

1. **Establish Baseline**
   ```
   /detect-architecture <solution>
   /full-review <solution>
   ```

2. **Generate Recommendations**
   ```
   /recommendations <solution>
   ```

3. **Prioritize Improvements**
   - Critical issues (blockers)
   - High impact, low effort
   - Architecture-breaking changes
   - Quick wins

4. **Implement Fixes**
   ```
   /architecture-fix <violation>
   /design-fix <violation>
   ```

5. **Validate Improvements**
   ```
   /full-review <solution> (compare to baseline)
   ```

### **CI/CD Integration**

Add to build pipeline:

```yaml
- name: Code Analysis
  run: |
    dotnet run --project CodeAnalyzer
    --solution ${{ github.workspace }}
    --output analysis-report.json

- name: Check Compliance
  run: |
    if ($compliance_score -lt 80) {
        Write-Error "Compliance score below 80%"
        exit 1
    }

- name: Comment on PR
  run: copilot /full-review
```

---

## Troubleshooting

### **"No violations found but score is low"**
- Check if solution structure is atypical
- Ensure configuration matches your architecture style
- Consider custom-layered-analyzer for non-standard patterns

### **"Analyzer fails to parse solution"**
- Verify solution path is correct
- Ensure solution builds successfully
- Check for C# language version compatibility

### **"Score varies between runs"**
- Rebuild solution between runs
- Clear cache in between analysis
- Ensure no uncommitted changes

---

## Quick Reference

### **Architecture Styles Quick Lookup**

| Architecture | Skill Base | Layer Validation | Service Boundaries | Deployment |
|--------------|-----------|-----------------|-------------------|-----------|
| Clean | `clean-architecture-*` | ✅ Strict | N/A | Single |
| Layered | `layered-architecture-*` | ✅ Horizontal | N/A | Single |
| Component | `component-architecture-*` | ✅ Feature-based | ✅ Via APIs | Single |
| Microservices | `microservices-architecture-*` | N/A | ✅ Independent | Multiple |
| Monolith | `monolith-architecture-*` | ✅ Internal | ✅ Logical | Single |
| Modular Monolith | `modular-monolith-*` | ✅ Module-based | ✅ Module APIs | Single |

### **Quality Dimensions Quick Lookup**

| Dimension | Skill | Min Score for Deploy | Typical Issues |
|-----------|-------|----------------------|-----------------|
| Architecture | `*-architecture-*` | 80% | Boundary violations |
| SOLID | `solid-*` | 75% | SRP, DIP violations |
| Performance | `performance-analyzer` | 70% | N+1 queries, allocations |
| Security | `security-analyzer` | 95% | Injection, secrets |
| Memory | `memory-analyzer` | 75% | LOH, boxing |
| Concurrency | `concurrency-analyzer` | 85% | Async/await issues |
| Reliability | `reliability-analyzer` | 80% | Missing retries |

---

## Support & Resources

- **Documentation:** See individual SKILL.md files
- **Examples:** GitHub repository `/examples` directory
- **Issues:** File issues with analysis results and solution structure
- **Contributions:** PRs welcome for new analysis patterns

---

**Last Updated:** 2026-04-26  
**Maintained By:** Enterprise Code Analysis Team

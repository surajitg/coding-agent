# Enterprise Code Analysis Agent - Practical Guide

## Real-World Usage Scenarios

This guide demonstrates practical use cases and how to interact with the code analysis agent.

---

## Scenario 1: First-Time Full Analysis

**Situation:** You're taking over a new .NET project and need to understand its code quality.

### Steps

**Step 1: Detect Architecture**
```
User: I just started on a new project at /projects/ecommerce-api. 
Can you tell me what architecture it uses?

Agent Response:
✓ Detected Architecture: Modular Monolith
  Confidence: 94%
  
Analysis:
├─ 4 feature modules: Orders, Payments, Inventory, Users
├─ Single deployable unit
├─ Module communication via interfaces
└─ Shared infrastructure layer

Next: Would you like a full compliance analysis?
```

**Step 2: Run Full Analysis**
```
User: Yes, run a full analysis to see how well it's structured.

Agent Response:
📊 FULL CODE ANALYSIS REPORT
════════════════════════════════════════════

📋 EXECUTIVE SUMMARY
Overall Score: 76% ⚠️ FAIR
Critical Issues: 2
High Priority: 5
Medium Priority: 8
Quick Wins: 3

🏗️ ARCHITECTURE ANALYSIS (78%)
Detected: Modular Monolith
Issues:
  ├─ Circular dependency: Orders → Inventory → Orders
  ├─ Orders module missing API contract definition
  └─ Shared database vs. module isolation

🎯 SOLID PRINCIPLES (72%)
SRP Violations: 3
├─ OrderService handles creating, saving, notifying
OCP Violations: 1
├─ PaymentProcessor uses type switches
DIP Violations: 2
├─ Direct PaymentGateway dependency

⚡ CODE QUALITY
├─ Performance: 64% ⚠️
│  ├─ 2 N+1 query patterns in OrderRepository
│  └─ 4 inefficient LINQ chains
├─ Security: 98% ✓
├─ Memory: 82% ✓
├─ Concurrency: 88% ✓
└─ Reliability: 75% ⚠️
   └─ 3 external calls missing timeouts

RECOMMENDATIONS (by priority):
1. [CRITICAL] Break circular dependency (2 hours)
2. [HIGH] Define Orders module API contract (1 hour)
3. [HIGH] Fix N+1 queries (3 hours)
4. [MEDIUM] Split OrderService (4 hours)
5. [QUICK WIN] Add ConfigureAwait(false) (30 mins)
```

**Step 3: Get Specific Recommendations**
```
User: The circular dependency is concerning. How do I fix it?

Agent Response:
🔗 CIRCULAR DEPENDENCY ANALYSIS

Current Flow:
  Orders → Inventory → Orders
  
Violation Path:
  OrderService.cs:42 → InventoryValidator.cs:15 → OrderValidator.cs:28

ROOT CAUSE:
Orders module imports Inventory for validation.
Inventory imports Orders for stock deduction.

✅ SOLUTION 1: Extract Validation (RECOMMENDED)
Create shared validation module (breaks dependency):
  Orders → Shared → Inventory
  Inventory → Shared
  No circular reference

Code Changes Needed:
  1. Create Validators folder in root
  2. Move OrderValidator to Validators/Orders/
  3. Move InventoryValidator to Validators/Inventory/
  4. Update imports
  5. Delete cross-module references

Estimated Effort: 1.5 hours
Risk: LOW - Moving existing code

✅ SOLUTION 2: Async Event-Based
Orders publishes StockNeeded event.
Inventory listens and validates asynchronously.

Effort: 3 hours
Risk: MEDIUM - Architectural change

I recommend SOLUTION 1. Would you like code samples?
```

---

## Scenario 2: Pre-Merge Code Review

**Situation:** A team member submitted a PR with changes. You want to analyze the changes before merging.

### Steps

```
User: Review this PR for the orders feature. 
Files changed:
- OrderService.cs
- OrderRepository.cs
- OrderValidator.cs

Agent Response:

📝 PULL REQUEST CODE REVIEW
═══════════════════════════════════════

🏗️ ARCHITECTURE ANALYSIS
✓ Changes maintain module boundaries
✓ No new circular dependencies
⚠️ OrderValidator introduces database dependency
   └─ Validators should not query DB
   └─ Suggestion: Move to repository pattern

🎯 SOLID ANALYSIS
❌ OrderService violates SRP (5 reasons to change):
   ├─ Creating orders
   ├─ Validating orders
   ├─ Saving to database
   ├─ Publishing events
   └─ Sending notifications

   RECOMMENDATION:
   OrderService → Create, Coordinate
   OrderValidator → Validate
   OrderRepository → Save
   OrderPublisher → Events
   OrderNotifier → Notifications

⚡ CODE QUALITY

Performance: ⚠️ ISSUE FOUND
  Line 42 in OrderRepository.cs:
  ```csharp
  foreach (var item in order.Items) {
      var pricing = _db.Pricing.FirstOrDefault(p => p.Id == item.PricingId);
      // N+1 query!
  }
  ```
  FIX: Batch load pricing
  ```csharp
  var pricingIds = order.Items.Select(i => i.PricingId);
  var pricing = _db.Pricing.Where(p => pricingIds.Contains(p.Id));
  ```

Reliability: ⚠️ ISSUE FOUND
  Line 18 in OrderService.cs:
  PaymentGateway call has no timeout configured
  ```csharp
  var result = await _paymentGateway.ChargeAsync(order);
  ```
  FIX: Add timeout
  ```csharp
  using var cts = new CancellationTokenSource(TimeSpan.FromSeconds(30));
  var result = await _paymentGateway.ChargeAsync(order, cts.Token);
  ```

Security: ✓ GOOD
Concurrency: ✓ GOOD
Memory: ✓ GOOD

📊 SUMMARY
──────────
Code Quality Score: 71% ⚠️

✅ What's Good:
  • Proper async/await usage
  • Good exception handling
  • Clear method names

❌ What Needs Work:
  1. Split OrderService (SRP violation)
  2. Fix N+1 query
  3. Add payment timeout
  4. Remove DB dependency from validator

RECOMMENDATION: 
Request changes. Issues are not blocking but should be fixed
before merge to maintain code quality.

Would you like me to suggest specific code changes?
```

---

## Scenario 3: Performance Optimization Sprint

**Situation:** Your product team wants to improve application performance. You need to identify bottlenecks.

### Steps

```
User: Our application is slow. Can you find performance issues?

Agent Response:

⚡ PERFORMANCE ANALYSIS REPORT
═════════════════════════════════════

Performance Score: 64% ❌ POOR

🔍 CRITICAL ISSUES (Do These First)

1. N+1 Query in Order Processing (HIGH IMPACT)
   Location: OrderService.cs:156-162
   Impact: Queries multiply with order items
   
   CURRENT CODE:
   ```csharp
   var orders = _db.Orders.ToList();
   foreach (var order in orders) {
       var customer = _db.Customers.FirstOrDefault(c => c.Id == order.CustomerId);
       var items = _db.OrderItems.Where(i => i.OrderId == order.Id).ToList();
       foreach (var item in items) {
           var product = _db.Products.FirstOrDefault(p => p.Id == item.ProductId);
       }
   }
   ```
   
   QUERIES: 1 (orders) + n (customers) + n (items) + n*m (products) = TOO MANY!
   
   OPTIMIZED CODE:
   ```csharp
   var orders = _db.Orders
       .Include(o => o.Customer)
       .Include(o => o.Items)
           .ThenInclude(i => i.Product)
       .ToList();
   ```
   
   EXPECTED IMPROVEMENT: 95% query reduction
   EFFORT: 1 hour
   PRIORITY: 🔥 CRITICAL

2. Inefficient LINQ Usage (MEDIUM IMPACT)
   Location: InventoryService.cs:89
   
   CURRENT: inventory.ToList().Where(i => i.Available).ToList()
   BETTER: inventory.Where(i => i.Available).ToList()
   
   IMPACT: Reduces in-memory operations
   EFFORT: 15 minutes
   PRIORITY: HIGH

3. String Concatenation in Loop (LOW IMPACT)
   Location: ReportGenerator.cs:234
   
   CURRENT:
   ```csharp
   string report = "";
   foreach (var item in items) {
       report += item.ToString() + ",";
   }
   ```
   
   BETTER:
   ```csharp
   var sb = new StringBuilder();
   foreach (var item in items) {
       sb.Append(item).Append(",");
   }
   var report = sb.ToString();
   ```
   
   IMPACT: Avoids string allocations
   EFFORT: 20 minutes

⏱️ QUICK WINS (Easy Fixes)

4. ToList() Call Before Where()
   Locations: 4 instances in ProductService.cs
   Fix: Move Where() before ToList()
   
5. Missing Eager Loading
   Locations: 3 queries in CustomerService.cs
   Fix: Add .Include() for related entities

📊 ESTIMATED IMPACT

If you fix items 1-3:
├─ Query time: ↓ 80%
├─ Response time: ↓ 40%
├─ Database load: ↓ 60%
└─ Total effort: 2-3 hours

PRIORITY PLAN:
Week 1: Fix N+1 queries
Week 1: Fix inefficient LINQ
Week 2: Fix string concatenation
Week 2: Fix eager loading

Would you like code samples for each issue?
```

---

## Scenario 4: Architecture Review for New Feature

**Situation:** Planning a new feature and want to ensure it fits the architecture.

### Steps

```
User: We're adding a Notifications feature to our modular monolith.
How should we structure it to maintain our architecture?

Agent Response:

🏗️ MODULAR MONOLITH ARCHITECTURE REVIEW
═════════════════════════════════════════

Current Architecture:
├─ Orders Module
├─ Payments Module
├─ Inventory Module
├─ Users Module
└─ Shared Infrastructure

✅ RECOMMENDED NOTIFICATION MODULE STRUCTURE

NotificationsModule/
├─ Domain/
│  ├─ INotificationService.cs
│  ├─ Notification.cs
│  └─ NotificationTemplate.cs
├─ Application/
│  ├─ SendNotificationHandler.cs
│  └─ NotificationAppService.cs
├─ Infrastructure/
│  ├─ EmailProvider.cs
│  ├─ SmsProvider.cs
│  └─ NotificationRepository.cs
└─ Contracts/
   └─ INotificationApi.cs (for inter-module communication)

🔌 MODULE COMMUNICATION

Orders Module WANTS TO:
  Send email when order created
  
How: OrderCreatedEvent
  ├─ Orders publishes event
  ├─ Notifications subscribes
  └─ No direct dependency

Code Example:
```csharp
// In Orders/Application
public class CreateOrderHandler {
    public async Task Handle(CreateOrderCommand cmd) {
        var order = new Order(cmd);
        _eventBus.Publish(new OrderCreatedEvent(order.Id, order.CustomerEmail));
    }
}

// In Notifications/Application
public class OrderCreatedEventHandler {
    public async Task Handle(OrderCreatedEvent evt) {
        await _notificationService.SendOrderConfirmationAsync(evt.Email);
    }
}
```

🎯 ARCHITECTURE VALIDATION

✓ Module autonomy maintained
✓ Clear contract via INotificationApi
✓ No circular dependencies
✓ Event-based communication
✓ Shared infrastructure usage
✓ Testable in isolation

⚠️ IMPORTANT CONSIDERATIONS

1. Database Strategy:
   ├─ Option A: Separate notifications table (RECOMMENDED)
   │  └─ Better isolation
   ├─ Option B: Shared database with partition
   │  └─ Simple but less isolated
   
2. External Integrations:
   ├─ Email: SendGrid/AWS SES
   ├─ SMS: Twilio/AWS SNS
   └─ Store credentials in KeyVault, not code

3. Error Handling:
   ├─ Email fails: Queue for retry
   ├─ SMS fails: Log and alert
   └─ Use pattern: circuit breaker + retry

✅ CHECKLIST BEFORE IMPLEMENTATION

[ ] Module structure defined
[ ] Communication contracts agreed
[ ] Database schema planned
[ ] External integrations chosen
[ ] Error handling strategy decided
[ ] Team architecture review complete
[ ] Performance impact assessed

Would you like detailed code templates for each layer?
```

---

## Scenario 5: Refactoring Large Service

**Situation:** You have a large service violating SRP. You need to split it correctly.

### Steps

```
User: My UserService.cs has 1000 lines and does too much.
How should I refactor it?

Agent Response:

🔄 SERVICE REFACTORING ANALYSIS
════════════════════════════════

Current UserService - SRP Violations Found:
├─ Create user (responsibility 1)
├─ Update user (responsibility 2)
├─ Delete user (responsibility 3)
├─ Send welcome email (responsibility 4)
├─ Log to audit trail (responsibility 5)
├─ Generate JWT tokens (responsibility 6)
├─ Validate user data (responsibility 7)
└─ Hash passwords (responsibility 8)

⚠️ 8 REASONS TO CHANGE THIS CLASS

REFACTORED ARCHITECTURE
```

```
UserService → Orchestrates user creation flow
├─ Calls IUserRepository to persist
├─ Calls IUserValidator to validate
├─ Calls IPasswordHasher to secure
├─ Calls IUserNotifier to email
├─ Calls IAuditLogger to log
└─ Calls ITokenProvider to generate JWT

New Classes:

1. UserRepository (Data Access)
   ├─ CreateAsync(user)
   ├─ UpdateAsync(user)
   ├─ DeleteAsync(userId)
   └─ GetByIdAsync(userId)

2. UserValidator (Validation)
   ├─ ValidateEmail(email)
   ├─ ValidatePassword(password)
   └─ ValidateUserData(user)

3. PasswordHasher (Security)
   ├─ HashPassword(password)
   └─ VerifyPassword(hash, password)

4. UserNotifier (Notifications)
   ├─ SendWelcomeEmailAsync(user)
   ├─ SendConfirmationAsync(user)
   └─ SendResetEmailAsync(user)

5. AuditLogger (Logging)
   ├─ LogUserCreated(userId)
   ├─ LogUserUpdated(userId)
   └─ LogUserDeleted(userId)

6. TokenProvider (Token Management)
   ├─ GenerateToken(user)
   └─ RefreshToken(token)

7. UserService (REFACTORED - Orchestrator Only)
   ├─ CreateUserAsync(command)
   ├─ UpdateUserAsync(command)
   ├─ DeleteUserAsync(userId)
   └─ AuthenticateAsync(credentials)

DEPENDENCY INJECTION SETUP
```

```csharp
services.AddScoped<IUserRepository, UserRepository>();
services.AddScoped<IUserValidator, UserValidator>();
services.AddScoped<IPasswordHasher, PasswordHasher>();
services.AddScoped<IUserNotifier, UserNotifier>();
services.AddScoped<IAuditLogger, AuditLogger>();
services.AddScoped<ITokenProvider, TokenProvider>();
services.AddScoped<IUserService, UserService>();
```

```
REFACTORED UserService.cs (Orchestrator)
```

```csharp
public class UserService : IUserService {
    private readonly IUserRepository _repository;
    private readonly IUserValidator _validator;
    private readonly IPasswordHasher _hasher;
    private readonly IUserNotifier _notifier;
    private readonly IAuditLogger _auditLogger;
    private readonly ITokenProvider _tokenProvider;

    public UserService(
        IUserRepository repository,
        IUserValidator validator,
        IPasswordHasher hasher,
        IUserNotifier notifier,
        IAuditLogger auditLogger,
        ITokenProvider tokenProvider) {
        _repository = repository;
        _validator = validator;
        _hasher = hasher;
        _notifier = notifier;
        _auditLogger = auditLogger;
        _tokenProvider = tokenProvider;
    }

    public async Task<UserDto> CreateUserAsync(CreateUserCommand cmd) {
        // Validate
        await _validator.ValidateUserData(new User { 
            Email = cmd.Email, 
            Password = cmd.Password 
        });

        // Hash password
        var hashedPassword = _hasher.HashPassword(cmd.Password);

        // Create user
        var user = new User { 
            Email = cmd.Email, 
            PasswordHash = hashedPassword 
        };
        await _repository.CreateAsync(user);

        // Send welcome email
        await _notifier.SendWelcomeEmailAsync(user);

        // Log
        _auditLogger.LogUserCreated(user.Id);

        // Return
        return new UserDto(user);
    }
}
```

📊 REFACTORING IMPACT

Before:
├─ UserService.cs: 1000+ lines ❌
├─ Hard to test: Requires mocking everything
├─ High coupling: Everything depends on UserService
└─ Difficult to reuse: Can't use components independently

After:
├─ UserService.cs: ~100 lines ✓
├─ Easy to test: Each component has single responsibility
├─ Low coupling: Components are independent
└─ Highly reusable: Each component can be used separately

🎯 REFACTORING CHECKLIST

[ ] Create IUserRepository interface
[ ] Create UserRepository implementation
[ ] Create IUserValidator interface
[ ] Create UserValidator implementation
[ ] Create IPasswordHasher interface
[ ] Create PasswordHasher implementation
[ ] Create IUserNotifier interface
[ ] Create UserNotifier implementation
[ ] Create IAuditLogger interface
[ ] Create AuditLogger implementation
[ ] Create ITokenProvider interface
[ ] Create TokenProvider implementation
[ ] Update UserService to use interfaces
[ ] Update DI configuration
[ ] Update tests for each component
[ ] Verify existing functionality
[ ] Deploy and monitor

ESTIMATED EFFORT: 3-4 hours
RISK LEVEL: LOW (refactoring, no logic changes)
TESTING IMPACT: Increases (more unit tests)
MAINTAINABILITY: Greatly improves

Would you like specific code templates for each component?
```

---

## Scenario 6: Security Audit

**Situation:** Your company wants to ensure code is secure before a major release.

### Steps

```
User: Run a security audit on our entire codebase before release.

Agent Response:

🔒 SECURITY AUDIT REPORT
═════════════════════════════

Security Score: 94% (ACCEPTABLE FOR RELEASE)

Critical Issues: 0 ✓
High Severity: 1 ⚠️
Medium Severity: 2

🔴 HIGH SEVERITY

1. Hardcoded Connection String
   Location: appsettings.json:line 12
   
   CURRENT:
   ```json
   {
     "ConnectionString": "Server=prod-db.azure.com;User=admin;Password=P@ssw0rd123;"
   }
   ```
   
   ISSUE: Credentials exposed in version control
   RISK: Anyone with repo access can access database
   
   FIX: Use KeyVault
   ```json
   {
     "ConnectionString": "@Microsoft.KeyVault(SecretUri=https://...)"
   }
   ```
   
   EFFORT: 30 minutes
   PRIORITY: 🔥 FIX BEFORE RELEASE

🟡 MEDIUM SEVERITY

2. Missing Input Validation on API Endpoint
   Location: UserController.cs:CreateUser()
   
   CURRENT:
   ```csharp
   [HttpPost("create")]
   public async Task<IActionResult> CreateUser(UserDto dto) {
       // No validation!
       var user = new User { Email = dto.Email };
   }
   ```
   
   FIX:
   ```csharp
   [HttpPost("create")]
   public async Task<IActionResult> CreateUser([FromBody] CreateUserDto dto) {
       // Auto-validation via attributes
   }
   
   public class CreateUserDto {
       [Required]
       [EmailAddress]
       public string Email { get; set; }
       
       [Required]
       [MinLength(8)]
       public string Password { get; set; }
   }
   ```
   
   EFFORT: 1 hour

3. SQL Queries Need Parameterization Review
   Location: ProductRepository.cs:GetByName()
   
   All queries appear parameterized ✓
   But spotted one that needs review: line 156

📊 AUDIT SUMMARY

✓ GOOD FINDINGS:
  ├─ No direct SQL injection vulnerabilities
  ├─ Passwords are properly hashed
  ├─ JWTs have appropriate expiration
  ├─ HTTPS enforced in configuration
  └─ Security headers configured

⚠️ TO DO BEFORE RELEASE:
  1. Remove hardcoded connection string [30 min] 🔥
  2. Add input validation to 2 endpoints [1 hour]
  3. Review stored procedure at line 156 [15 min]
  4. Update security headers in production config [10 min]

✅ RELEASE READINESS: 95%

All critical issues must be fixed before production deployment.
Current status: BLOCKED pending high-priority fixes.

Once fixed: ✓ APPROVED FOR RELEASE

Would you like detailed patches for each issue?
```

---

## Scenario 7: Continuous Monitoring

**Situation:** You want to monitor code quality trends over time.

### Steps

```
User: Show me our code quality trends over the last 3 months.

Agent Response:

📈 CODE QUALITY TRENDS (Last 3 Months)
════════════════════════════════════════

OVERALL SCORE TREND:
   Month 1: 72% 📊
   Month 2: 76% ↗ (+4%)
   Month 3: 81% ↗ (+5%)
   
   TREND: ✓ IMPROVING

SCORE BY DIMENSION:
                 M1   M2   M3   Δ
Architecture:   78%  82%  85%  ↗
SOLID:          68%  72%  78%  ↗
Performance:    65%  71%  76%  ↗
Security:       96%  97%  98%  ↗
Memory:         74%  78%  82%  ↗
Concurrency:    82%  85%  88%  ↗
Reliability:    71%  75%  79%  ↗

BEST IMPROVEMENTS:
🏆 SOLID Principles: +10% (SRP violations reduced from 8 to 3)
🥈 Performance: +11% (N+1 queries fixed)
🥉 Reliability: +8% (Error handling improved)

REMAINING CHALLENGES:
⚠️ Performance still below 80% target
   └─ Focus area: Batch operations

⚠️ Architecture compliance still below 90% target
   └─ Focus area: Module boundaries

✅ ACHIEVEMENTS:
  • No critical security issues (consistent)
  • Concurrency safety improved 6%
  • New team members trained on SOLID (seen in scores)

🎯 NEXT MONTH TARGETS:
  Architecture:   85% → 88%
  Performance:    76% → 82%
  Overall:        81% → 85%

VELOCITY: Continue current improvement pace to hit 88% by end of Q2.

Continue your refactoring efforts - they're working!
```

---

## Key Takeaways

### When to Use Each Scenario

| Scenario | Best Time | Action |
|----------|-----------|--------|
| Full Analysis | Start of project or sprint | Establish baseline |
| PR Review | Before merge | Maintain standards |
| Performance | Performance issues reported | Identify bottlenecks |
| Architecture | Planning new feature | Ensure alignment |
| Refactoring | Known technical debt | Plan improvements |
| Security | Before release | Audit compliance |
| Monitoring | Regular cadence | Track trends |

---

**Version:** 1.0 | **Updated:** 2026-04-26

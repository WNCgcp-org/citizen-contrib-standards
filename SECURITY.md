# Security Guide for Citizen Developers

This guide explains security concepts in plain language. These aren't optional—they're required for approval.

## Why Security Matters

A single security mistake can:
- Expose patient data (HIPAA violation = major fines)
- Allow attackers into our systems
- Damage Wellnecity's reputation
- Result in legal liability

**The good news:** Following these rules makes security straightforward.

---

## The Golden Rules

### 1. Never Hardcode Secrets

**What's a secret?**
- Database passwords
- API keys
- Connection strings
- Authentication tokens
- Private keys

**Wrong (instant rejection):**

```typescript
// NEVER DO THIS
const dbPassword = "super_secret_password_123";
const apiKey = "sk-ant-api03-xxxxxxxxxxxxx";
const mongoUri = "mongodb://admin:password123@production-server:27017";
```

**Right:**

```typescript
// ALWAYS DO THIS
const mongoUri = process.env.MONGODB_URI;
const apiKey = process.env.ANTHROPIC_API_KEY;
```

**Where do secrets go?**
- In a `.env` file locally (never committed to Git)
- In Kubernetes secrets for production (Engineering handles this)

### 2. Always Use Environment Variables

Create a `.env.example` file that shows what variables are needed (without real values):

```bash
# .env.example - commit this file
MONGODB_URI=mongodb://user:password@localhost:27017/dbname
ANTHROPIC_API_KEY=sk-ant-xxxxx
SECRET_KEY=your-secret-here
```

Create a `.env` file with real values (never commit this):

```bash
# .env - NEVER commit this file
MONGODB_URI=mongodb://dev:dev@localhost:27017/app?authSource=admin
ANTHROPIC_API_KEY=sk-ant-api03-real-key-here
SECRET_KEY=actually-secret-value
```

### 3. Make Sure .env is in .gitignore

Your `.gitignore` must include:

```gitignore
# Environment files - CRITICAL
.env
.env.local
.env.*.local

# Also ignore
*.pem
*.key
```

**How to check:** Run `git status`. If `.env` shows up, it's not properly ignored.

---

## Validating User Input

**The problem:** Users (and attackers) can send any data to your application. Never trust it.

**Wrong:**

```typescript
// DANGEROUS - trusting user input
const userId = request.body.userId;
const user = await User.findById(userId); // What if userId is malicious?
```

**Right:**

```typescript
import { z } from 'zod';

// Define what valid input looks like
const GetUserSchema = z.object({
  userId: z.string().length(24) // MongoDB ObjectIds are 24 characters
});

// Validate before using
const result = GetUserSchema.safeParse(request.body);

if (!result.success) {
  return Response.json({
    success: false,
    error: { code: 'INVALID_INPUT', message: 'Invalid user ID format' }
  }, { status: 400 });
}

// Now it's safe to use
const user = await User.findById(result.data.userId);
```

### Common Validation Rules

```typescript
import { z } from 'zod';

// Email
const email = z.string().email();

// Non-empty string with max length
const name = z.string().min(1).max(100);

// Positive number
const quantity = z.number().int().positive();

// One of specific values
const status = z.enum(['pending', 'active', 'completed']);

// Optional field
const description = z.string().max(500).optional();

// Date
const birthDate = z.coerce.date();
```

---

## Never Expose Internal Errors

**The problem:** Error messages can reveal system details to attackers.

**Wrong:**

```typescript
catch (error) {
  return Response.json({
    error: error.message // Might say "MongoDB connection failed at 10.0.1.5:27017"
  });
}
```

**Right:**

```typescript
catch (error) {
  // Log the real error internally
  console.error('Database error:', error);

  // Return a generic message to the user
  return Response.json({
    success: false,
    error: {
      code: 'SERVER_ERROR',
      message: 'An unexpected error occurred. Please try again.'
    }
  }, { status: 500 });
}
```

---

## Data Classification

Not all data is equal. Know what you're handling.

| Classification | Examples | Extra Requirements |
|----------------|----------|-------------------|
| **PHI (Protected Health Info)** | Patient names, medical records, diagnoses | HIPAA compliance, encryption, audit logs |
| **PII (Personally Identifiable)** | Emails, phone numbers, addresses | Encryption at rest, access controls |
| **Internal** | Department budgets, internal memos | Standard security |
| **Public** | Marketing materials, public announcements | Minimal restrictions |

**If your app handles PHI or PII:** You must work directly with Engineering. Standard citizen developer process may not be sufficient.

---

## Authentication & Authorization

### Authentication (Who are you?)

**Never build your own authentication.** Use the company auth service:

```typescript
import { withAuth } from '@wellnecity/auth';

export const GET = withAuth(async (request, { user }) => {
  // user is verified and available
  return Response.json({ success: true, data: { name: user.name } });
});
```

### Authorization (What can you do?)

Even authenticated users shouldn't access everything:

```typescript
export const GET = withAuth(async (request, { user }) => {
  const itemId = request.params.id;
  const item = await Item.findById(itemId);

  // Check if user owns this item or has permission
  if (item.ownerId !== user.id && user.role !== 'admin') {
    return Response.json({
      success: false,
      error: { code: 'FORBIDDEN', message: 'You cannot access this item' }
    }, { status: 403 });
  }

  return Response.json({ success: true, data: item });
});
```

---

## HTTPS Only

All production applications use HTTPS. You don't need to configure this—it's automatic in our Kubernetes environment.

**For local development:** HTTP is fine (`http://localhost:3000`)

**For production:** Always HTTPS (Engineering handles SSL certificates)

---

## Common Attacks to Understand

### SQL/NoSQL Injection

**What:** Attacker sends malicious database commands through input fields.

**Protected by:** Using Mongoose (it parameterizes queries automatically)

```typescript
// Mongoose protects you automatically
const user = await User.findOne({ email: userInput });
// Even if userInput contains malicious commands, Mongoose sanitizes it
```

### Cross-Site Scripting (XSS)

**What:** Attacker injects malicious scripts into your pages.

**Protected by:** React (it escapes content automatically)

```tsx
// React protects you automatically
<div>{userInput}</div>
// Even if userInput contains <script>alert('hacked')</script>, it's escaped

// DANGER: This bypasses protection - never do this with user input
<div dangerouslySetInnerHTML={{ __html: userInput }} />
```

### Cross-Site Request Forgery (CSRF)

**What:** Attacker tricks authenticated users into performing unwanted actions.

**Protected by:** Using proper authentication tokens (handled by `@wellnecity/auth`)

---

## Security Checklist

Before submitting your PR, verify:

- [ ] No secrets in code (passwords, API keys, connection strings)
- [ ] `.env` is in `.gitignore`
- [ ] `.env.example` exists with placeholder values
- [ ] All user input is validated with Zod
- [ ] Error messages don't expose internal details
- [ ] Using `@wellnecity/auth` for authentication
- [ ] Authorization checks exist for sensitive operations
- [ ] No `dangerouslySetInnerHTML` with user content
- [ ] `npm audit` shows 0 high/critical vulnerabilities

---

## If You're Not Sure

**Ask before building.** Security questions go to:

- **Teams:** `Citizen Developer Help` or `Security` channel
- **Principals:** Jonathan Rodriguez, Derrick Woolworth, Will Queen
- **Email:** security@wellnecity.com

It's always better to ask than to build something insecure.

---

## Reporting Security Issues

Found a security problem in an existing application?

1. **Don't post it publicly** (Teams channels, email threads, etc.)
2. **Email security@wellnecity.com** directly
3. **Include:** What you found, how to reproduce it, potential impact

Thank you for helping keep Wellnecity secure.

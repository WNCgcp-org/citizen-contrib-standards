# Wellnecity Development Standards

This document defines the technical standards for all citizen-developed applications. These are not suggestions—they are requirements for approval.

## Why Standards Exist

1. **Security:** Consistent patterns mean fewer vulnerabilities
2. **Maintainability:** Anyone on Engineering can support your app
3. **Scalability:** Apps built correctly can grow with demand
4. **Compliance:** Healthcare data has legal requirements (HIPAA)

---

## Approved Technology Stack

### Languages & Database Connectivity

| Language | Approved For | Version | Database ORM/ODM |
|----------|--------------|---------|------------------|
| TypeScript/Node.js | Web apps, APIs, scripts | Node 20+, TS 5.x | **Mongoose (required)** |
| Python | Data processing, ML, automation | 3.11+ | **Pydantic + PyMongo/Motor** |

**Not Approved:** Java, Go, Rust, PHP, Ruby, C#, shell scripts for production, raw MongoDB drivers without ORM/ODM

### Frontend Frameworks

| Framework | Status | Notes |
|-----------|--------|-------|
| React | Approved | Use with TypeScript |
| Next.js | Approved | Preferred for new apps |
| Vue | Not Approved | |
| Angular | Not Approved | |
| Svelte | Not Approved | |

### Backend Frameworks

| Framework | Status | Notes |
|-----------|--------|-------|
| Next.js API Routes | Approved | Preferred |
| Express.js | Approved | TypeScript required |
| FastAPI (Python) | Approved | For Python-specific needs |
| Flask | Not Approved | Use FastAPI instead |
| Django | Not Approved | |

### Databases

| Database | Status | Notes |
|----------|--------|-------|
| MongoDB | Approved | **Required** - the only approved database |
| Redis | Approved | Caching only |
| PostgreSQL | Not Approved | Use MongoDB |
| MySQL | Not Approved | Use MongoDB |
| SQLite | Not Approved | Use MongoDB |

### Local Development Requirements

| Tool | Required | Notes |
|------|----------|-------|
| Docker Desktop | Yes | Required for local MongoDB container |
| Node.js | Yes | Version 20+ |
| Git | Yes | For version control |

**You must have Docker Desktop installed** on your Mac or Windows machine to develop applications. Engineering provides a standard `docker-compose.yml` for local MongoDB.

### External Services

| Service | Status | Notes |
|---------|--------|-------|
| Anthropic Claude API | Approved | Via approved SDK |
| OpenAI API | Approved | Requires justification |
| AWS S3 | Approved | Via company account only |
| Twilio | Requires Approval | Case-by-case |
| SendGrid | Requires Approval | Case-by-case |
| Stripe | Not Approved | Finance team handles payments |

---

## Project Structure

All projects must follow this structure:

```
your-project/
├── README.md              # Required - must answer 4 questions
├── CLAUDE.md              # Required - Claude Code instructions
├── package.json           # Required - dependencies and scripts
├── tsconfig.json          # Required for TypeScript projects
├── docker-compose.yml     # Required - local MongoDB setup
├── .env.example           # Required - template for env vars (no real values!)
├── .gitignore             # Required
├── src/
│   ├── app/               # Next.js app directory (if using Next.js)
│   ├── components/        # React components
│   ├── lib/
│   │   └── db.ts          # Required - MongoDB connection (use standard pattern)
│   ├── models/            # Required - Mongoose schema definitions
│   ├── api/               # API routes or backend code
│   └── types/             # TypeScript type definitions
├── tests/                 # Required - test files
├── docs/                  # Optional - additional documentation
└── scripts/               # Optional - build/deploy scripts
```

### For Python Projects

```
your-project/
├── README.md              # Required
├── CLAUDE.md              # Required
├── pyproject.toml         # Required - dependencies (include pydantic, pymongo/motor)
├── docker-compose.yml     # Required - local MongoDB setup
├── .env.example           # Required
├── .gitignore             # Required
├── src/
│   └── your_project/
│       ├── __init__.py
│       ├── main.py
│       ├── db.py          # Required - MongoDB connection
│       ├── models/        # Required - Pydantic models
│       │   └── __init__.py
│       └── ...
├── tests/                 # Required
└── docs/                  # Optional
```

---

## Code Standards

### TypeScript Requirements

```typescript
// REQUIRED: Strict mode in tsconfig.json
{
  "compilerOptions": {
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true
  }
}
```

```typescript
// GOOD: Explicit types
function calculateTotal(items: CartItem[]): number {
  return items.reduce((sum, item) => sum + item.price, 0);
}

// BAD: Implicit any
function calculateTotal(items) {
  return items.reduce((sum, item) => sum + item.price, 0);
}
```

### Python Requirements

```python
# REQUIRED: Type hints for all functions
def calculate_total(items: list[CartItem]) -> float:
    return sum(item.price for item in items)

# REQUIRED: Use Pydantic for all data models (especially database models)
from pydantic import BaseModel, Field
from typing import Optional
from datetime import datetime

class CartItem(BaseModel):
    name: str
    price: float
    quantity: int = Field(gt=0)
    created_at: datetime = Field(default_factory=datetime.utcnow)

# For MongoDB documents, use Pydantic with PyObjectId
from bson import ObjectId
from pydantic import ConfigDict

class PyObjectId(ObjectId):
    @classmethod
    def __get_validators__(cls):
        yield cls.validate

    @classmethod
    def validate(cls, v, handler):
        if not ObjectId.is_valid(v):
            raise ValueError("Invalid ObjectId")
        return ObjectId(v)

class UserModel(BaseModel):
    model_config = ConfigDict(arbitrary_types_allowed=True)

    id: Optional[PyObjectId] = Field(default=None, alias="_id")
    email: str
    name: str
    department: str
```

### Naming Conventions

| Type | Convention | Example |
|------|------------|---------|
| Files | kebab-case | `user-profile.tsx` |
| Components | PascalCase | `UserProfile` |
| Functions | camelCase | `getUserProfile` |
| Variables | camelCase | `userProfile` |
| Constants | SCREAMING_SNAKE | `MAX_RETRIES` |
| Database tables | snake_case | `user_profiles` |
| Environment vars | SCREAMING_SNAKE | `DATABASE_URL` |

---

## Database Standards

### MongoDB with Mongoose (Node.js) or Pydantic (Python)

All applications must use MongoDB with the **required** ORM/ODM:

| Language | Required Stack | Raw Driver Allowed? |
|----------|----------------|---------------------|
| Node.js/TypeScript | **Mongoose** | No |
| Python | **Pydantic + PyMongo/Motor** | No |

**Using raw MongoDB drivers without Mongoose/Pydantic will result in rejection.**

### Local Development Setup

**Step 1:** Ensure Docker Desktop is running

**Step 2:** Add this `docker-compose.yml` to your project root:

```yaml
# docker-compose.yml
version: '3.8'
services:
  mongodb:
    image: mongo:7
    container_name: ${PROJECT_NAME:-app}-mongodb
    ports:
      - "27017:27017"
    environment:
      MONGO_INITDB_ROOT_USERNAME: dev
      MONGO_INITDB_ROOT_PASSWORD: dev
      MONGO_INITDB_DATABASE: app
    volumes:
      - mongodb_data:/data/db

volumes:
  mongodb_data:
```

**Step 3:** Start your local database:

```bash
docker-compose up -d
```

### Connection Pattern

Use the Mongoose ODM for all MongoDB interactions:

```typescript
// lib/db.ts - REQUIRED pattern
import mongoose from 'mongoose';

const MONGODB_URI = process.env.MONGODB_URI!;

if (!MONGODB_URI) {
  throw new Error('Please define the MONGODB_URI environment variable');
}

let cached = global.mongoose;

if (!cached) {
  cached = global.mongoose = { conn: null, promise: null };
}

export async function connectToDatabase() {
  if (cached.conn) {
    return cached.conn;
  }

  if (!cached.promise) {
    cached.promise = mongoose.connect(MONGODB_URI).then((mongoose) => {
      return mongoose;
    });
  }

  cached.conn = await cached.promise;
  return cached.conn;
}
```

### Schema Definition

```typescript
// models/user.ts - Example schema
import mongoose, { Schema, Document } from 'mongoose';

export interface IUser extends Document {
  email: string;
  name: string;
  department: string;
  createdAt: Date;
  updatedAt: Date;
}

const UserSchema = new Schema<IUser>(
  {
    email: { type: String, required: true, unique: true },
    name: { type: String, required: true },
    department: { type: String, required: true },
  },
  { timestamps: true }
);

export const User = mongoose.models.User || mongoose.model<IUser>('User', UserSchema);
```

### Environment Variables

```bash
# .env.example
# Local development (Docker)
MONGODB_URI=mongodb://dev:dev@localhost:27017/app?authSource=admin

# Production (provided by Engineering at deployment)
# MONGODB_URI=mongodb+srv://...
```

### Production Deployment

**You do NOT manage production databases.** When your app is approved:

1. Engineering provisions a MongoDB database
2. Engineering provides production `MONGODB_URI` credentials
3. Credentials are injected via Kubernetes secrets
4. Your code works unchanged—just uses the environment variable

### Python Connection Pattern (Pydantic + PyMongo)

```python
# db.py - REQUIRED pattern for Python projects
import os
from pymongo import MongoClient
from pymongo.database import Database

MONGODB_URI = os.getenv("MONGODB_URI")

if not MONGODB_URI:
    raise ValueError("Please define the MONGODB_URI environment variable")

_client: MongoClient | None = None
_db: Database | None = None

def get_database() -> Database:
    global _client, _db
    if _db is None:
        _client = MongoClient(MONGODB_URI)
        _db = _client.get_default_database()
    return _db

def close_database():
    global _client, _db
    if _client:
        _client.close()
        _client = None
        _db = None
```

```python
# Using with Pydantic models
from db import get_database
from models.user import UserModel

def create_user(user: UserModel) -> str:
    db = get_database()
    result = db.users.insert_one(user.model_dump(by_alias=True, exclude={"id"}))
    return str(result.inserted_id)

def get_user(user_id: str) -> UserModel | None:
    db = get_database()
    doc = db.users.find_one({"_id": ObjectId(user_id)})
    return UserModel(**doc) if doc else None
```

### What NOT to Do

```typescript
// BAD: Hardcoded connection string
const client = new MongoClient('mongodb://user:pass@prod-server:27017');

// BAD: Using raw MongoDB driver without Mongoose (Node.js)
import { MongoClient } from 'mongodb';
const client = new MongoClient(uri);
// This bypasses Mongoose - REJECTED

// BAD: Creating indexes in application code (do it in migrations)
await collection.createIndex({ email: 1 });
```

```python
# BAD: Using PyMongo without Pydantic models
def create_user(name, email):
    db.users.insert_one({"name": name, "email": email})  # No validation!

# BAD: Hardcoded connection string
client = MongoClient('mongodb://user:pass@prod-server:27017')
```

---

## API Standards

### REST Endpoints

```typescript
// Standard response format
interface ApiResponse<T> {
  success: boolean;
  data?: T;
  error?: {
    code: string;
    message: string;
  };
}

// GOOD: Consistent response
return Response.json({
  success: true,
  data: { user: foundUser }
});

// GOOD: Error response
return Response.json({
  success: false,
  error: {
    code: 'USER_NOT_FOUND',
    message: 'No user found with that ID'
  }
}, { status: 404 });
```

### HTTP Status Codes

| Code | When to Use |
|------|-------------|
| 200 | Success |
| 201 | Created |
| 400 | Bad request (client error) |
| 401 | Not authenticated |
| 403 | Not authorized |
| 404 | Not found |
| 500 | Server error |

### Authentication

All APIs must use the company authentication service:

```typescript
// REQUIRED: Use the auth middleware
import { withAuth } from '@wellnecity/auth';

export const GET = withAuth(async (request, { user }) => {
  // user is automatically available and verified
  const data = await getData(user.id);
  return Response.json({ success: true, data });
});
```

---

## Testing Requirements

### Minimum Coverage

- All API endpoints must have at least one test
- All utility functions must be tested
- Happy path + one error case minimum

### Test Structure

```typescript
// tests/api/users.test.ts
import { describe, it, expect } from 'vitest';

describe('GET /api/users', () => {
  it('returns users when authenticated', async () => {
    const response = await fetch('/api/users', {
      headers: { Authorization: 'Bearer test-token' }
    });
    const data = await response.json();

    expect(response.status).toBe(200);
    expect(data.success).toBe(true);
    expect(Array.isArray(data.data.users)).toBe(true);
  });

  it('returns 401 when not authenticated', async () => {
    const response = await fetch('/api/users');
    expect(response.status).toBe(401);
  });
});
```

### Running Tests

```bash
# Required scripts in package.json
{
  "scripts": {
    "test": "vitest",
    "test:coverage": "vitest --coverage"
  }
}
```

---

## Environment Variables

### Required Structure

Create `.env.example` (committed) and `.env` (gitignored):

```bash
# .env.example - TEMPLATE ONLY, NO REAL VALUES
MONGODB_URI=mongodb://dev:dev@localhost:27017/app?authSource=admin
ANTHROPIC_API_KEY=sk-ant-xxxxx
NEXTAUTH_SECRET=your-secret-here
NEXTAUTH_URL=http://localhost:3000
```

### .gitignore Requirements

```gitignore
# REQUIRED entries
.env
.env.local
.env.*.local
*.pem
*.key
node_modules/
.next/
dist/
coverage/
```

---

## Logging Standards

Use structured logging, not console.log:

```typescript
// GOOD: Structured logging
import { logger } from '@wellnecity/logger';

logger.info('User action completed', {
  userId: user.id,
  action: 'profile_update',
  duration: elapsed
});

logger.error('Database connection failed', {
  error: err.message,
  host: dbHost
});

// BAD: Console statements
console.log('User did something');
console.log(user);
```

### Log Levels

| Level | When to Use |
|-------|-------------|
| `error` | Something failed that shouldn't have |
| `warn` | Something unexpected but handled |
| `info` | Normal operations worth noting |
| `debug` | Detailed info for troubleshooting |

---

## Error Handling

### Required Pattern

```typescript
// GOOD: Explicit error handling
try {
  const result = await riskyOperation();
  return { success: true, data: result };
} catch (error) {
  logger.error('Operation failed', { error: error.message });
  return {
    success: false,
    error: { code: 'OPERATION_FAILED', message: 'Could not complete operation' }
  };
}

// BAD: Swallowing errors
try {
  const result = await riskyOperation();
} catch {
  // silent failure - NEVER DO THIS
}

// BAD: Exposing internal errors to users
catch (error) {
  return { error: error.message }; // May leak sensitive info
}
```

---

## Dependency Management

### Allowed and Prohibited

```json
// package.json
{
  "dependencies": {
    // APPROVED: Common utilities
    "lodash": "^4.x",
    "date-fns": "^3.x",
    "zod": "^3.x",

    // APPROVED: Database (MongoDB only)
    "mongoose": "^8.x",

    // APPROVED: UI
    "react": "^18.x",
    "next": "^14.x",
    "tailwindcss": "^3.x",

    // REQUIRES APPROVAL: Anything else
  }
}
```

### Security Scanning

Before every PR, run:

```bash
npm audit
# Must have 0 high or critical vulnerabilities
```

---

## Deployment Requirements

### Required Files

```yaml
# Dockerfile - required for deployment
FROM node:20-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
RUN npm run build
EXPOSE 3000
CMD ["npm", "start"]
```

### Health Check Endpoint

Every deployable app needs:

```typescript
// app/api/health/route.ts
export async function GET() {
  return Response.json({
    status: 'healthy',
    timestamp: new Date().toISOString()
  });
}
```

### Resource Limits

| Resource | Default Limit |
|----------|---------------|
| Memory | 512MB |
| CPU | 0.5 cores |
| Storage | 1GB |
| Instances | 1 |

Request increases via Engineering if needed.

---

## Review Checklist (Engineering Use)

Engineering will verify:

**General:**
- [ ] README answers all 4 questions
- [ ] CLAUDE.md is present and unmodified from template
- [ ] Only approved technologies used
- [ ] MongoDB used as database (no PostgreSQL, MySQL, SQLite)
- [ ] docker-compose.yml present for local MongoDB
- [ ] No hardcoded secrets or connection strings
- [ ] .env.example present, .env gitignored
- [ ] MONGODB_URI uses environment variable
- [ ] Tests exist and pass
- [ ] Health endpoint exists (if deployable)
- [ ] Dockerfile present (if deployable)

**Node.js/TypeScript Projects:**
- [ ] **Mongoose ODM used** (raw MongoDB driver = rejection)
- [ ] Database connection uses standard pattern from `lib/db.ts`
- [ ] Models defined in `src/models/` with TypeScript interfaces
- [ ] TypeScript strict mode enabled
- [ ] npm audit shows no high/critical vulnerabilities

**Python Projects:**
- [ ] **Pydantic used for all data models** (no raw dicts = rejection)
- [ ] PyMongo or Motor used with Pydantic validation
- [ ] Database connection uses standard pattern from `db.py`
- [ ] Type hints on all functions
- [ ] Models defined in `src/models/` directory

---

## Exceptions Process

Need something not on the approved list?

1. Create an issue in this repository with:
   - What you need
   - Why the approved alternatives don't work
   - Security implications you've considered
2. Tag `@wellnecity/engineering`
3. Wait for approval before proceeding

**Do not build first and ask forgiveness later.** Unapproved technologies will be rejected.

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2025-01-21 | Initial release |

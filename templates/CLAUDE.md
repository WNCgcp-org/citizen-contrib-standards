# Wellnecity Development Standards for Claude Code

You are helping build an internal application for Wellnecity. Follow these standards exactly.

## Technology Requirements

### Database: MongoDB ONLY with Required ORM/ODM

- **ALWAYS** use MongoDB as the database
- **NEVER** use PostgreSQL, MySQL, SQLite, or any other database

**For Node.js/TypeScript projects:**
- **ALWAYS** use **Mongoose** ODM - this is mandatory, not optional
- **NEVER** use raw MongoDB driver (`mongodb` package) - use Mongoose instead
- Rejection will occur if raw MongoDB driver is used without Mongoose

**For Python projects:**
- **ALWAYS** use **Pydantic** for data models - this is mandatory, not optional
- **ALWAYS** use PyMongo or Motor with Pydantic validation
- **NEVER** pass raw dictionaries to/from the database without Pydantic models
- Rejection will occur if Pydantic is not used for data validation

### Local Development

- Applications run with a local MongoDB container via Docker
- Always include a `docker-compose.yml` for local MongoDB
- Use environment variables for database connection strings

### Required docker-compose.yml

Every project must have this file:

```yaml
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

### Required Database Connection Pattern

Always create `src/lib/db.ts` with this exact pattern:

```typescript
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

### Required .env.example

```bash
# Local development (Docker MongoDB)
MONGODB_URI=mongodb://dev:dev@localhost:27017/app?authSource=admin

# Production credentials provided by Engineering at deployment
```

## Language and Framework Requirements

### Approved Stack - Node.js/TypeScript

- **Language:** TypeScript (strict mode required)
- **Frontend:** React with Next.js
- **Backend:** Next.js API routes or Express.js (TypeScript)
- **Database:** MongoDB with **Mongoose** (required)
- **Styling:** Tailwind CSS
- **Testing:** Vitest

### Approved Stack - Python

- **Language:** Python 3.11+ with type hints
- **Backend:** FastAPI
- **Database:** MongoDB with **Pydantic** + PyMongo/Motor (required)
- **Testing:** pytest

### Not Approved

- **Databases:** PostgreSQL, MySQL, SQLite, or any non-MongoDB database
- **Frameworks:** Vue, Angular, Svelte, Flask, Django
- **Languages:** Plain JavaScript (must use TypeScript), Java, Go, Rust, PHP
- **Database access:** Raw MongoDB driver without Mongoose (Node.js) or without Pydantic (Python)

## Project Structure

### Node.js/TypeScript Projects

```
project/
├── README.md
├── CLAUDE.md
├── package.json
├── tsconfig.json
├── docker-compose.yml
├── .env.example
├── .gitignore
├── src/
│   ├── app/              # Next.js app directory
│   ├── components/       # React components
│   ├── lib/
│   │   └── db.ts         # MongoDB connection (use pattern above)
│   ├── models/           # Mongoose schemas (REQUIRED)
│   └── types/            # TypeScript types
└── tests/
```

### Python Projects

```
project/
├── README.md
├── CLAUDE.md
├── pyproject.toml
├── docker-compose.yml
├── .env.example
├── .gitignore
├── src/
│   └── your_project/
│       ├── __init__.py
│       ├── main.py
│       ├── db.py         # MongoDB connection
│       └── models/       # Pydantic models (REQUIRED)
│           └── __init__.py
└── tests/
```

## TypeScript Configuration

Always use strict mode. Create `tsconfig.json` with:

```json
{
  "compilerOptions": {
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "target": "ES2022",
    "lib": ["dom", "dom.iterable", "ES2022"],
    "jsx": "preserve",
    "module": "esnext",
    "moduleResolution": "bundler",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "esModuleInterop": true,
    "skipLibCheck": true
  }
}
```

## Mongoose Schema Pattern (Node.js/TypeScript)

Define all schemas in `src/models/`:

```typescript
// src/models/example.ts
import mongoose, { Schema, Document } from 'mongoose';

export interface IExample extends Document {
  name: string;
  description: string;
  createdAt: Date;
  updatedAt: Date;
}

const ExampleSchema = new Schema<IExample>(
  {
    name: { type: String, required: true },
    description: { type: String, required: true },
  },
  { timestamps: true }
);

export const Example = mongoose.models.Example || mongoose.model<IExample>('Example', ExampleSchema);
```

## Pydantic Model Pattern (Python)

Define all models in `src/your_project/models/`:

```python
# src/your_project/models/example.py
from pydantic import BaseModel, Field, ConfigDict
from typing import Optional
from datetime import datetime
from bson import ObjectId

class PyObjectId(ObjectId):
    @classmethod
    def __get_pydantic_core_schema__(cls, _source_type, _handler):
        from pydantic_core import core_schema
        return core_schema.str_schema()

    @classmethod
    def validate(cls, v):
        if not ObjectId.is_valid(v):
            raise ValueError("Invalid ObjectId")
        return ObjectId(v)

class ExampleModel(BaseModel):
    model_config = ConfigDict(
        populate_by_name=True,
        arbitrary_types_allowed=True,
    )

    id: Optional[PyObjectId] = Field(default=None, alias="_id")
    name: str = Field(..., min_length=1, max_length=100)
    description: str = Field(..., max_length=500)
    created_at: datetime = Field(default_factory=datetime.utcnow)
    updated_at: datetime = Field(default_factory=datetime.utcnow)
```

### Python Database Connection Pattern

```python
# src/your_project/db.py
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
```

### Using Pydantic with PyMongo

```python
from db import get_database
from models.example import ExampleModel
from bson import ObjectId

def create_example(example: ExampleModel) -> str:
    db = get_database()
    data = example.model_dump(by_alias=True, exclude={"id"})
    result = db.examples.insert_one(data)
    return str(result.inserted_id)

def get_example(example_id: str) -> ExampleModel | None:
    db = get_database()
    doc = db.examples.find_one({"_id": ObjectId(example_id)})
    return ExampleModel(**doc) if doc else None
```

## API Response Pattern

Use consistent response format:

```typescript
// Success
return Response.json({
  success: true,
  data: { /* your data */ }
});

// Error
return Response.json({
  success: false,
  error: {
    code: 'ERROR_CODE',
    message: 'Human readable message'
  }
}, { status: 400 });
```

## Security Rules

### NEVER Do These

- **NEVER** hardcode secrets, passwords, API keys, or connection strings
- **NEVER** commit `.env` files (only `.env.example`)
- **NEVER** expose internal error messages to users
- **NEVER** trust user input without validation
- **NEVER** use `eval()` or dynamic code execution
- **NEVER** disable TypeScript strict mode

### ALWAYS Do These

- **ALWAYS** use environment variables for secrets
- **ALWAYS** validate and sanitize user input with Zod
- **ALWAYS** use parameterized queries (Mongoose handles this)
- **ALWAYS** include `.env` in `.gitignore`

## Input Validation

Use Zod for all input validation:

```typescript
import { z } from 'zod';

const CreateItemSchema = z.object({
  name: z.string().min(1).max(100),
  description: z.string().max(500).optional(),
  quantity: z.number().int().positive(),
});

// In your API route
const body = await request.json();
const result = CreateItemSchema.safeParse(body);

if (!result.success) {
  return Response.json({
    success: false,
    error: { code: 'VALIDATION_ERROR', message: result.error.message }
  }, { status: 400 });
}

const validatedData = result.data;
```

## Required .gitignore

```gitignore
# Dependencies
node_modules/

# Environment
.env
.env.local
.env.*.local

# Build
.next/
dist/
build/

# IDE
.vscode/
.idea/

# OS
.DS_Store
Thumbs.db

# Testing
coverage/

# Secrets
*.pem
*.key
```

## Testing Requirements

Write tests using Vitest. Every API endpoint needs at least one test:

```typescript
// tests/api/items.test.ts
import { describe, it, expect } from 'vitest';

describe('Items API', () => {
  it('creates an item successfully', async () => {
    // test implementation
  });

  it('returns error for invalid input', async () => {
    // test implementation
  });
});
```

## README Requirements

The project README must answer these four questions:

1. **What is it?** - Clear description of what the app does
2. **What is it built with?** - List the technologies used
3. **Should it be deployed?** - Yes/No and why
4. **Is it internal-facing?** - Yes/No

## Before Committing

Remind the user to:

1. Verify no secrets in code
2. Verify `.env` is gitignored
3. Verify README answers all 4 questions
4. **Node.js:** Run `npm audit` (0 high/critical vulnerabilities) and `npm test`
5. **Python:** Run `pip-audit` or `safety check` and `pytest`

## Helpful Commands

### For All Projects

```bash
# Start local MongoDB
docker-compose up -d

# Stop local MongoDB
docker-compose down

# View MongoDB logs
docker-compose logs -f mongodb
```

### For Node.js/TypeScript Projects

```bash
# Run tests
npm test

# Check for vulnerabilities
npm audit

# Type check
npx tsc --noEmit
```

### For Python Projects

```bash
# Run tests
pytest

# Check for vulnerabilities
pip-audit

# Type check
mypy src/
```

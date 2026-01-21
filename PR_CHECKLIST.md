# Pull Request Checklist

Complete this checklist before submitting your Pull Request for Engineering review. PRs missing items will be rejected without detailed feedback.

## How to Use This Checklist

1. Go through each item below
2. Check the box when complete
3. Copy this checklist into your PR description
4. Submit the PR with all boxes checked

---

## Pre-Submission Checklist

### Documentation

- [ ] **README answers all 4 questions:**
  - [ ] What is it?
  - [ ] What is it built with?
  - [ ] Should it be deployed?
  - [ ] Is it internal-facing?
- [ ] **CLAUDE.md** is present (copied from template, unmodified)

### Technology Stack

- [ ] Using **TypeScript** (not plain JavaScript)
- [ ] Using **MongoDB** as database (not PostgreSQL, MySQL, or SQLite)
- [ ] Using **Mongoose** ODM (not raw MongoDB driver)
- [ ] Using only approved frameworks (Next.js, React, Express.js, Tailwind CSS)
- [ ] `tsconfig.json` has `"strict": true`

### Project Structure

- [ ] `docker-compose.yml` present for local MongoDB
- [ ] `src/lib/db.ts` uses standard connection pattern
- [ ] `src/models/` directory contains Mongoose schemas
- [ ] `.env.example` present with all required variables
- [ ] `.gitignore` includes `.env`, `node_modules/`, `.next/`

### Security

- [ ] **No hardcoded secrets** in any file
- [ ] **No `.env` file committed** (only `.env.example`)
- [ ] `MONGODB_URI` comes from environment variable
- [ ] All user input validated with Zod
- [ ] No `eval()` or dynamic code execution
- [ ] Error messages don't expose internal details

### Code Quality

- [ ] TypeScript compiles without errors: `npx tsc --noEmit`
- [ ] No `any` types (except where absolutely necessary with comment explaining why)
- [ ] Consistent naming conventions followed
- [ ] API responses use standard format (`{ success, data }` or `{ success, error }`)

### Testing

- [ ] Tests exist in `tests/` directory
- [ ] All tests pass: `npm test`
- [ ] Every API endpoint has at least one test

### Dependencies

- [ ] `npm audit` shows **0 high or critical** vulnerabilities
- [ ] No unnecessary dependencies
- [ ] All dependencies are in `package.json` (no global installs required)

### Deployment Readiness (if applicable)

- [ ] `Dockerfile` present (if app should be deployed)
- [ ] Health check endpoint at `/api/health`
- [ ] App starts without errors: `npm run build && npm start`

---

## PR Description Template

Copy this into your PR description:

```markdown
## Summary

[Brief description of what this application does]

## The 4 Questions

1. **What is it?** [Your answer]
2. **What is it built with?** TypeScript, Next.js, MongoDB, [other technologies]
3. **Should it be deployed?** [Yes/No and why]
4. **Is it internal-facing?** [Yes/No]

## Checklist

[Paste completed checklist here]

## Testing Instructions

[How can Engineering test this application locally?]

1. Clone the repo
2. `npm install`
3. `docker-compose up -d`
4. `cp .env.example .env`
5. `npm run dev`
6. Open http://localhost:3000

## Screenshots (if applicable)

[Add screenshots of the application UI]

## Questions for Engineering

[Any questions or areas where you need guidance]
```

---

## Common Rejection Reasons

Avoid these instant rejection triggers:

| Issue | How to Fix |
|-------|------------|
| Missing README or incomplete answers | Fill out all 4 questions completely |
| Hardcoded database connection string | Use `process.env.MONGODB_URI` |
| `.env` file committed | Add to `.gitignore`, remove from repo |
| Using PostgreSQL/MySQL | Migrate to MongoDB |
| No tests | Add at least one test per endpoint |
| TypeScript errors | Run `npx tsc --noEmit` and fix all errors |
| High/critical npm vulnerabilities | Run `npm audit fix` or update packages |
| Missing CLAUDE.md | Copy from templates, do not modify |

---

## After Submission

1. **Tag `@wellnecity/engineering`** in the PR
2. **Wait for review** (1-2 business days for compliant PRs)
3. **Address feedback** if any issues are found
4. **Do not merge yourself** - Engineering will merge approved PRs

## Need Help?

- **Slack:** `#citizen-dev-help`
- **Office Hours:** Check the Engineering team calendar
- **Documentation:** [development-standards repository](https://github.com/wellnecity/development-standards)

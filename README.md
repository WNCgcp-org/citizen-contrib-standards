# Wellnecity Citizen Developer Program

This repository provides standards, templates, and guidance for Wellnecity team members who want to build internal applications using Claude Code—without needing a traditional software engineering background.

## What Is This?

The Citizen Developer Program enables anyone at Wellnecity to build productivity and tracking applications for their department using AI-assisted development (Claude Code). Engineering provides the guardrails; you provide the ideas.

**This is NOT:**
- A free-for-all where anything goes
- A way to bypass security or compliance
- An alternative to working with Engineering on complex systems

**This IS:**
- A structured path to get your internal tools built and deployed
- A set of standards that, if followed, lead to fast approvals
- A support system for non-developers to ship real software

## The Workflow

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                             │
│   YOU (Nancy, Eric, Deepika, etc.)                                          │
│     │                                                                       │
│     ▼                                                                       │
│   Build with Claude Code ──► Follow STANDARDS.md                            │
│     │                                                                       │
│     ▼                                                                       │
│   Push to GitHub ──► README must answer 4 questions                         │
│     │                                                                       │
│     ▼                                                                       │
│   Create Pull Request ──► Use PR_CHECKLIST.md                               │
│     │                                                                       │
│     ▼                                                                       │
│   Engineering Review                                                        │
│     │                                                                       │
│     ├──► Approved ──► Build Actions ──► K8S Deploy                          │
│     │                                                                       │
│     └──► Rejected ──► Fix issues ──► Re-submit PR                           │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

## Prerequisites

Before you begin, you need:

1. **GitHub Access** - Request from IT if you don't have it
2. **Docker Desktop Installed** - Required for local MongoDB database
   - [Download for Mac](https://www.docker.com/products/docker-desktop/)
   - [Download for Windows](https://www.docker.com/products/docker-desktop/)
3. **Claude Code Installed** - See [Claude Code Setup Guide](#claude-code-setup)
4. **Read This Entire Repository** - Especially STANDARDS.md and SECURITY.md
5. **Your App Idea Approved** - Brief conversation with your manager and Engineering lead

## Getting Started

### Step 1: Set Up Your Environment

```bash
# Install Claude Code (if not already installed)
npm install -g @anthropic-ai/claude-code

# Clone the standards repository
git clone https://github.com/wellnecity/development-standards.git

# Copy the CLAUDE.md template to your home directory
cp development-standards/templates/CLAUDE.md ~/.claude/CLAUDE.md
```

### Step 2: Create Your Project Repository

1. Go to GitHub and create a new repository under the `wellnecity` organization
2. Name it descriptively: `{department}-{what-it-does}` (e.g., `ops-inventory-tracker`)
3. Clone it locally and add the required files:

```bash
git clone https://github.com/wellnecity/your-project-name.git
cd your-project-name

# Copy the project template
cp ../development-standards/templates/PROJECT_README_TEMPLATE.md ./README.md
cp ../development-standards/templates/CLAUDE.md ./CLAUDE.md
```

### Step 3: Build Your Application

Open Claude Code in your project directory:

```bash
cd your-project-name
claude
```

Tell Claude what you want to build. Be specific about:
- What problem you're solving
- Who will use it
- What data it needs to track

**Claude Code will follow the standards automatically** because of the CLAUDE.md file in your project.

### Step 4: Submit for Review

1. Ensure your README answers all 4 required questions
2. Run through the PR_CHECKLIST.md
3. Create a Pull Request to the `main` branch
4. Tag `@wellnecity/engineering` for review

## The 4 Required README Questions

Every application README must clearly answer:

| Question | Why It Matters |
|----------|----------------|
| **What is it?** | Engineering needs to understand the purpose to assess risk and resource needs |
| **What is it built with?** | Must use approved languages, frameworks, and databases |
| **Should it be deployed?** | Some tools are local-only; others need infrastructure |
| **Is it internal-facing?** | External apps have stricter security requirements |

## Approval Timeline

| Scenario | Expected Timeline |
|----------|-------------------|
| Standards followed, simple app | 1-2 business days |
| Standards followed, needs discussion | 3-5 business days |
| Standards NOT followed | Rejected immediately—fix and resubmit |

## What Gets You Rejected

Instant rejection reasons:

- Using non-approved languages or frameworks
- Hardcoded secrets, passwords, or API keys
- No README or incomplete README
- No tests whatsoever
- Direct database connections without using approved patterns
- External-facing endpoints without authentication
- Ignoring the CLAUDE.md standards file

## Getting Help

- **Slack:** `#citizen-dev-help`
- **Office Hours:** Engineering holds weekly office hours (check calendar)
- **Documentation:** This repository + linked resources

## Repository Contents

| File | Purpose |
|------|---------|
| [README.md](README.md) | You are here |
| [STANDARDS.md](STANDARDS.md) | Technical requirements and rules |
| [SECURITY.md](SECURITY.md) | Security requirements (read this!) |
| [PR_CHECKLIST.md](PR_CHECKLIST.md) | Checklist before submitting |

### Templates (copy these to your project)

| File | Purpose |
|------|---------|
| [templates/CLAUDE.md](templates/CLAUDE.md) | Skills file for Claude Code - **required** |
| [templates/PROJECT_README_TEMPLATE.md](templates/PROJECT_README_TEMPLATE.md) | Template for your app's README |
| [templates/docker-compose.yml](templates/docker-compose.yml) | Local MongoDB setup |
| [templates/gitignore](templates/gitignore) | Copy to `.gitignore` in your project |
| [templates/env.example](templates/env.example) | Copy to `.env.example` in your project |

## Claude Code Setup

### Installation

```bash
# macOS/Linux
npm install -g @anthropic-ai/claude-code

# Verify installation
claude --version
```

### First-Time Configuration

```bash
# Start Claude Code to complete authentication
claude

# Follow the prompts to authenticate with your Anthropic account
```

### Using the Standards Skills File

The CLAUDE.md file tells Claude Code how to build applications the Wellnecity way. Always include it in your project:

```bash
# Project-level (recommended)
cp templates/CLAUDE.md ./CLAUDE.md

# Or global (applies to all your projects)
cp templates/CLAUDE.md ~/.claude/CLAUDE.md
```

## Frequently Asked Questions

**Q: Can I use Python instead of the approved stack?**
A: Only if it's on the approved list in STANDARDS.md. When in doubt, ask first.

**Q: My app doesn't need to be deployed—do I still need approval?**
A: If it touches company data or will be used by others, yes. Personal scripts are fine.

**Q: How do I handle sensitive data?**
A: Read SECURITY.md. Short answer: never hardcode it, use environment variables, and work with Engineering on data classification.

**Q: Can I use external APIs?**
A: Pre-approved APIs are listed in STANDARDS.md. Others require Engineering approval.

**Q: What if I need a database?**
A: Use one of the approved databases and connection patterns. See STANDARDS.md.

---

## How Standards Are Enforced

The Citizen Developer Program uses a layered approach to ensure quality:

| Layer | What It Does |
|-------|--------------|
| **CLAUDE.md Skills File** | Instructs Claude Code to follow Wellnecity patterns automatically |
| **PR Checklist** | Self-verification before submission |
| **Engineering Review** | Human verification against standards |
| **GitHub Actions** | Automated tests and security scans (coming soon) |
| **MCP Integration** | Programmatic enforcement during development (roadmap) |

### Roadmap: MCP (Model Context Protocol)

Future versions of this program will include an MCP server that provides real-time enforcement of standards during development. This will allow Claude Code to:

- Validate technology choices before building
- Check for security issues in real-time
- Verify project structure automatically
- Connect to approved databases and services

---

**Remember:** Following the standards isn't bureaucracy—it's the fast path to getting your app deployed. Engineering wants to say "yes" to well-built applications.

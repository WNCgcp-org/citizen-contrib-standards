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

### Step 1: Install Prerequisites

Before you begin, install these tools:

```bash
# Install Docker Desktop (required for local MongoDB)
# Download from: https://www.docker.com/products/docker-desktop/

# Install Claude Code
npm install -g @anthropic-ai/claude-code

# Verify installations
claude --version
docker --version
```

### Step 2: Clone This Standards Repository

```bash
# Clone the standards repository to your projects folder
git clone https://github.com/WNCgcp-org/citizen-contrib-standards.git

# Navigate into the repository
cd citizen-contrib-standards
```

### Step 3: Start Claude Code and Let It Read the Standards

This is the key step. Start Claude Code **inside the standards repository** and ask it to help you create your project:

```bash
# Make sure you're in the citizen-contrib-standards directory
cd citizen-contrib-standards

# Start Claude Code
claude
```

Then tell Claude:

```
I want to create a new project called [your-project-name] for [what it does].
Please read the standards in this repository, then help me set up a new
project repository that follows all the Wellnecity standards.
```

**Claude will:**
1. Read STANDARDS.md, SECURITY.md, and the templates
2. Create a new directory for your project
3. Set up the correct project structure
4. Copy and configure the required files (CLAUDE.md, docker-compose.yml, etc.)
5. Initialize git and help you push to GitHub

### Step 4: Build Your Application

Once your project is set up, Claude will continue helping you build. Be specific about:
- What problem you're solving
- Who will use it
- What data it needs to track

Claude will automatically follow the standards because it has read them.

### Step 5: Submit for Review

When your application is ready:

1. Ensure your README answers all 4 required questions
2. Run through the [PR_CHECKLIST.md](PR_CHECKLIST.md)
3. Push your code to GitHub under the `WNCgcp-org` organization
4. Create a Pull Request to the `main` branch
5. Tag `@WNCgcp-org/engineering` for review

### Example Session

```bash
$ cd citizen-contrib-standards
$ claude

You: I want to create a new project called "ops-equipment-tracker" to help
     the Operations team track equipment check-outs and returns. Please read
     the standards in this repository and set up my project.

Claude: I'll read the standards first, then set up your project...
        [Claude reads STANDARDS.md, SECURITY.md, templates/CLAUDE.md, etc.]

        I've created your project at ../ops-equipment-tracker with:
        - MongoDB + Mongoose configuration
        - Next.js with TypeScript
        - docker-compose.yml for local development
        - All required template files

        Let's start Docker and begin building. What features do you need?
```

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

- **Teams:** `Citizen Developer Help` channel
- **Principals:** Jonathan Rodriguez, Derrick Woolworth, Will Queen
- **Office Hours:** Engineering holds weekly office hours (check calendar)
- **Documentation:** This repository + [Wellnecity Style Guide](https://github.com/WNCgcp-org/style-guide)

## Repository Contents

| File | Purpose |
|------|---------|
| [README.md](README.md) | You are here |
| [STANDARDS.md](STANDARDS.md) | Technical requirements and rules |
| [SECURITY.md](SECURITY.md) | Security requirements (read this!) |
| [PR_CHECKLIST.md](PR_CHECKLIST.md) | Checklist before submitting |

### Related Repositories

| Repository | Purpose |
|------------|---------|
| [WNCgcp-org/style-guide](https://github.com/WNCgcp-org/style-guide) | **Wellnecity Style Guide** - Colors, typography, components, CSS/Tailwind config |

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

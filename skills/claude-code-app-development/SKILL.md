---
name: claude-code-app-development
description: Use this skill for application development with Claude Code, Spec-Driven Development (SDD), TypeScript+Bun stack (React, TailwindCSS, Elysia.js, Prisma), AWS CodeCommit, AWS CDK test environments, and Playwright MCP testing. Delivers a working test environment with URL and test accounts. Triggers when users ask about building applications, spec-kit workflow, Bun/React/Elysia development, AWS CodeCommit, AWS CDK/serverless, or Playwright testing. AI proceeds autonomously except during /speckit.clarify when user clarification is required. This is a long-running skill - execute via OpenClaw or subagent without timeout.
---

# Claude Code Application Development

CLI tool for autonomous application development using **Spec-Driven Development (SDD)** with **Spec Kit**, TypeScript + Bun stack, **AWS CodeCommit** for version control, **Playwright MCP** testing, and **AWS CDK** test environments.

**Goal:** Deliver a working test environment with URL and test accounts for user testing.

**Execution:** Long-running task - run via OpenClaw or subagent in background. No timeout. Continue until completion.

## Tech Stack

| Layer | Technology |
|-------|------------|
| **Runtime** | Bun |
| **Language** | TypeScript |
| **Frontend** | React + TailwindCSS + **Anysystem Design** |
| **Backend** | Elysia.js |
| **ORM** | Prisma |
| **Database** | PostgreSQL / SQLite |

## Frontend Components: Anysystem Design

**Use Anysystem Design** (`anysystem-design`) for all UI components.
Reference: https://github.com/david-marstree/anysystem

**Available Components:**
- Form: Input, PasswordInput, Selectbox, Checkbox, RadioGroup, DatePicker, AutoComplete
- Layout: Container, Row, Column, SideMenuLayout, EmptyLayout
- Navigation: Navbar, NavList
- Data: DataTable, Text
- Interactive: Button, Modal

**If component not available in Anysystem:**
- Build custom components following Anysystem design patterns
- Use same Tailwind CSS utility classes
- Match the visual style and interactions

## Quick Start

```bash
# 1. Install specify CLI
uv tool install specify-cli --from git+https://github.com/github/spec-kit.git

# 2. Create AWS CodeCommit repository
aws codecommit create-repository --repository-name <PROJECT_NAME> --repository-description "Project <PROJECT_NAME>"

# 3. Initialize project with CodeCommit remote
specify init <PROJECT_NAME> --ai claude --no-git
cd <PROJECT_NAME>
git init
git remote add origin https://git-codecommit.us-east-1.amazonaws.com/v1/repos/<PROJECT_NAME>
git add . && git commit -m "Initial commit"
git push -u origin main

# 4. Follow SDD workflow
/speckit.constitution → /speckit.specify → /speckit.clarify → /speckit.plan → /speckit.tasks → /speckit.implement
```

## Bun Commands

```bash
bun install           # Install dependencies
bun run dev           # Run all apps
bun run dev:web       # Frontend only
bun run dev:api        # Backend only
bunx prisma generate  # Generate Prisma client
bunx prisma db push   # Push schema to DB
bun run test:e2e      # Playwright E2E tests
```

## SDD + Testing Workflow

```
┌─────────────────────────────────────────────────────────────────┐
│  0. Setup        →  Create CodeCommit repo + git init          │
│  1. Initialize   →  specify init --ai claude                   │
│  2. Constitution →  /speckit.constitution                      │
│  3. Specify      →  /speckit.specify                           │
│  4. Clarify      →  /speckit.clarify (REQUIRED)                │
│  5. Plan         →  /speckit.plan                              │
│  6. Tasks        →  /speckit.tasks                             │
│  7. Implement    →  /speckit.implement                         │
├─────────────────────────────────────────────────────────────────┤
│  8. Test         →  Playwright MCP tools                        │
│           ↓                                                     │
│  [BUG] → 9. Fix  →  Claude Code Edit/Read tools                │
│           ↓                                                     │
│        Retest → [PASS] → Test Environment Ready                 │
└─────────────────────────────────────────────────────────────────┘

FINAL DELIVERABLE: Working test environment URL + test accounts (if auth required)
```

## Core Commands

| Command | Purpose |
|---------|---------|
| `/speckit.constitution` | Create project principles |
| `/speckit.specify` | Define requirements (what & why) |
| `/speckit.plan` | Technical architecture (how) |
| `/speckit.tasks` | Generate task list |
| `/speckit.implement` | Execute all tasks |

## Testing Tools (Playwright MCP)

| Tool | Purpose |
|------|---------|
| `mcp__chrome-devtools__navigate_page` | Navigate to URL |
| `mcp__chrome-devtools__take_snapshot` | Capture page structure |
| `mcp__chrome-devtools__click` | Click elements |
| `mcp__chrome-devtools__fill` | Fill form inputs |
| `mcp__chrome-devtools__get_console_message` | Check JS errors |
| `mcp__chrome-devtools__take_screenshot` | Visual evidence |

## Test Environment (AWS CDK + Serverless)

### CDK Commands

```bash
cdk synth          # Preview CloudFormation
cdk deploy         # Deploy to AWS
cdk watch          # Auto-deploy on changes
cdk destroy        # Tear down test environment
cdk bootstrap      # First-time setup
```

### Serverless Architecture (Cost-Optimized)

| Service | Purpose | Cost Tip |
|---------|---------|----------|
| **Lambda** | Compute | 1M free req/month, pay-per-invoke |
| **API Gateway** | REST API | $1/million requests |
| **DynamoDB** | Database | On-demand mode |
| **S3** | Storage | $0.023/GB |
| **EventBridge** | Events | $1/million events |
| **Cognito** | Auth | 50K MAU free tier |

### Test Environment Workflow

```
┌──────────────────────────────────────────────────────┐
│  TEST ENVIRONMENT SETUP                               │
├──────────────────────────────────────────────────────┤
│  1. Create CDK stack (Lambda + API Gateway)          │
│  2. cdk synth → Preview infrastructure              │
│  3. cdk deploy → Deploy to AWS                       │
│  4. Get API URL from outputs                         │
│  5. Run Playwright tests against test API            │
│  6. cdk destroy → Clean up (when done)              │
└──────────────────────────────────────────────────────┘
```

### CDK Project Structure

```
infra/
├── bin/infra.ts          # Entry point
├── lib/test-stack.ts      # Test stack
├── lambdas/               # Lambda functions
└── cdk.json
```

## Bug Fix Flow

1. Analyze test failure / console errors
2. Read source code with Claude Code `Read`
3. Fix with Claude Code `Edit`
4. Retest with Playwright MCP

## User Confirmation Rules

**AI proceeds autonomously** for most decisions:
- Code implementation choices (unless constitutional principle violated)
- File creation and modification
- Test execution and debugging
- Routine development tasks

**AI MUST ask user** when clarification is needed:
- During `/speckit.clarify` phase - user requirements are unclear or ambiguous
- Spec details that affect core functionality
- Architecture decisions with significant trade-offs
- Edge cases not covered in spec
- Any scenario where guessing could lead to wrong outcome

**Clarify before proceeding** when:
- Requirement meaning is unclear → ask user to clarify
- Multiple valid approaches exist → explain options and ask preference
- Missing information in spec → request clarification
- User intent is ambiguous → seek confirmation

## When to Use

- Build web applications (frontend/backend)
- Create CLI tools, APIs, microservices
- Start projects with structured SDD methodology
- Modernize legacy systems iteratively
- Automated UI testing with Playwright MCP

## Deliverables

**Upon completion, deliver to user:**

1. **Test Environment URL**
   - Local development: `http://localhost:3000` (web) + `http://localhost:3001` (api)
   - AWS CDK: Deployed API Gateway URL

2. **Test Accounts** (if authentication required)
   ```
   | Email                    | Password | Role      |
   |--------------------------|----------|-----------|
   | test@example.com         | Test123! | admin     |
   | user@example.com         | Test123! | user      |
   ```

3. **Test Credentials Document**
   - Store in `specs/<feature>/test-accounts.md`
   - Include all pre-seeded test data

4. **Quick Test Guide**
   - List of key user flows to test
   - Expected behavior for each flow

## References

For detailed content, see:
- `references/tech-stack.md` - Bun + React + Elysia.js + Prisma stack
- `references/integration.md` - Auth, CORS, shared types, seeding
- `references/spec-kit-workflow.md` - Complete SDD workflow
- `references/claude-capabilities.md` - Claude Code capabilities
- `references/testing.md` - Playwright MCP testing guide
- `references/project-structure.md` - Project structure
- `references/aws-cdk.md` - AWS CDK commands & serverless architecture
- `references/codecommit.md` - AWS CodeCommit setup & git workflow

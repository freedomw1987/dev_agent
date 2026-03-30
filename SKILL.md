---
id: SKILL
aliases: []
tags: []
description: "Use this skill for application development with Claude Code, Spec-Driven Development (SDD), TypeScript+Bun stack (React, TailwindCSS, Elysia.js, Prisma), AWS CodeCommit, AWS CDK test environments, and Playwright MCP testing. STRICT SDD ENFORCEMENT: Must follow the full /speckit workflow in order — constitution → specify → clarify → plan → tasks → implement. NO step skipping. Execute via subagent — run silently in background, notify user ONLY upon completion with deliverables (URL + test accounts). Triggers when users ask about building applications, spec-kit workflow, Bun/React/Elysia development, AWS CodeCommit, AWS CDK/serverless, or Playwright testing. AI proceeds autonomously except during /speckit.clarify when user clarification is required. This is a long-running skill - execute via OpenClaw or subagent without timeout."
name: dev_agent
---

# Claude Code Application Development

**Spec-Driven Development (SDD)** with TypeScript + Bun stack, Playwright MCP testing, and AWS CDK test environments.

**Goal:** Deliver a working test environment with URL and test accounts.
**Execution:** Run via subagent silently. Notify user ONLY upon completion.

## SDD Workflow (STRICT - NO SKIPPING)

```
Setup → /speckit.constitution → /speckit.specify → /speckit.clarify →
/speckit.plan → /speckit.tasks → /speckit.implement → Test → Deliver
```

| Command | Purpose |
|---------|---------|
| `/speckit.constitution` | Create project principles |
| `/speckit.specify` | Define requirements (what & why) |
| `/speckit.clarify` | **REQUIRED** - Clarify ambiguous requirements |
| `/speckit.plan` | Technical architecture (how) |
| `/speckit.tasks` | Generate task list |
| `/speckit.implement` | Execute all tasks |

**AI proceeds autonomously** except during `/speckit.clarify` when user clarification is needed.

## Testing Feedback Loop

```
Implement → Playwright MCP Test → [FAIL] → Fix → Retest
                                       ↓
                                     [PASS] → Deliver
```

**Playwright MCP Tools:** `navigate_page`, `take_snapshot`, `click`, `fill`, `get_console_message`, `take_screenshot`

## Quick Start

```bash
# 1. Install specify CLI
uv tool install specify-cli --from git+https://github.com/github/spec-kit.git

# 2. Install agent skills
bunx skills add elysiajs/skills      # Elysia.js API development
bunx skills add david-marstree/anysystem  # Anysystem Design UI
bunx skills add prisma/skills        # Prisma ORM

# 3. Create CodeCommit repository
aws codecommit create-repository --repository-name <PROJECT_NAME> --repository-description "Project <PROJECT_NAME>"

# 4. Initialize project
specify init <PROJECT_NAME> --ai claude --no-git
cd <PROJECT_NAME>
git init
git remote add origin https://git-codecommit.us-east-1.amazonaws.com/v1/repos/<PROJECT_NAME>
git add . && git commit -m "Initial commit" && git push -u origin main

# 5. Run development
bun install && bun run dev    # web: localhost:3000, api: localhost:3001
bun run test:e2e             # Playwright E2E tests
```

## Available Agent Skills

Invoke via `/<skill-name>` or `Skill` tool:

| Skill | Purpose |
|-------|---------|
| `elysiajs/skills` | Elysia.js API development |
| `david-marstree/anysystem` | Anysystem Design UI components |
| `prisma/skills` | Prisma ORM |
| `speckit` workflow | SDD commands (constitution, specify, clarify, plan, tasks, implement) |
| `playwright-mcp` | Playwright MCP testing tools |
| `aws-cdk` | AWS CDK test environments |

## Deliverables

1. **Test Environment URL** — Local: `localhost:3000/3001` / AWS: API Gateway URL
2. **Test Accounts** — `test@example.com / Test123!` (admin), `user@example.com / Test123!` (user)
3. **Quick Test Guide** — Key user flows and expected behavior

## References

Detailed guides: `references/tech-stack.md`, `references/spec-kit-workflow.md`, `references/testing.md`, `references/aws-cdk.md`, `references/codecommit.md`, `references/integration.md`, `references/project-structure.md`, `references/claude-capabilities.md`

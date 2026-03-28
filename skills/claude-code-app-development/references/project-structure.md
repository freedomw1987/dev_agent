# Project Structure Reference

## After `specify init` Project Structure

```
.
├── .specify/
│   ├── memory/
│   │   └── constitution.md      # Project principles
│   ├── scripts/
│   │   ├── check-prerequisites.sh
│   │   ├── common.sh
│   │   ├── create-new-feature.sh
│   │   ├── setup-plan.sh
│   │   └── update-claude-md.sh
│   ├── specs/
│   │   └── 001-feature-name/
│   │       ├── spec.md         # Requirements & user stories
│   │       ├── plan.md         # Technical implementation plan
│   │       ├── tasks.md        # Implementation task list
│   │       ├── contracts/      # API specifications
│   │       ├── data-model.md   # Data model
│   │       ├── research.md      # Tech stack research
│   │       └── quickstart.md   # Setup instructions
│   └── templates/
│       ├── spec-template.md
│       ├── plan-template.md
│       └── tasks-template.md
├── .claude/
│   └── commands/               # Slash commands for SDD
│       ├── speckit-constitution.md
│       ├── speckit-specify.md
│       ├── speckit-plan.md
│       ├── speckit-tasks.md
│       └── speckit-implement.md
└── CLAUDE.md                   # Project context for AI
```

## Feature Branch Structure

```
001-feature-name/    # Feature branch name
002-another-feature/ # Next feature
```

## Test Directory Structure

```
.specify/specs/001-feature-name/
├── spec.md           # Requirements
├── plan.md           # Tech plan
├── tasks.md          # Tasks
└── test/
    └── e2e/          # Playwright tests
        ├── login.spec.ts
        └── dashboard.spec.ts
```

## Key Files

| File | Purpose |
|------|---------|
| `constitution.md` | Project principles guiding all decisions |
| `spec.md` | Requirements and user stories |
| `plan.md` | Technical architecture and implementation details |
| `tasks.md` | Actionable implementation task list |
| `CLAUDE.md` | Project context for Claude Code sessions |

## Development Workflow

```
┌─────────────────────────────────────────────────────────┐
│ SPEC-DRIVEN DEVELOPMENT                                │
├─────────────────────────────────────────────────────────┤
│ 1. specify init --ai claude                            │
│    ↓                                                    │
│ 2. /speckit.constitution (create principles)           │
│    ↓                                                    │
│ 3. /speckit.specify (define requirements)              │
│    ↓                                                    │
│ 4. /speckit.clarify (clarify if needed)                │
│    ↓                                                    │
│ 5. /speckit.plan (technical architecture)              │
│    ↓                                                    │
│ 6. /speckit.tasks (break into tasks)                    │
│    ↓                                                    │
│ 7. /speckit.implement (build feature)                   │
│    ↓                                                    │
│ 8. Playwright MCP tests (verify functionality)         │
│    ↓                                                    │
│ 9. Claude Code bug fixes (if needed)                   │
└─────────────────────────────────────────────────────────┘
```

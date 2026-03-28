# Spec Kit Workflow Reference

Spec Kit provides structured slash commands for Spec-Driven Development (SDD).

## Core Commands

| Command | Purpose |
|---------|---------|
| `/speckit.constitution` | Create project governing principles and development guidelines |
| `/speckit.specify` | Define what to build (requirements and user stories) |
| `/speckit.plan` | Create technical implementation plans with chosen tech stack |
| `/speckit.tasks` | Generate actionable task lists for implementation |
| `/speckit.implement` | Execute all tasks to build the feature |

## Optional Commands

| Command | Purpose |
|---------|---------|
| `/speckit.clarify` | Clarify underspecified areas (recommended before plan) |
| `/speckit.analyze` | Cross-artifact consistency analysis |
| `/speckit.checklist` | Generate quality checklists |

## Development Phases

### Phase 1: Initialize Project

```bash
# Install specify CLI
uv tool install specify-cli --from git+https://github.com/github/spec-kit.git

# Initialize new project with Claude Code
specify init <PROJECT_NAME> --ai claude

# Or initialize in existing directory
specify init . --ai claude
```

### Phase 2: Constitution

Create project principles:

```
/speckit.constitution Create principles focused on code quality, testing standards, user experience consistency, and performance requirements
```

### Phase 3: Specification

Define requirements (focus on WHAT and WHY, not tech stack):

```
/speckit.specify Build an application that [describe requirements]
```

### Phase 4: Clarify

Clarify underspecified areas before planning:

```
/speckit.clarify
```

### Phase 5: Technical Plan

Specify tech stack and architecture:

```
/speckit.plan The application uses [tech stack details]
```

### Phase 6: Task Breakdown

```
/speckit.tasks
```

### Phase 7: Implementation

```
/speckit.implement
```

## Key Philosophy

**Spec-Driven Development** flips traditional development:
- Specifications become **executable**
- Focus on **what** and **why** before **how**
- Multi-step refinement vs one-shot generation

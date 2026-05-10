---
name: bootstrap-workflow
description: Phase 4 of project bootstrap. Set up GitHub project board, configure branch protection, and seed the initial backlog from PRD features (with all 4 Power Sections populated). Final bootstrap phase before development begins.
---

# Bootstrap Phase 4: Workflow Activation

Set up GitHub project board, branch protection, and create initial backlog from PRD features.

**Prerequisites**:
- Phase 1 (Product Definition) complete
- Phase 2 (Technical Architecture) complete
- Phase 3 (Agent Specialization) complete

This is the final bootstrap phase. It activates the full agent workflow.

## Ticket Format Requirement

Before creating any issues, read `docs/TICKET-FORMAT.md` in full. Every issue
created in this phase — epics and features alike — MUST follow the Agentic PRD
Lite format. Tickets without the 4 Power Sections (A. Environment Context,
B. Guardrails, C. Happy Path, D. Definition of Done) will not pass grooming
and will have to be rewritten.

## What This Phase Does

### 1. GitHub Project Board Setup

Verify or create project board with columns:
- **Icebox** — Ideas not yet prioritized
- **Backlog** — Prioritized but not ready
- **Ready** — Well-defined, ready to work (2-5 items)
- **In Progress** — Currently being worked
- **In Review** — PR created, awaiting review
- **Done** — Merged and complete

### 2. Branch Protection Configuration

Verify or configure branch protection on `main`:
- [ ] Require pull request reviews before merging
- [ ] Require status checks to pass (if CI configured)
- [ ] Do not allow bypassing the above settings

### 3. Initial Backlog Creation

Convert PRD features into GitHub issues following `docs/TICKET-FORMAT.md`:
- Create epics for major feature areas (epics use Problem Statement + high-level scope)
- Create feature issues with ALL required fields:
  - Problem Statement, Parent Epic, Effort Estimate, Priority
  - A. Environment Context (from `docs/TECHNICAL-ARCHITECTURE.md`)
  - B. Guardrails (from `docs/AGENTIC-CONTROLS.md` + PRD constraints)
  - C. Happy Path (numbered steps: Input → Logic → Output)
  - D. Definition of Done (specific test assertions, lint commands, reviewer checks)
- Link issues to epics
- Add priority labels (P0/P1/P2/P3)

### 4. Ready Column Population

Move the highest-priority, well-defined tickets to Ready:
- Select 3-5 tickets for initial Ready column
- Ensure they meet Definition of Ready
- Add technical guidance and acceptance criteria

### 5. Project Configuration Finalization

Update project-level instructions with:
- Project board URL
- Repository URL
- Team/org information
- Any final configuration

## Pre-Flight Checklist

Before running this phase, ensure you have:
- [ ] GitHub repository created
- [ ] GitHub personal access token with repo, project, and workflow permissions
- [ ] Permission to create project boards
- [ ] Permission to configure branch protection

## Pre-Flight Verification (REQUIRED)

Before any board or ticket operations, verify the following. STOP and report
to the user if any check fails — do not continue with partial tooling.

1. **GitHub access works** — Try a `gh` call. If it fails, STOP.
2. **GitHub account is correct** — Run `gh auth status` and confirm the active
   account matches the expected worker/bot account.
3. **Project board is accessible** — Attempt to read the project board. If
   access is denied or the board does not exist, STOP and report.

## Configuration Required

You'll be asked to provide:

```
GitHub Organization: [your-org]
Repository Name: [your-repo]
Project Board Name: [your-project-name]
```

## Process

1. **Verify GitHub Access** — Test token permissions, confirm org/repo access
2. **Create/Verify Project Board** — Check if board exists, create columns if needed
3. **Configure Branch Protection** — Check current settings, apply protection rules
4. **Generate Backlog** — Read all source docs, create epic and feature issues with full Power Sections, set priorities. Self-check: before creating each issue, verify it contains sections A through D.
5. **Populate Ready Column** — Select MVP tickets, ensure Definition of Ready met
6. **Update Configuration** — Add URLs to project instructions

## Example Backlog Generation

> Every issue MUST follow `docs/TICKET-FORMAT.md`. The example below shows the
> expected structure. Do NOT create bare-title issues without Power Sections.

From a PRD feature like:
```markdown
### MVP Features
- User authentication (email/password)
```

Create an epic:
```
Epic: User Authentication

Problem Statement:
The application has no way to identify users. All routes are public.
We need email/password authentication to gate access to user-specific data.

Scope: signup, login, password reset, session management.
Priority: P0
```

Then create feature issues with full Power Sections:
```
TICKET: Implement email/password signup

Problem Statement:
New users cannot create accounts. We need a signup endpoint that accepts
email + password, validates input, and creates a user record.

Parent Epic: #<epic-number>
Effort Estimate: M
Priority: P0

--- A. Environment Context ---
- Stack: (from TECHNICAL-ARCHITECTURE.md)
- Existing pattern: (reference a similar route in the codebase)
- Files to create/modify: (list explicitly)

--- B. Guardrails ---
- (from AGENTIC-CONTROLS.md + PRD constraints)
- Do NOT store plaintext passwords
- Do NOT modify existing auth middleware

--- C. Happy Path ---
1. Client sends POST /auth/signup with {email, password}
2. Server validates email format and password strength
3. Server hashes password, creates user record
4. Server returns 201 with {id, email}

--- D. Definition of Done ---
- Test asserts POST /auth/signup with valid data returns 201
- Test asserts duplicate email returns 409
- Test asserts weak password returns 422
- Lint and type checks pass with zero errors
- PR reviewer can run the signup flow manually
```

## What Gets Unlocked

After Phase 4, the full workflow is active:
- `groom-backlog` skill works with the project board
- `work-ticket` picks up tickets from the Ready column
- `review-pr` reviews PRs in the repository
- `sprint-status` shows the board status

## Verification

After this phase, verify the workflow:

1. **Check Project Board** — Visit the GitHub project board URL, verify columns and issues exist
2. **Check Branch Protection** — Verify `main` is protected
3. **Test Workflow** — Run the `sprint-status` skill; should show your board status

## Troubleshooting

**"GitHub token not authorized"** — Ensure token has `repo`, `project`, and `workflow` scopes
**"Cannot create project board"** — Verify org permissions
**"Branch protection failed"** — Verify admin access to repo
**"Issues not appearing on board"** — Check issue labels match board filters

### Output Format

Report each phase with a Progress Line, then end with a Result Block:

```
-> Configured GitHub project board
-> Set up branch protection rules
-> Generated backlog from PRD (12 issues)
-> Populated Ready column (4 tickets)

---

**Result:** Workflow setup complete
Project board: configured
Issues created: 12
Ready column: 4 tickets
Status: ready for development
```

<!-- Source: Agile Flow (https://github.com/vibeacademy/agile-flow) -->
<!-- SPDX-License-Identifier: BUSL-1.1 -->

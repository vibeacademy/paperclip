---
name: lock-scope
description: Formally lock MVP scope before development begins. Walks through a 6-question checklist and produces SCOPE-LOCK.md. Use to transition from product planning to execution.
---

# Lock Scope

Formalize the transition from product planning to execution by verifying and documenting that MVP scope is locked.

## Pre-Check

Before proceeding, verify these documents exist:
- `docs/PRODUCT-REQUIREMENTS.md` (from `bootstrap-product`)
- `docs/TECHNICAL-ARCHITECTURE.md` (from `bootstrap-architecture`)

If missing, inform the user which bootstrap phases need to be completed first.

---

## Scope Lock Checklist

Ask the user to confirm each criterion ONE AT A TIME:

### Question 1: Feature List
```
Is your MVP feature list finalized?

1. Yes - I can list exactly what's in v1
2. Mostly - A few items still being decided
3. No - Still exploring options
```

If not "Yes", ask which features are still undecided.

### Question 2: Acceptance Criteria
```
Do all MVP features have acceptance criteria (testable "done" conditions)?

1. Yes - All features have clear acceptance criteria
2. Partial - Some features need criteria defined
3. No - Most features lack clear criteria
```

If not "Yes", ask which features need acceptance criteria defined.

### Question 3: Open Questions
```
Are major technical and product decisions resolved?

1. Yes - No blocking open questions
2. Mostly - A few non-blocking questions remain
3. No - Major decisions still pending
```

If not "Yes", ask what open questions remain.

### Question 4: Stakeholder Alignment
```
Are all stakeholders aligned on this scope?

1. Yes - Everyone agrees this is what we're building
2. Mostly - Minor disagreements exist
3. No - Significant misalignment
```

If not "Yes", ask what alignment issues exist.

### Question 5: Timeline
```
Do you have a target launch date or timeline?

1. Yes - Specific date: [please specify]
2. Roughly - General timeframe (e.g., "Q2", "3 months")
3. No - No timeline established
```

### Question 6: Change Process
```
How will scope changes be handled after lock?

1. Formal trade-off process (add something = cut something)
2. Review meeting required for any additions
3. No process defined yet
```

---

## Lock Decision

Based on responses:

- **LOCKED** — All criteria met (all "Yes" responses) → generate SCOPE-LOCK.md
- **CONDITIONAL LOCK** — Minor gaps (mostly "Yes" with some "Mostly") → document open items, proceed with lock but flag items
- **NOT READY** — Significant gaps (any "No" responses) → list what needs to be resolved, do not create lock document

---

## Output: SCOPE-LOCK.md

Generate the following document and save to `docs/SCOPE-LOCK.md`:

```markdown
# Scope Lock Document

**Lock Date:** [Today's date]
**Lock Status:** [LOCKED / CONDITIONAL]
**Target Launch:** [From Q5]

---

## MVP Scope

### Features In Scope

| Feature | Description | Acceptance Criteria | Priority |
|---------|-------------|---------------------|----------|
| [Feature 1] | [Brief description] | [Testable criteria] | P0 |
| [Feature 2] | [Brief description] | [Testable criteria] | P0 |
| [Feature 3] | [Brief description] | [Testable criteria] | P1 |

### Explicitly Out of Scope (v1)

- [Feature A] - Reason: [Why deferred]
- [Feature B] - Reason: [Why deferred]

---

## Open Items

[If CONDITIONAL lock]

| Item | Type | Owner | Due Date |
|------|------|-------|----------|
| [Open question 1] | Decision | [Who] | [Date] |
| [Missing criteria] | Definition | [Who] | [Date] |

---

## Change Control Process

[From Q6]

**To add scope after lock:**
1. [Step 1]
2. [Step 2]
3. [Step 3]

**Authorized approvers:**
- [Role/Name]

---

## Stakeholder Sign-Off

| Role | Status | Notes |
|------|--------|-------|
| Product | [Aligned/Pending] | |
| Engineering | [Aligned/Pending] | |
| Design | [Aligned/Pending] | |
| Marketing | [Aligned/Pending] | |

---

## Timeline

**Target Launch:** [Date/Timeframe]

| Milestone | Target Date |
|-----------|-------------|
| Scope Lock | [Today] |
| Dev Midpoint | [Date] |
| Feature Complete | [Date] |
| Launch | [Date] |

---

## What This Lock Means

By locking scope, we commit to:

1. **Building exactly this** - The features listed above, no more, no less
2. **Predictable timeline** - Dates are based on this defined scope
3. **Visible trade-offs** - Any additions require cutting something else
4. **Marketing can plan** - GTM strategy can be built against this target

This document is the contract between Product, Engineering, and Marketing.
```

### Output Format

End your output with a Result Block:

```
---

**Result:** Scope locked
Status: LOCKED
Target launch: Q2 2025
Features in scope: 12
Open items: 0
Document: docs/SCOPE-LOCK.md
```

<!-- Source: Agile Flow (https://github.com/vibeacademy/agile-flow) -->
<!-- SPDX-License-Identifier: BUSL-1.1 -->

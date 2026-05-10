---
name: bootstrap-product
description: Phase 1 of project bootstrap. Walk the user through a structured 25-question questionnaire to produce docs/PRODUCT-REQUIREMENTS.md and docs/PRODUCT-ROADMAP.md. Pre-fills from research artifacts if they exist.
---

# Bootstrap Phase 1: Product Definition

Guide the user through a structured questionnaire to define their product. Ask questions ONE AT A TIME and wait for responses before proceeding.

## Instructions

**IMPORTANT: Follow this exact question sequence. Do not skip or combine questions.**

For multiple-choice questions, present numbered options and ask the user to respond with the number OR type their own answer.

---

## Pre-Check: Research Artifacts

Before starting the questionnaire, check for these files:
- `docs/MARKET-RESEARCH.md`
- `docs/JOBS-TO-BE-DONE.md`
- `docs/POSITIONING-ANALYSIS.md`

**If any are found**, tell the user which artifacts were found. Example:
> "I found 2 research artifacts: Market Research and JTBD Analysis. I'll pre-populate several questions from these — you can confirm or override each one."

**Pre-population mapping** — for each question below, if the source artifact exists, show the pre-filled answer and ask "Is this correct? (yes to confirm, or type to override)":

| Question | Source Artifact | Section |
|----------|----------------|---------|
| Q0.1 (domain) | MARKET-RESEARCH.md | Product Concept |
| Q0.2 (value proposition) | POSITIONING-ANALYSIS.md | Positioning Statement |
| Q2.1 (problem) | JOBS-TO-BE-DONE.md | Core Job Statement + Pain Points |
| Q2.2 (who has problem) | POSITIONING-ANALYSIS.md | Best-Fit Customer |
| Q2.3 (current solutions) | POSITIONING-ANALYSIS.md | Competitive Alternatives |
| Q3.1 (primary user) | POSITIONING-ANALYSIS.md | Best-Fit Customer |
| Q3.2 (pain point) | JOBS-TO-BE-DONE.md | Underserved Needs |
| Q3.3 (secondary users) | MARKET-RESEARCH.md | Target Audience Insights |
| Q6.1 (competitors) | MARKET-RESEARCH.md | Competitor Analysis |
| Q6.2 (differentiator) | POSITIONING-ANALYSIS.md | Unique Attributes |

**If no artifacts are found**, proceed normally. Mention that running `research`, `jtbd`, and `positioning` first would produce higher-quality results, but they are not required.

---

## Questionnaire

### Section 0: Your Domain

**Q0.1** — What is your product's domain? Describe it in one sentence.
**Q0.2** — What is your core value proposition in one sentence?

Use the domain and value proposition throughout the generated PRD and Roadmap to make them specific to the founder's product, not generic template content.

### Section 1: Product Type & Category

**Q1.1** — What type of product are you building? (Web / Mobile / Desktop / API / CLI / Library / Hardware / Other)
**Q1.2** — What category? (B2B SaaS / B2C / Dev tools / E-commerce / Content / Productivity / Finance / Healthcare / Education / Other)

### Section 2: Problem & Vision

**Q2.1** — Describe the problem your product solves in 1-3 sentences.
**Q2.2** — Who experiences this problem most acutely? (Consumers / Small biz / Mid-market / Enterprise / Devs / Profession / Other)
**Q2.3** — How do people currently solve this today? (Manual / Inadequate software / Competitors / Live with pain / Workarounds / Other)

### Section 3: Target Users

**Q3.1** — Describe your primary user in one sentence (role, context, goal).
**Q3.2** — What is the #1 pain point for this user?
**Q3.3** — Will there be secondary user types? (One type / Admin role / Multiple distinct types / Describe)

### Section 4: Core Features (MVP)

**Q4.1** — List 3-5 features that MUST be in your MVP. Be specific.
**Q4.2** — What features are explicitly OUT OF SCOPE for v1?
**Q4.3** — What's the ONE thing your product must do exceptionally well?

### Section 5: Success Metrics

**Q5.1** — How will you measure success? (Signups / DAU/MAU / Revenue / Retention / Completion / Time saved / NPS / Other)
**Q5.2** — What's your target for this metric in the first 3 months post-launch?

### Section 6: Competitive Landscape

**Q6.1** — Who are your main competitors or alternatives? (List 1-3, or "No direct competitors")
**Q6.2** — What's your key differentiator?

### Section 7: Timeline & Constraints

**Q7.1** — When do you need to launch? (ASAP / 1mo / 2-3mo / 3-6mo / 6+mo / No deadline)
**Q7.2** — What are your biggest constraints? (Time / Budget / Technical / Regulatory / Team / Multiple / None)
**Q7.3** — Any technical requirements or preferences?

---

## After Collecting All Responses

Once all questions are answered, synthesize the responses into two documents:

1. **docs/PRODUCT-REQUIREMENTS.md** — Structured PRD
2. **docs/PRODUCT-ROADMAP.md** — Phased roadmap

Present a summary to the user and confirm before writing files.

## Output Templates

### docs/PRODUCT-REQUIREMENTS.md

```markdown
# Product Requirements Document

## Product Overview
- **Type**: [From Q1.1]
- **Category**: [From Q1.2]

## Vision & Problem Statement
[Synthesized from Q2.1]

## Target Audience
- **Primary**: [From Q2.2]
- **User Description**: [From Q3.1]
- **Key Pain Point**: [From Q3.2]
- **Secondary Users**: [From Q3.3]

### Current Solutions
[From Q2.3]

## Features

### MVP (Must Have)
[From Q4.1]

### Out of Scope (v1)
[From Q4.2]

### Core Value Proposition
[From Q4.3]

## Success Metrics
- **Primary Metric**: [From Q5.1]
- **3-Month Target**: [From Q5.2]

## Competitive Analysis
- **Competitors**: [From Q6.1]
- **Differentiator**: [From Q6.2]

## Constraints & Requirements
- **Timeline**: [From Q7.1]
- **Constraints**: [From Q7.2]
- **Technical Requirements**: [From Q7.3]

## Research Foundation
{Include this section only if research artifacts were used}
- [Market Research](./MARKET-RESEARCH.md) {if available}
- [Jobs-to-be-Done Analysis](./JOBS-TO-BE-DONE.md) {if available}
- [Positioning Analysis](./POSITIONING-ANALYSIS.md) {if available}
```

### docs/PRODUCT-ROADMAP.md

```markdown
# Product Roadmap

## Overview
[Timeline summary based on Q7.1]

## Phase 1: MVP
- **Target**: [Based on Q7.1]
- **Goal**: Deliver core value proposition
- **Features**: [From Q4.1]
- **Success Criteria**: [From Q5.1 + Q5.2]

## Phase 2: Iteration
- **Target**: Post-MVP
- **Goal**: Expand based on feedback
- **Features**: TBD based on user feedback

## Constraints & Risks
[From Q7.2 and Q7.3]
```

## What Happens Next

After this phase:
1. Review the generated PRD and Roadmap
2. Use the `bootstrap-architecture` skill for Phase 2 (Technical Architecture)

### Output Format

End your output with a Result Block:

```
---

**Result:** Product definition complete
Documents: docs/PRODUCT-REQUIREMENTS.md, docs/PRODUCT-ROADMAP.md
Features defined: 8 (3 MVP, 5 future)
Next: bootstrap-architecture
```

<!-- Source: Agile Flow (https://github.com/vibeacademy/agile-flow) -->
<!-- SPDX-License-Identifier: BUSL-1.1 -->

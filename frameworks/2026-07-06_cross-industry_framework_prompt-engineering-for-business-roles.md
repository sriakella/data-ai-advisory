---
title: "Prompt Engineering Framework for Product Managers, Business Analysts, and Scrum Masters"
vertical: Cross-Industry
pattern_type: framework
maturity: published
tags: [prompt-engineering, product-management, business-analysis, scrum, decision-intelligence, ai-adoption]
published: 2026-07-06
---

# Prompt Engineering Framework for Product Managers, Business Analysts, and Scrum Masters

## Context — the situation this addresses

Most prompt engineering guidance targets engineers, architects, or AI specialists. Product Managers, Business Analysts, and Scrum Masters operate with different optimization functions:

- **PMs** optimize for outcomes, customer value, prioritization, and product strategy.
- **BAs** optimize for requirements clarity, process understanding, stakeholder alignment, and traceability.
- **Scrum Masters** optimize for team effectiveness, delivery flow, impediment removal, and continuous improvement.

Prompts for these roles should emphasize problem understanding before solution design, decision quality before technical depth, stakeholder alignment before implementation details, and business outcomes before technology choices.

## Position — the architectural take

### Core XML Prompt Template

Every prompt for business roles should follow this structure:

```xml
<role>
  Product Manager | Business Analyst | Scrum Master
</role>

<context>
  If context is omitted:
  - Infer from the conversation.
  - Infer from recent messages.
  - Ask questions only when critical information is missing.
</context>

<task>
  Describe the business objective.
</task>

<format>
  text | markdown | table | csv | json | roadmap | backlog | user stories
</format>

<thinking_modes>
  Optional reasoning approaches.
</thinking_modes>

<quality_constraints>
  Mandatory quality controls.
</quality_constraints>
```

### The Thinking Mode / Quality Constraint Distinction

This is the structural insight most practitioners miss:

**Thinking Modes** determine *"How should the AI reason?"* — they shape the reasoning path. **Quality Constraints** determine *"How trustworthy should the output be?"* — they shape output integrity. Conflating the two produces either ungrounded creativity or rigorous triviality.

### Role-Specific Thinking Mode Stacks

#### Product Manager Stack

| Mode | Function | Use Cases |
|------|----------|-----------|
| **Steelman** | Interprets customer requests and stakeholder proposals at their strongest; prevents strawman arguments, premature rejection, feature bias | PRDs, product strategy, executive reviews |
| **First Principles** | Reduces problems to fundamental user and business needs — *"What problem are we actually solving?"* | Discovery, innovation, product-market fit, requirement validation |
| **Decision Intelligence** | Focuses on decisions over information collection; outputs decision + alternatives + tradeoffs + recommendation | Prioritization, roadmap planning, go/no-go decisions |
| **L99** | Forces expert-level analysis including hidden assumptions, edge cases, failure scenarios, organizational implications, long-term effects | Strategic initiatives, platform decisions, transformation programs |

#### Business Analyst Stack

| Mode | Function | Use Cases |
|------|----------|-----------|
| **First Principles** | Ensures requirements originate from actual business needs | Requirements definition |
| **Socratic** | Drives clarification through structured questioning — *Who uses this? What triggers this process? What happens if data is missing? What defines success?* | Stakeholder interviews, requirements elicitation |
| **Critic** | Identifies ambiguity and gaps in requirements | Requirement reviews, acceptance criteria, process documentation |
| **Pareto** | Focuses on the few requirements generating most business value | MVP definition, requirement prioritization, scope management |

#### Scrum Master Stack

| Mode | Function | Use Cases |
|------|----------|-----------|
| **Systems Thinking** | Examines interactions between team, process, stakeholders, and dependencies | Sprint analysis, retrospectives |
| **Critic** | Highlights delivery risks, process bottlenecks, sprint anti-patterns | Delivery risk assessment |
| **Decision Intelligence** | Supports sprint tradeoffs, resource allocation, escalation decisions | Sprint planning, backlog refinement |
| **Pareto** | Finds major blockers, high-impact improvements, key delivery constraints | Continuous improvement |

### Universal Quality Constraints

These remain stable regardless of role:

- **Verifiable Foundations** — Distinguish facts from assumptions. Cite evidence where possible.
- **Calibrated Confidence** — State uncertainty. Avoid unwarranted certainty.
- **Auditable Logic Trails** — Make reasoning transparent. Surface assumptions.
- **Structured Output** — Organize information logically. Use sections and tables.
- **High-Entropy Synthesis** — Generate insights, not summaries. Connect information across domains.

## Tradeoffs — what this gains, what this gives up

**Gains:**

- Separating thinking modes from quality constraints prevents the common failure of rigorous but unimaginative outputs (all constraint, no reasoning) or creative but ungrounded outputs (all reasoning, no constraint).
- Role-specific mode stacks eliminate the trial-and-error cycle most non-technical practitioners face when prompting.
- The XML template is composable — modes and constraints can be mixed across roles for cross-functional scenarios.

**Gives up:**

- Does not address multi-turn conversation orchestration or agent-based workflows.
- Assumes a single-model interaction; does not cover prompt chaining across specialized models.
- The mode taxonomy is prescriptive — practitioners with domain-specific reasoning patterns may need to extend it.

## Application notes — where this fits, where it does not

**Where it fits:**

- Enterprise AI adoption programs where PMs, BAs, and Scrum Masters are the primary AI users, not engineers.
- Teams standardizing prompt practices across a product organization.
- Training and enablement curricula for non-technical AI adoption.

**Where it does not:**

- Engineering-heavy contexts where code generation, debugging, or architecture design is the primary use case.
- Agentic AI orchestration where prompts are system instructions, not user-facing templates.

## Worked Examples

### Product Manager — Evaluate Build Decision

```xml
<role>Senior Product Manager</role>
<task>Evaluate whether we should build an AI-powered customer support chatbot.</task>
<thinking_modes>Steelman, First Principles, Decision Intelligence, L99</thinking_modes>
<quality_constraints>Verifiable Foundations, Calibrated Confidence, Auditable Logic Trails</quality_constraints>
```

**Expected output structure:** Problem Definition → Customer Pain Points → Alternatives → Tradeoffs → Risks → Recommendation → Success Metrics.

### Business Analyst — Automation Requirements

```xml
<role>Business Analyst</role>
<task>Create requirements for customer onboarding automation.</task>
<thinking_modes>Socratic, First Principles, Critic</thinking_modes>
```

**Expected output structure:** Stakeholders → Current Process → Future Process → Functional Requirements → Non-Functional Requirements → Assumptions → Open Questions.

### Scrum Master — Velocity Diagnosis

```xml
<role>Scrum Master</role>
<task>Analyze why sprint velocity has declined for the last three sprints.</task>
<thinking_modes>Systems Thinking, Critic, Decision Intelligence</thinking_modes>
```

**Expected output structure:** Symptoms → Root Causes → Evidence → Risks → Improvement Actions → Success Indicators.

## References — public sources only, with URLs

- Anthropic prompt engineering documentation: [https://docs.claude.com/en/docs/build-with-claude/prompt-engineering/overview](https://docs.claude.com/en/docs/build-with-claude/prompt-engineering/overview)
- Prior artifact in this repo: [DI + Activation Substrata Reference Architecture](../bfs/2026-06-16_bfs_reference-architecture_di-activation-substrata.md) — the Decision Intelligence thinking mode draws from the same DI framework applied there.

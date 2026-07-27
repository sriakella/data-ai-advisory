# REVISED OBJECTIVES — PROPOSED ADDITIONS TO V4
## Status: PROPOSED — not applied to V4
## Scope: AI Agents Obj 1 + Prompt Engineering (8 obj) + Data 360 (4 obj) + Testing/Deployment (4 obj) + Governance (2 obj) + Multi-Agent Orchestration (2 obj)

---

## OBJECTIVE 1 — FUNDAMENTALS, AGENT SCRIPT & HYBRID REASONING

**Purpose:** Establish how the agent runtime works end-to-end and the Agent Script building blocks that control it — the foundation every other AI Agents objective depends on.

**Guide:** "Explain how an agent works and its basic building blocks of agent script" + "Explain the components and benefits of hybrid reasoning, including how Agent Script functions in Canvas and Script View."

This is now a **first-class objective** and the most under-drilled area — prioritize it.

### How an agent works
✓ **Agent** = the AI worker; at runtime the reasoning engine classifies the user message → selects a topic → selects/sequences actions → grounds → generates a response
✓ **Reasoning engine** = plans the workflow (what to do); the **LLM** generates language (how to say it) — two responsibilities, never conflated

### Next-Generation Authoring (NGA)
✓ **NGA** = the current agent authoring paradigm in Agent Builder
✓ **Agent Script** ⚠ = the underlying structure/definition of the agent's topics, actions, and behavior — the "code" beneath the agent
✓ **Canvas View** ⚠ = the visual, declarative authoring interface (drag/configure)
✓ **Script View** ⚠ = the code-level interface to read/manipulate the underlying Agent Script directly

**Canvas vs. Script View binding:**
```
Visual, declarative, business-user authoring   → Canvas View
Code-level, developer, direct Script edit      → Script View
Same underlying Agent Script — two views of it
```

### Hybrid Reasoning
✓ **Hybrid reasoning** ⚠ = the paradigm where **deterministic logic** (hard, enforced rails) and **probabilistic logic** (LLM generation) operate together
- **Benefit:** determinism guarantees required steps/guardrails; the LLM handles natural-language flexibility and ambiguity
- **Exam framing:** "architecture combining enforced rules with LLM generation" = hybrid reasoning

### Agent Script Execution Model ⚠ [VERSION_ALERT: SU26]

**Action invocation — two modes, one critical distinction:**
```
run @actions.ActionName     → DETERMINISTIC: forced execution every turn, no LLM choice
reasoning.actions:          → PROBABILISTIC: LLM decides whether/when to call the action
```
**Binding rule:** "must always execute" / "every turn" / "deterministic action" → `run`. "Agent should use when appropriate" / "let the LLM decide" → `reasoning.actions:`.
Exam-tested: Set 2 Q16 (differentiation), Q41 (effect of switching from reasoning.actions to run).

**`before_reasoning` block:**
⚠ Executes at the **start of the next turn after a transition** — NOT mid-turn, NOT during the current subagent's reasoning pass. If you transition to a subagent and expect `before_reasoning` to run immediately in that same turn, it won't — it fires on the NEXT user message.
Exam-tested: Set 2 Q10 (timing risk), Q22 (deterministic actions at subagent start), Q59 (before_reasoning + filters).

**`@utils` built-in utilities:**
⚠ `@utils.setVariables` = set context/custom variable values (no-code, no custom backend)
⚠ `@utils.transition` = move conversation to another subagent/topic
⚠ `@utils.escalate` = hand off to a human agent (HITL)
All three are built-in Agent Script utilities — NOT custom Apex, NOT Flow. The exam tests whether you know these exist as no-code primitives.
Exam-tested: Set 2 Q53.

**Action `description` field:**
⚠ The `description` property on an Agent Script action is what the LLM reads to decide **when** to use that action (in probabilistic/reasoning mode). Analogous to `classification description` for topics, but at the action level.
Exam-tested: Set 2 Q52.

**Operator syntax:**
⚠ `=` = assignment operator (set a variable's value)
⚠ `==` = comparison operator (evaluate equality in a condition)
Using `=` in a condition evaluates as assignment, not comparison — silent logic error.
Exam-tested: Set 2 Q15.

### The Agent (NGA) Lifecycle
✓ `Ideation → Building → Testing → Deployment → Observation`
**Do NOT confuse with the Prompt Template lifecycle** (Build → Preview → Activate → Invoke). "Preview" is a template word; agents use Testing Center.

**⚠ [VERIFY] before exam:** Agent Script syntax specifics, exact Canvas/Script View mechanics, and hybrid-reasoning internal wording sit at the training edge. The *concepts* (visual vs. code view; deterministic + probabilistic together) are exam-safe; exact syntax is not — primary-source the Spring '26 guide and Trailhead.

### Distractor bank
\# **LLM** as the planner — it generates language, it does not plan the workflow
\# **Prompt** as the agent structure — the prompt is assembled input, not the Agent Script
\# Prompt Template lifecycle terms ("Preview," "Activate/Invoke") mislabeled as the agent lifecycle
\# **`reasoning.actions:`** when the stem says "must always execute" — that's `run`, not reasoning
\# **Custom Apex / Flow** when the stem asks about `@utils` — @utils are built-in, no custom code

### Layer-Config-Symptom
| Layer | Config Items | Typical Symptom If Missing |
|-------|--------------|------------------------------|
| **Authoring** | NGA (Next-Generation Authoring), Canvas View, Script View, Agent Script Building Blocks, block sequencing/transitions | Agent starts but doesn't execute blocks — conversation ends early without action attempt, or wrong Canvas branch chosen |

---

## OBJECTIVE 2 — DETERMINISTIC BEHAVIOR (Filters, Variables, Template Expressions)

**Purpose:** Ensure the agent enforces hard business rules that the LLM cannot override — the guardrails that make hybrid reasoning trustworthy.

**Guide:** "manage deterministic behavior for the agent using mechanisms like filters, variables, and template expressions."

### The #1 exam distinction
- **Probabilistic:** Instructions / prompts — the model *interprets* and may deviate
- **Deterministic:** Filters, Variables, **Template Expressions** — the platform *enforces* regardless of the LLM

**Binding rule:** any stem with "**must always**," "**must never**," "**strictly enforce**," or "**regardless of conversation**" → a **deterministic** mechanism. Never Instructions.

### The three mechanisms
✓ **Variables** = typed runtime state carried across turns
- Context variables = system-populated · Custom variables = collected from the user
✓ **Filters** = hard conditional gates on topic/action eligibility (e.g., `User.Region == 'EMEA'`)
✓ **Template Expressions** ⚠ = logical formulas within Agent Script that evaluate variables and enforce runtime logic **before** the reasoning engine acts

**Three-way discrimination:**
```
Carry a runtime value across turns        → Variable
Gate whether a topic/action can run        → Filter
Evaluate a formula/expression deterministically → Template Expression
Guide the LLM's behavior (soft)            → Instructions (probabilistic — the trap)
```

### Deterministic Execution Mechanics ⚠ [VERSION_ALERT: SU26]

**`mutable` keyword:**
⚠ Controls whether a variable can be **reassigned** after initial set. If a variable is NOT mutable, its value is locked after first assignment — subsequent `@utils.setVariables` calls on it are silently ignored. Use `mutable` when the variable must change across turns (e.g., conversation state); omit when the value must be tamper-proof (e.g., verified identity).

**`available-when` filter evaluation timing:**
⚠ Evaluated at the **START of each turn**, not mid-reasoning. If a variable changes during reasoning (e.g., via an action), the `available-when` filter does NOT re-evaluate until the next turn. This means an action that sets a variable and a filter that gates on that variable won't interact within the same turn.
Exam-tested: Set 1 Q39, Set 2 Q40.

**Context variable for deterministic logic:**
⚠ Context variables (system-populated: channel, user identity, session metadata) can drive deterministic fee calculations, routing decisions, and conditional gating — the exam tests whether you select a context variable or a custom variable for system-provided values.
Exam-tested: Set 2 Q4.

**Cross-reference:** The `run` keyword (Obj 1) is the deterministic action invocation mechanism — it forces execution every turn regardless of LLM judgment. `reasoning.actions:` is the probabilistic counterpart.

### Distractor bank
\# **Instructions** = the trap for any "must always/never" stem — probabilistic
\# **LLM temperature** = generation tuning, not a workflow control
\# **Topic classification description** = influences *selection*, not hard enforcement of a value
\# **Custom variable** when the stem says "system-provided value" — that's a context variable

### Layer-Config-Symptom
| Layer | Config Items | Typical Symptom If Missing |
|-------|--------------|------------------------------|
| **Deterministic Control** | Variables, Filters, Template Expressions | Same input produces different output on repeat runs — condition evaluated inconsistently |

---

## OBJECTIVE 3 — TOPICS & ACTIONS (Standard / Custom)

**Purpose:** Configure what the agent can do (topics) and how it does it (actions) — the capability surface the reasoning engine selects from.

**Guide:** "select and configure standard topics, custom topics, standard Agent actions, and custom Agent actions."

### Topic (the capability)
✓ **Topic** = business capability container: classification description + scope + instructions + actions (e.g., "Order Management")
✓ **Standard topic** = ships out-of-the-box per agent type · **Custom topic** = org-specific capability you build
✓ **Classification description** = what the reasoning engine matches utterances against; the **primary lever** for misclassification fixes

### Action (the operation)
✓ **Action** = executable operation within a topic (e.g., "Query Order Status," "Initiate Refund")
✓ **Standard action** = Salesforce-shipped, configure-only
✓ **Custom action** = built from **Flow, invocable Apex, prompt templates, or External Services/APIs**

**Decision heuristic:**
```
"What business capability?"      → Topic
"What specific system operation?" → Action
"Agent must run an Apex method"   → Custom action
```

### Action Selection Mechanics ⚠ [VERSION_ALERT: SU26]

**Action `description` field (LLM selection driver):**
⚠ The `description` property on an action is what the LLM reads in **probabilistic mode** (`reasoning.actions:`) to decide whether and when to invoke that action. Poorly written action descriptions → LLM selects the wrong action or skips the right one. Analogous to `classification description` for topics but operates at the action level.
Exam-tested: Set 2 Q52 — "which component helps the LLM decide when to use the action?" → action `description`.

**Two-level selection model:**
```
User utterance → Classification Description → TOPIC selected
              → Action Description        → ACTION selected (within the matched topic)
```
Fix misclassification at the topic level → refine classification description.
Fix wrong action within correct topic → refine action description.

**Testing Center evaluation metrics:**
⚠ The Testing Center evaluates three distinct metrics per test case:
- **Topic match** — did the agent route to the correct topic?
- **Action match** — did the agent invoke the correct action(s)?
- **Response quality** — was the generated response accurate and appropriate?
Each metric can pass or fail independently. A "correct topic, wrong action" failure points to the action-level configuration, not the topic classification.
Exam-tested: Set 2 Q12.

### Distractor bank
\# **Intent** = user objective, not a configured capability (Topic distractor)
\# **Flow / Apex** = *implementations* of a custom action, offered as distractors for "Action"
\# **Prompt template** = can *be* a custom action, but is a Prompt Engineering artifact — wrong when the question wants the agent-layer construct
\# **Classification description** when the stem says "wrong action within correct topic" — that's the action `description`, not the topic classification description

### Layer-Config-Symptom
| Layer | Config Items | Typical Symptom If Missing |
|-------|--------------|------------------------------|
| **Topic Routing** | Standard/Custom Topics, Classification Descriptions, Topic Instructions (probabilistic guidance within a matched topic — distinct from Classification Descriptions which drive matching), Agent Router, Training Data Quality | Right topic not matched / inconsistent classification across similar utterances / agent matched correct topic but behaves inconsistently within it (Topic Instructions too vague) |
| **Action Config** | Standard/Custom Actions, Action Typology (Atomic/Composite, Deterministic/Prompt-based). Custom action sources: Flow, Invocable Apex, Prompt Templates, External Services/APIs | Right topic selected but wrong action invoked, or only one action fires on a compound request |

---

## OBJECTIVE 4 — SECURITY CONTEXT & THE AGENT USER

**Purpose:** Define whose permissions govern the agent's data access — the identity layer that silently gates every action's success or failure.

**Guide:** "Explain the security context in which the agent is actually running, and how it impacts agent action execution."

### The model
✓ **Security context** = determines which records, objects, and fields the agent can access, and therefore whether an action succeeds
✓ **Service Agent** → runs as a dedicated **Agent User** (external audience); data access governed by the Agent User's object + FLS permissions
✓ **Employee Agent** → runs in the **logged-in user's** context; inherits that employee's existing permissions

**Impact on action execution:** if the running context lacks FLS/object access, the action **fails or returns nothing** — even if the action is correctly configured. Security is a silent failure source; the exam tests this.

### The cross-domain trap
```
Agent record access       → Agent User (Service) or logged-in user (Employee)
Prompt merge-field access → Running User   (Prompt Engineering — NOT an agent term)
```

### User Verification Modes ⚠ [VERSION_ALERT: SU26]

**Credential-Based vs. Token-Based User Verification:**
⚠ **Token-Based** (default) = the agent acts as the **Agent User** for all DML operations. `LastModifiedBy` / `CreatedBy` fields show the Agent User record.
⚠ **Credential-Based** = the agent acts on behalf of the **authenticated Community User**. `LastModifiedBy` / `CreatedBy` fields show the actual authenticated user, NOT the Agent User.

**Binding rule:** "who shows up in audit fields after agent-initiated DML?" → depends on verification mode. Token-Based = Agent User. Credential-Based = Community User.
Exam-tested: Set 2 Q20.

**Missing permission set as root cause:**
⚠ When the agent action "returns nothing" or "data doesn't change" but no error is thrown → first suspect is the Agent User (or logged-in user) missing the required permission set. This is the #1 silent-failure pattern on the exam.
Exam-tested: Set 2 Q28.

### Distractor bank
\# **Running User** = Prompt Builder merge-field resolution — the #1 agent-security distractor; never the answer for agent execution context
\# **Permission set alone** = grants access, but the *identity the agent acts as* is Agent User / logged-in user
\# **Integration User / Named Credential** = integration-layer identities, wrong layer
\# **Token-Based verification** when stem says "audit fields show the actual customer" — that's Credential-Based

### Layer-Config-Symptom
| Layer | Config Items | Typical Symptom If Missing |
|-------|--------------|------------------------------|
| **Security** | Agent User record, CRUD/Permission Set grants (action execution) — NOT FLS, which governs reasoning-engine data visibility (Prompt Engineering) | Action executes without error but data doesn't change / silent permission failure |

---

## OBJECTIVE 5 — AGENT TYPES (Employee vs. Service)

**Purpose:** Match the agent type to the audience — the first architectural decision that determines security context, channel options, and data access model.

**Guide:** "identify when to use an Employee or Service agent." **Only these two types are in scope.**

| Agent Type | Audience | Purpose | Security context |
|---|---|---|---|
| ✓ **Employee Agent** | Internal workforce | HR, IT help desk, internal lookups | Logged-in user |
| ✓ **Service Agent** | External customers | Case deflection, Knowledge, returns | Agent User |

**Decision tree:**
```
Internal employee asking for help  → Employee Agent
External customer needing support   → Service Agent
```

**Anti-greed rule:** Service Agent is NOT the default. An internal HR/IT request is an **Employee Agent** even though it "feels like service." Match the *audience*, not the surface verb.

**❌ OUT OF SCOPE — removed from v3:** Sales Agent. The guide tests only Employee vs. Service. Discard any Sales Agent scenario bindings — they were off-blueprint.

### Sales Agent Scope ⚠ [VERIFY — primary-source before exam]

⚠ Set 2 (SU26, 77% scorer) Q6 and Q9 show **Sales Agent** as the marked-correct answer for partner-portal and upsell/escalation scenarios. Set 1 (93% scorer) marks the same Q6 scenario as **Service Agent**. The 77% scorer may have self-scored incorrectly on these questions (up to 15 questions wrong in that sitting).

**Current position:** Sales Agent remains out-of-scope per the official exam guide. However, if the Spring '26 or Summer '26 guide revision adds Sales Agent, it would apply to: partner portal (external sales audience), upsell/cross-sell (revenue-generating agent actions), and escalation-to-sales workflows.

**Exam strategy:** If an exam question offers Employee, Service, AND Sales Agent as options, and the scenario describes an external revenue/sales context → [VERIFY] whether Sales Agent is now in the exam pool. If only Employee and Service are offered → apply the standard audience-matching rule.

### Distractor bank
\# Choosing **Service Agent** for internal IT/HR (internal audience → Employee)
\# Choosing **Employee Agent** for external customer support (external → Service)
\# **Sales Agent / Copilot / Chatbot** = not exam-safe answers for this objective [VERIFY Sales Agent per SU26 evidence]

### Layer-Config-Symptom
| Layer | Config Items | Typical Symptom If Missing |
|-------|--------------|------------------------------|
| **Persona** | Employee Agent, Service Agent (only two types) | Agent behaves incorrectly for use case — Service agent accessing employee data, or Employee agent visible to customers |

---

## OBJECTIVE 6 — CHANNELS

**Purpose:** Connect the agent to the surfaces where users interact with it — the last-mile configuration that makes the agent reachable.

**Guide:** "connecting agents to various channels such as digital experience, email, voice, and Slack."

✓ **Digital experience** = Messaging for In-App and Web / Experience Cloud embed
✓ **Email** = inbound email conversations
✓ **Voice** = synchronous telephony integration (now exam-safe per guide — no longer [VERIFY])
✓ **Slack** = agent surfaced as a Slack app / collaboration surface
✓ **Omni-Channel flow** = routes HITL escalations to the right human-agent queue

### Channel Deployment Mechanics ⚠ [VERSION_ALERT: SU26]

**Messaging LWC requirement (digital experience):**
⚠ Deploying the agent to a digital experience (Messaging for In-App and Web) requires adding the **Messaging LWC** (Lightning Web Component) to the Experience Cloud page. Without this component, the agent is configured and active but **invisible** to end users — no chat widget appears.
Exam-tested: Set 2 Q57.

**Channel rebuild post-deployment:**
⚠ Channel connections do NOT deploy via change set or Metadata API. After deploying agent metadata to a target org, you must **manually reconnect** all channels (digital experience, email, voice, Slack) and rebuild Data Library indexes. Deploy ≠ channel-ready.
Cross-reference: Testing/Deployment Obj 3 (Agent Deployment — REBUILDS section).

### Distractor bank
\# **Lightning App / Record Page / Console** = UI surfaces, not agent channels
\# **"Retry forever"** = never the HITL answer; HITL escalates via Omni-Channel, it does not loop
\# **Change set** when the stem asks "how to connect the channel in the target org" — channels are manually reconnected, not deployed

### Layer-Config-Symptom
| Layer | Config Items | Typical Symptom If Missing |
|-------|--------------|------------------------------|
| **Channel & Linkage** | Communication Channels, Flow Definition Field, Service Channel, Telephony Connection, Fallback Queue vs. HITL | Agent deployed & active but invisible in channel — channel doesn't trigger agent, user can't reach agent |

---

## OBJECTIVE 7 — AGENT API

**Purpose:** Provide programmatic access to the agent for custom, headless, or external surfaces that standard channels don't cover.

**Guide:** "identify when it's appropriate to use Agent API." (Its own objective — NOT lumped under channels, NOT under Multi-Agent.)

✓ **Agent API** = programmatic/headless surface to invoke an Agentforce agent
**When appropriate:**
- A channel **not** natively supported by Salesforce (custom mobile app backend, proprietary terminal/portal)
- Headless integration / custom UI surface
- Bridging into external open-standard multi-agent systems

**Binding rule:** "no standard channel fits / custom or external surface / headless" → **Agent API**. If a standard channel (digital experience, email, voice, Slack) fits, use that instead.

### Three-Way Protocol Distinction ⚠ [VERSION_ALERT: SU26]

```
Agent API   = app → agent     (external system INVOKES the agent)
MCP         = agent → tools   (agent DISCOVERS and CALLS external tools/data)
A2A         = agent ↔ agent   (agents COMMUNICATE and DELEGATE across platforms)
```

**Binding rules:**
- "External system needs to call our agent" → **Agent API**
- "Agent needs to access external tools or data sources" → **MCP**
- "Two agents on different platforms need to coordinate" → **A2A**

⚠ **A2A** = Agent-to-Agent protocol — the open standard for cross-platform agent communication. NOT Agent API (which is app→agent, unidirectional invocation). The exam tests this distinction directly.
Exam-tested: Set 2 Q23 (A2A as open standard protocol), Q62 (Agent API as framework for external integration), Q64 (MCP purpose).

### Distractor bank
\# Using Agent API when a standard channel already fits — over-engineering
\# Confusing Agent API (invoke *an agent*) with MCP (agent → tools) or A2A (agent → agent)
\# **MCP** when the stem says "external system calling our agent" — MCP is agent-outbound, Agent API is agent-inbound
\# **A2A** when the stem says "custom app backend invoking the agent" — A2A is agent↔agent, not app→agent

### Layer-Config-Symptom
| Layer | Config Items | Typical Symptom If Missing |
|-------|--------------|------------------------------|
| **External** | Agent API, authentication, payload structure, connection | External system can't invoke agent — API call rejected, payload not accepted, no response |

---

## AI AGENTS — QUICK DIAGNOSTICS (PARETO KEYWORD MAP)

1. "Evaluate a formula deterministically" → **Template Expression**
2. "Strictly require a value before proceeding" → **Variable + Filter**
3. "Internal HR support bot" → **Employee Agent**
4. "Customer-facing returns agent" → **Service Agent**
5. "Agent must run an invocable Apex method" → **Custom Action**
6. "Agent on a proprietary external portal / headless" → **Agent API**
7. "Agent exposing fields the customer shouldn't see" → **Agent User permissions**
8. "Code-level agent authoring interface" → **Script View**
9. "Visual, declarative agent builder" → **Canvas / NGA**
10. "Combining enforced rules with LLM generation" → **Hybrid Reasoning**
11. "Must always / never / regardless" → **Deterministic** (never Instructions)
12. "Planner deciding which action runs" → **Reasoning engine** (never the LLM)
13. "Must execute every turn, no LLM choice" → **`run` keyword** (never `reasoning.actions:`)
14. "Set a variable without custom code" → **`@utils.setVariables`**
15. "Hand off to human agent from Agent Script" → **`@utils.escalate`**
16. "Who shows in audit fields after agent DML?" → **Verification mode** (Token-Based = Agent User, Credential-Based = Community User)
17. "Agent needs to call external tools" → **MCP** (not Agent API)
18. "Two agents coordinating across platforms" → **A2A** (not Agent API)
19. "Wrong action within correct topic" → **Action `description`** (not classification description)
20. "Action returns nothing, no error" → **Missing permission set** (silent failure)

## AI AGENTS — DISTINCTION PAIRS

1. **Canvas View vs. Script View** = visual declarative vs. code-level, same Agent Script
2. **Hybrid reasoning** = deterministic rails + probabilistic LLM together
3. **Reasoning engine vs. LLM** = plans workflow vs. generates language
4. **Filter vs. Variable vs. Template Expression** = eligibility gate vs. runtime value vs. formula evaluation
5. **Instructions vs. deterministic mechanisms** = probabilistic guidance vs. enforced logic
6. **Topic vs. Action** = business capability vs. executable operation
7. **Standard vs. Custom action** = shipped/configure-only vs. Flow/Apex/template/API
8. **Employee vs. Service Agent** = internal (logged-in user) vs. external (Agent User)
9. **Agent User vs. Running User** = agent execution identity vs. prompt merge-field identity
10. **Agent API vs. standard channel** = custom/headless/external vs. natively supported surface
11. **Agent lifecycle vs. Template lifecycle** = Ideation→Build→Test→Deploy→Observe vs. Build→Preview→Activate→Invoke
12. **`run` vs. `reasoning.actions:`** = deterministic forced execution vs. probabilistic LLM-chosen invocation
13. **`before_reasoning` timing** = fires at start of NEXT turn after transition, not mid-turn
14. **`@utils.*` vs. custom code** = built-in no-code Agent Script utilities vs. Flow/Apex
15. **`=` vs. `==`** = assignment vs. comparison — using `=` in a condition is a silent logic error
16. **Classification description vs. Action description** = topic selection driver vs. action selection driver
17. **Token-Based vs. Credential-Based verification** = Agent User in audit fields vs. Community User in audit fields
18. **Agent API vs. MCP vs. A2A** = app→agent vs. agent→tools vs. agent↔agent

## AI AGENTS — CALIBRATED CONFIDENCE

**HIGH confidence (guide-grounded, drill-confirmed):**
Topic vs. Action · Employee vs. Service selection · Agent User security context · deterministic vs. probabilistic (filters/variables vs. instructions) · reasoning engine vs. LLM · channels including Voice · Agent API when-to-use.

**MODERATE / ⚠ (training-edge — primary-source before exam):**
Agent Script syntax · exact Canvas vs. Script View mechanics · hybrid-reasoning internal wording · template-expression syntax · `run` vs. `reasoning.actions:` · `before_reasoning` timing · `@utils.*` utilities · action `description` field · `=` vs `==` operator · Credential-Based User Verification audit-field behavior · Messaging LWC requirement · A2A protocol specifics.

**Removed as out-of-scope:** Sales Agent (guide tests Employee/Service only). [VERIFY: Set 2 SU26 evidence suggests Sales Agent may appear in Summer '26 exam pool — primary-source the current exam guide before sitting.]

---
---

# PROMPT ENGINEERING (20%) — Study Notes v4-revised (Guide-Aligned)

**Exam:** Salesforce Certified Agentforce Specialist (AI-201), Spring '26
**Domain weight:** 20% (~12 of 60 questions)
**Status:** ✅ MASTERED — Wave 2: 11/12 (91.7%)
**Scope flags:** ✓ exam-safe · ⚠ training-edge, primary-source before exam · # distractor (wrong layer/scope) · ❌ nonexistent/colloquial

> **v4-revised delta from v4:** Purpose one-liner added to every objective. Few-shot examples best practice added to Obj 6. Prompt defense vs. toxicity scoring distinction sharpened in Obj 7 (jailbreaking prevention). BYOLLM model routing mechanics added to Obj 8. Related-list grounding for enrichment data cross-referenced in Obj 4. Considerations callout added to Obj 5 and Obj 8. All new items from Set 2 (SU26) carry ⚠ [VERSION_ALERT: SU26]. No existing content removed.

**Eight guide objectives:**
1. When it's appropriate to use Prompt Builder
2. Access controls governing prompt templates
3. Template type considerations (field generation, flex, etc.)
4. Appropriate grounding technique per scenario
5. Create/activate/execute lifecycle
6. Best practices for writing effective prompts
7. Trust Layer security/privacy features
8. Managing and preventing specific model access

---

## THE ARCHITECTURE STACK

```
Business Need
   ↓
Template Type        (Flex / Field Generation / Record Summary / Sales Email)
   ↓
Access Control        (Prompt Template User / Manager / Einstein Generative AI Admin)
   ↓
Grounding              (merge fields → related lists → flow → Data Library → DMO)
   ↓
Instructions            (probabilistic behavior guidance)
   ↓
Trust Layer              (masking → zero data retention → toxicity scoring → audit trail)
   ↓
Model                     (governed by Model Builder / BYOLLM)
   ↓
Response
```

**Top-down diagnosis heuristic:**
- Wrong template surfaced in the wrong place? → **Template Type** mismatch
- Wrong data injected? → **Grounding technique** mismatch
- Fabricated content? → **Instructions** (constrain to grounded data) — never toxicity scoring
- PII exposed to external LLM? → **Trust Layer masking**
- Wrong model available? → **Model Builder** access management

---

## OBJECTIVE 1 — PROMPT BUILDER APPROPRIATENESS

**Purpose:** Determine whether the use case requires AI-generated content grounded in live Salesforce data, or a static template — the first gate that prevents Prompt Builder misapplication.

✓ **Prompt Builder** = the tool for building **generated, dynamic** content grounded in Salesforce data
✓ **Use when:** content must be generated fresh per execution, grounded in records/data, admin-maintained, reusable
✓ **Do NOT use when:** content is static and identical for every recipient — that's a standard Lightning email template, not a prompt template

**Decision heuristic:** "identical wording, no generation, brand-approved" → static template. "Generated fresh, grounded in data, varies per execution" → Prompt Builder.

### Appropriateness Scenarios from Exam Sets

**Static content trap (the #1 PE distractor):**
When the stem says "identical for every recipient," "brand-approved copy that never changes," or "standard welcome email" → **Lightning email template**. The exam tests whether you default to Prompt Builder when no generation is needed.
Exam-tested: Set 2 Q33 (Lightning email template recommendation), Set 1 Q57 (Record Summary use case).

**Good use cases for Prompt Builder:**
⚠ The exam tests "which business requirement presents a good use case" — the answer always involves: (a) content that varies per record/context, (b) grounding in live data, (c) natural-language generation, not templated merge.
Exam-tested: Set 2 Q35, Set 2 Q24 (hotel resort guest summary on Contact page → Prompt Builder, Record Summary type).

### Distractor bank
\# **Lightning email template** = correct answer when NO generation is needed — the trap is reaching for Prompt Builder by default
\# **Flow with email alert** = static logic, not generative
\# **Einstein Work Summaries** = channel-level service feature, not Prompt Builder

### Layer-Config-Symptom
| Layer | Config Items | Typical Symptom If Missing |
|-------|--------------|------------------------------|
| **Template Type** | Flex (multi-unrelated inputs OR programmatic invocation), Field Generation (single-field ✨ button), Record Summary (single-record page), Sales Email (recipient + optional related object) | Wrong template surfaced in wrong context / template unavailable where expected |

---

## OBJECTIVE 2 — ACCESS CONTROLS

**Purpose:** Gate who can build, run, and administer prompt templates — a three-tier permission model where each tier is independent and the Running User's data access is a separate, parallel requirement.

✓ **Prompt Template User** = execute-only; runs active templates
✓ **Prompt Template Manager** = create, edit, manage templates in Prompt Builder
✓ **Einstein Generative AI Admin** = org-level generative AI setup; above build/run
✓ **Running user's FLS/object access** gates merge-field resolution — templates never execute in system context

### NEW — "Generative AI User" Permission Set ⚠ [VERSION_ALERT: SU26]
> **Source:** Set 2 Q28 — user can't see the ✨ field generation icon → "The user does not have the **Generative AI User** permission set assigned" (not "Prompt Template User")

✓ **Generative AI User** appears in Set 2 as a distinct answer from Prompt Template User — [VERIFY] whether this is a renamed label for the same permission or a fourth distinct permission set
✓ If distinct: Generative AI User = enables the ✨ icon visibility on fields; Prompt Template User = enables template execution. Different gates.
✓ If renamed: the three-tier model stands, but use "Generative AI User" as the exam-safe label for the execute tier
✓ **Exam trap in Q28:** option B offers "Prompt Template User" and option C offers "prompt template not activated for that user" — both are plausible but the correct answer is A (Generative AI User), suggesting the ✨ icon requires a specific permission distinct from template activation status

**Binding rule:** Run = User + data access (two independent requirements). Build = Manager. Administer = Generative AI Admin. Three tiers, no overlap. [VERIFY] whether "Generative AI User" is a fourth tier or a renamed "Prompt Template User."

### The Two-Requirement Gate

The exam tests this as a compound failure:
```
Can the user RUN the template?     → Prompt Template User perm set required
Can the template RESOLVE its data? → Running User's FLS/object access required
Both must be true. Either missing = different failure mode.
```
Missing User perm set → user can't execute at all. Missing FLS → template runs but merge fields resolve to **blank** (silent data loss, not an error). The exam distinguishes these two failure modes.
Exam-tested: Set 1 Q17 (Manager permission), Set 2 Q28 cross-ref (permission set as root cause pattern).

### Distractor bank
\# **Data Cloud permission set licenses** = real, wrong platform layer — never gate template execution
\# **Muting permission set** = real mechanism, never the minimum-grant answer
❌ **"Einstein Trust Layer Admin"** = DOES NOT EXIST — the Trust Layer has no named admin permission set; if "Trust Layer" appears inside a permission-set name, it is fake by construction

### Layer-Config-Symptom
| Layer | Config Items | Typical Symptom If Missing |
|-------|--------------|------------------------------|
| **Access Control** | Prompt Template User (execute-only), Prompt Template Manager (create/edit/manage), Einstein Generative AI Admin (org-level setup) — three tiers, no overlap. Running User's FLS/object access gates merge-field resolution (two independent requirements: permission set tier + Running User data access) | User can't run template (missing User perm set) / can't build template (missing Manager) / can't enable generative AI org-wide (missing Admin) / merge fields resolve to blank despite active template (Running User lacks FLS on referenced fields) |

---

## OBJECTIVE 3 — TEMPLATE TYPES

**Purpose:** Match the template type to the invocation surface and input shape — the most constrained type that satisfies the requirement is always the correct answer.

Bind as triples: **type → input shape/anchor → surface**

✓ **Flex** = (1) multiple unrelated input resources selected at run time OR (2) programmatic invocation surface (flow action / invocable Apex / agent action, e.g. automated generation with no user click)
✓ **Field Generation** = ✨-button-on-a-single-field via Lightning App Builder
✓ **Record Summary** = single-record page context, one-click, non-conversational
✓ **Sales Email** = recipient (lead/contact) + optional related object, surfaced in the email composer

**Anti-greed rule:** Flex fires ONLY on multi-unrelated-inputs OR automation — it is the last resort, not the default. Match the type to the constraint boundary of the use case; the correct answer is always the **most constrained type that still works**.

### Template Configuration Mechanics ⚠ [VERSION_ALERT: SU26]

**Adding resources to Flex templates:**
⚠ In Prompt Builder, "Add Resource" on a Flex template adds an additional data source (object/record) to the template's input set. Each resource brings its own merge fields. This is how Flex templates handle **unrelated** inputs — each input is a separate resource, not fields from a single record.
Exam-tested: Set 2 Q25.

**Output structure specification (Flex):**
⚠ For Flex templates generating structured output (e.g., emails), best practice is to specify **explicit section headings** in the instructions: Greeting, Body, Sign-off (or equivalent). This gives the LLM structural rails while allowing generated content within each section.
Exam-tested: Set 1 Q6 (Coral Cloud Flex, three-part structure), Set 2 Q19 (output structure recommendation).

### Distractor bank
\# **Einstein Work Summaries** = channel-level service feature — never field/record-page
\# **Record Summary as flow-invocable** = false; it's a page component only
\# **Field Generation for multi-object input** = false; it's single-field only
\# **Flex as default** when a more constrained type fits — anti-greed rule applies

### Layer-Config-Symptom
| Layer | Config Items | Typical Symptom If Missing |
|-------|--------------|------------------------------|
| **Template Type** | Flex (multi-unrelated inputs OR programmatic invocation), Field Generation (single-field ✨ button), Record Summary (single-record page), Sales Email (recipient + optional related object) | Wrong template surfaced in wrong context / template unavailable where expected |

---

## OBJECTIVE 4 — GROUNDING TECHNIQUES

**Purpose:** Select the lowest-cost grounding mechanism that satisfies the data requirement — escalate up the ladder only when the simpler option cannot compute or retrieve what the template needs.

The ladder — **lowest rung that satisfies the requirement wins:**

```
Merge fields             → direct field values from the anchor record
Related-list grounding   → child records displayed as-is
Template-triggered flow  → aggregation/logic (count, sum, filter) merge fields can't compute
Agentforce Data Library  → chunk-index-retrieve; semantic search over unstructured content
Data 360 DMO grounding   → zero-copy structured retrieval at run time, no replication
```

### Grounding Selection Scenarios ⚠ [VERSION_ALERT: SU26]

**Related-list grounding for enrichment data:**
⚠ Data 360 enrichment data (e.g., web activity records) surfaced as a **related list on Contact** can be grounded into a prompt template via related-list grounding. This bridges the D360→PE boundary: enrichment data flows through the standard related-list mechanism, not a custom retriever.
Exam-tested: Set 2 Q43 (D360 section, cross-refs PE grounding).

**Data Library grounding inside Flex templates:**
⚠ A Flex template can include an **Agentforce Data Library retriever** as one of its grounding sources. The retriever executes at invocation time, returns relevant chunks, and the template merges them alongside other input resources. This is how Flex templates combine structured record data (merge fields) with unstructured knowledge (Data Library) in a single generation.
Exam-tested: Set 2 Q34.

### NEW — Related-List Grounding Considerations
> **Source:** Set 1 Q45 — "UC is using related list merge fields associated with Account. What should UC consider?" → **Activities related list not supported (polymorphic relationship)**

✓ **Activities related list on Account is NOT supported** for merge-field grounding — Activities is a polymorphic relationship (Tasks + Events on a single related list), and Prompt Builder cannot resolve polymorphic relationships into merge fields
✓ **Person Accounts caveat:** enabling Person Accounts does NOT block merge fields on Account (distractor in Q45 option C — false)
✓ **Empty related list behavior:** prompt generation still returns a response when no related records exist — it does NOT yield "no response" (distractor in Q45 option A — false)
✓ **Exam pattern:** this is a Consideration stem testing edge-case awareness of related-list grounding, not grounding technique selection

**Ladder enforcement on exam:**
The exam tests whether you escalate prematurely. If the stem says "display child records as-is" → related-list grounding, NOT template-triggered flow. If the stem says "count/sum/filter across children" → template-triggered flow, NOT merge fields. The ladder is strict.

### Distractor bank
\# **Data Cloud Retrieval Action** = Agentforce **action layer** (structured, keyed on Unified Individual ID, injects into an **agent's** prompt context) — NOT Prompt Builder grounding
\# **Activation Target** = outward segment publishing; zero grounding role
\# **"RAG"** = architect vocabulary; exam-safe label is **Agentforce Data Library**
\# **Custom retriever** when the stem says "child records on the same object" — that's related-list grounding, not a retriever

### Layer-Config-Symptom
| Layer | Config Items | Typical Symptom If Missing |
|-------|--------------|------------------------------|
| **Grounding** | Merge fields → Related-list grounding → Template-triggered flow (aggregation/logic) → Agentforce Data Library (semantic search, unstructured) → Data 360 DMO (zero-copy structured) | Fabricated content (hallucination) — LLM generates without grounded data / wrong data injected / aggregation missing |

---

## OBJECTIVE 5 — LIFECYCLE (Create, Activate, Execute)

**Purpose:** Govern the template from authoring through production — the activation gate ensures no untested template serves live traffic, and versioning ensures every edit is traceable.

```
Build → Preview (sample-record test, pre-activation) → Activate (go-live gate) → Invoke
```

✓ **Preview** = build-time resolution test with a sample record — NOT called "Validate"
✓ **Version** = every edit creates a new version; served output changes only on activation
✓ **Activation** = the go-live gate; invocation requires active status

**Binding rule:** "edited but nothing changed" = new version not activated. Never caching, never a deploy requirement, never a lock.

### Lifecycle Considerations

**What to consider when managing prompt template versions:**
1. **Every edit = new version** — there is no "save without versioning." The version counter increments on every save.
2. **Activation is the serving gate** — a new version does NOT auto-serve. You must explicitly activate to push the new version to production. This is the exam's #1 lifecycle trap.
3. **Preview before activate** — Preview resolves the template against a sample record. If Preview shows blank merge fields, the Running User lacks FLS on those fields (cross-ref Obj 2).
4. **Deployed ≠ activated** — deploying a template via change set to a target org does NOT activate it. Activation is a separate manual step in the target org.
5. **Deployment includes ALL versions** — deploying a prompt template deploys every saved version from the source org to the target org, not just the active version. Prior versions are NOT stripped, NOT overwritten, and do NOT require manual activation before deployment succeeds. Exam-tested: Set 1 Q48.

Exam-tested: Set 2 Q45 ("correct process to leverage Prompt Builder" — confirms Build→Preview→Activate→Invoke), Set 2 Q39 cross-ref (versioning as work-preservation mechanism).

### Distractor bank
❌ **"Validate"** as a lifecycle step — the product label is Preview
\# **Trust Layer caching** = never the explanation for stale output after an edit
\# **Deploy** as a synonym for activate — deployment moves metadata, activation enables serving

### Layer-Config-Symptom
| Layer | Config Items | Typical Symptom If Missing |
|-------|--------------|------------------------------|
| **Lifecycle** | Build → Preview (sample-record test, pre-activation) → Activate (go-live gate) → Invoke. Versioning: every edit = new version, served only on activation | Edited template but output unchanged (new version not activated) / template can't be invoked (not activated) |

---

## OBJECTIVE 6 — BEST PRACTICES

**Purpose:** Write instructions that produce consistent, grounded, correctly structured output — the probabilistic layer where instruction quality directly determines generation quality.

✓ **Constrain to grounded data** = explicit instructions telling the model to only use provided grounding and state when information is unavailable — fixes **hallucination** (fabricated content)
✓ **Specify output structure** = explicit format/section requirements — fixes inconsistent formatting
✓ **Role + task + constraints, specific language** = the canonical instruction pattern

**Binding rule:** Hallucination (fabricated facts) → fix with constraining instructions. Toxicity (harmful/unsafe language) → fix with toxicity scoring. Different problems, different mechanisms — never interchange them.

### Instruction Techniques from Exam Sets ⚠ [VERSION_ALERT: SU26]

**Role-play instruction for tone:**
⚠ When the stem asks for "empathetic tone" or "professional warmth" → the correct answer is a **role-play instruction** in the prompt (e.g., "You are a compassionate customer service representative"). NOT LLM temperature, NOT a tone parameter, NOT toxicity scoring.
Exam-tested: Set 1 Q64, Set 2 Q17.

**Few-shot examples for extraction:**
⚠ When the stem asks for "extract [specific data] from unstructured text" (e.g., extract model number, extract key dates) → the correct best practice is **few-shot examples** embedded in the prompt instructions. Show 2-3 input→output pairs so the LLM learns the extraction pattern.
Exam-tested: Set 1 Q42, Set 2 Q47.

**Output structure specification:**
⚠ For multi-section generated content, specify **explicit section headings** (Greeting / Body / Sign-off, or Problem / Analysis / Recommendation). This constrains the LLM's output structure without constraining its language.
Exam-tested: Set 1 Q6 (three-part structure), Set 2 Q19 (output structure).

### Distractor bank
\# **Toxicity scoring for hallucination** = wrong dimension; toxicity ≠ factual accuracy
\# **Lower token limit** = does not fix fabrication, only truncates output
\# **LLM temperature** for tone control — temperature affects randomness, not emotional register; use role-play instructions instead
\# **System prompt** when the stem says "prompt template instruction" — the exam uses Salesforce terminology (instructions), not generic LLM terminology

### Layer-Config-Symptom
| Layer | Config Items | Typical Symptom If Missing |
|-------|--------------|------------------------------|
| **Instructions** | Probabilistic behavior guidance: role + task + constraints pattern, constrain-to-grounded-data directive, output structure specification | Inconsistent formatting / hallucinated facts (fix: constraining instructions, NOT toxicity scoring) |

---

## OBJECTIVE 7 — TRUST LAYER

**Purpose:** Protect data at the LLM boundary and detect harmful output — a set of orthogonal security features that apply org-wide to every generation request regardless of model or template.

✓ **Data masking** = mask PII before the prompt leaves Salesforce; **demask in the returned response** (the full round trip — both halves required)
✓ **Zero data retention** = external provider stores nothing, trains on nothing (exact trigram)
✓ **Toxicity scoring** = detects harmful/offensive/unsafe generated language — NOT hallucinations
✓ **Audit trail** = prompts, responses, masked-data records, toxicity scores → stored in **Data 360**
✓ **Prompt defense** = injection protections on the prompt envelope
✓ **Applies org-wide** = every generation request, prompt templates AND agent responses, regardless of model

### NEW — Dynamic Grounding as Trust Layer Component
> **Source:** Set 1 Q44 — "Which part of the Einstein Trust Layer architecture leverages an organization's own data within an LLM prompt?" → **Dynamic Grounding**

✓ **Dynamic Grounding** = the Trust Layer mechanism that injects org-specific data into the LLM prompt to produce relevant, accurate responses — reduces hallucination by grounding generation in real data
✓ Sits alongside masking, zero data retention, toxicity scoring, prompt defense, and audit trail as the **sixth Trust Layer architectural component**
✓ **Cross-ref Obj 4:** the grounding ladder (merge fields → related lists → flow → Data Library → DMO) feeds Dynamic Grounding. Obj 4 = which grounding technique; Obj 7 = Dynamic Grounding as the Trust Layer wrapper that ensures grounded data reaches the LLM
✓ **Exam trap:** Dynamic Grounding ≠ Prompt Defense (which protects against injection) ≠ Data Masking (which hides PII). Dynamic Grounding is the **accuracy** component; masking/defense are **security** components

### Trust Layer Threat-to-Feature Mapping ⚠ [VERSION_ALERT: SU26]

**Jailbreaking prevention:**
⚠ "Which feature helps minimize risks of jailbreaking?" → **Prompt defense**. NOT toxicity scoring (which detects harmful output AFTER generation), NOT data masking (which protects data, not the prompt envelope). Jailbreaking = adversarial prompt manipulation to bypass guardrails. Prompt defense = the feature that hardens the prompt envelope against such manipulation.
Exam-tested: Set 2 Q27.

**Threat-to-feature binding (exam-safe):**
```
Threat: PII leaks to external LLM       → Feature: Data masking (mask + demask round trip)
Threat: Provider stores/trains on data   → Feature: Zero data retention
Threat: Harmful/offensive output         → Feature: Toxicity scoring
Threat: Prompt injection / jailbreaking  → Feature: Prompt defense
Threat: No compliance record             → Feature: Audit trail → Data 360
Threat: Hallucination / irrelevant output → Feature: Dynamic Grounding (injects org data into prompt)
```
Each threat maps to exactly ONE feature. The exam tests cross-wiring (e.g., using toxicity scoring for jailbreaking, or masking for hallucination).

### Distractor bank
\# **Data Cloud dynamic data masking** = display-layer rendering, different product surface
\# **Shield Platform Encryption** = at-rest encryption, not LLM-boundary masking
\# **Setup Audit Trail** = admin config changes only — never AI interaction content (recurring trap across all domains)
\# **Event Monitoring** = platform logs, requires Shield, not the generative AI observability surface
\# **Toxicity scoring** for jailbreaking prevention — wrong; toxicity detects output, prompt defense protects input

### Layer-Config-Symptom
| Layer | Config Items | Typical Symptom If Missing |
|-------|--------------|------------------------------|
| **Trust Layer** | Data masking (mask before LLM + demask in response — full round trip), Zero data retention, Toxicity scoring (harmful language — NOT hallucination), Audit trail (stored in Data 360), Prompt defense (injection protection) | PII exposed to external LLM / harmful language passes through / no compliance audit record / prompt injection succeeds |

---

## OBJECTIVE 8 — MODEL MANAGEMENT

**Purpose:** Control which foundation models are available org-wide and register external customer-tenancy models — an orthogonal control surface from the Trust Layer that governs model access, not data protection.

✓ **Model Builder (Einstein Setup)** = enable/restrict/register foundation models; disable a model → unavailable for template selection org-wide
✓ **BYOLLM** = register a customer-tenancy model (e.g., their own Bedrock) via Model Builder

**Binding rule (layer separation):** Model Builder governs **WHICH** models are available. Trust Layer governs **HOW** data is protected, regardless of model. Orthogonal control surfaces — never the same Setup node.

### Model Routing Mechanics ⚠ [VERSION_ALERT: SU26]

**BYOLLM configuration:**
⚠ Registering a customer-tenancy model via BYOLLM requires configuring the **model routing** in Model Builder — specifying which API endpoint, authentication credentials, and model parameters the org should use when invoking the external model. This is distinct from restricting access (which disables a model) — routing directs traffic to the right endpoint.
Exam-tested: Set 2 Q65.

**Considerations for model management:**
1. **Disable ≠ delete** — disabling a model in Model Builder removes it from template selection but does not delete its configuration. Re-enabling restores access instantly.
2. **Trust Layer applies regardless** — switching models via Model Builder does NOT bypass Trust Layer protections. Masking, toxicity scoring, and audit trail apply to every model, including BYOLLM-registered models.
3. **BYOLLM does not bypass zero data retention** — the customer-tenancy model may have its own retention policy, but Salesforce's Trust Layer still applies to the Salesforce-side of the interaction (masking, audit trail, prompt defense).
4. **Model routing is per-org, not per-template** — you cannot route Template A to Model X and Template B to Model Y within the same org via Model Builder alone [VERIFY: Spring '26 may have added per-template model selection].

### Distractor bank
❌ **"They are the same configuration surface exposed in two Setup nodes"** = false — Model Builder and Trust Layer are always orthogonal
\# **Trust Layer** when the stem asks about model availability — Trust Layer governs protection, not access
\# **Named Credential** for BYOLLM authentication — Named Credentials are used in the registration, but the exam answer is "Model Builder / BYOLLM," not the underlying auth mechanism

### Layer-Config-Symptom
| Layer | Config Items | Typical Symptom If Missing |
|-------|--------------|------------------------------|
| **Model Management** | Model Builder (enable/restrict/register foundation models), BYOLLM (register customer-tenancy model) | Wrong model available org-wide / can't restrict model access / can't register external model |

---

## PARETO KEYWORD MAP

1. "Static, identical, no generation" → **Lightning email template** (not Prompt Builder)
2. "Create/edit templates, no admin rights" → **Prompt Template Manager**
3. "Execute only" → **Prompt Template User**
4. "Multiple unrelated inputs at run time" → **Flex**
5. "✨ button on a field" → **Field Generation**
6. "Automated, no user click, flow-triggered" → **Flex via flow action**
7. "Aggregation/count/sum across children" → **Template-triggered prompt flow**
8. "Semantic search over PDFs/articles" → **Agentforce Data Library**
9. "Zero-copy structured data" → **Data 360 DMO grounding**
10. "Edited but output unchanged" → **new version not activated**
11. "Fabricated content not in grounding" → **Hallucination → constraining instructions**
12. "Harmful/unsafe language" → **Toxicity scoring**
13. "PII unreadable to external LLM, readable in response" → **Trust Layer data masking**
14. "Provider stores/trains on our data?" → **Zero data retention (false — it doesn't)**
15. "Disable a model org-wide" → **Model Builder**
16. "Empathetic/warm/professional tone" → **Role-play instruction** (never LLM temperature)
17. "Extract specific data from unstructured text" → **Few-shot examples**
18. "Prevent prompt injection / jailbreaking" → **Prompt defense** (never toxicity scoring)
19. "Register external customer-tenancy model" → **BYOLLM via Model Builder**
20. "Child records displayed as-is in template" → **Related-list grounding** (never template-triggered flow)

---

## SEVEN EXAM-SAFE LABELS (exact, no fragments)

| Label | What it is |
|---|---|
| Prompt Template User | Execute-only permission set |
| Prompt Template Manager | Create/edit/manage permission set |
| Einstein Generative AI Admin | Org-level generative AI administration |
| Flex template | Multi-unrelated-input OR programmatic invocation |
| Agentforce Data Library | Chunk-index-retrieve grounding for unstructured content |
| Trust Layer data masking | Mask before LLM, demask in response |
| Model Builder | Governs which foundation models are available (incl. BYOLLM) |

---

## DISTINCTION PAIRS

1. **Manager vs. User** = builds vs. runs
2. **Field Generation vs. Flex** = one field via ✨ vs. multi-unrelated inputs / automation
3. **Preview vs. Activate** = build-time test vs. go-live gate
4. **Trust Layer masking vs. Data Cloud dynamic masking** = LLM-boundary round-trip vs. display rendering
5. **Gen-AI audit trail vs. Setup Audit Trail** = AI content in Data 360 vs. admin config changes
6. **Model Builder vs. Trust Layer** = which models vs. how protected
7. **Hallucination vs. Toxicity** = fabricated facts (fix: instructions+grounding) vs. harmful language (fix: toxicity scoring)
8. **Prompt defense vs. Toxicity scoring** = prevents jailbreaking (input-side) vs. detects harmful output (output-side)
9. **Role-play instruction vs. LLM temperature** = emotional register/tone vs. output randomness
10. **Few-shot examples vs. constraining instructions** = teach extraction pattern vs. limit to grounded data
11. **Deploy vs. Activate (templates)** = move metadata to target org vs. enable serving in target org
12. **Related-list grounding vs. Template-triggered flow** = display child records as-is vs. compute aggregation/logic across children

---

## CALIBRATED CONFIDENCE

**HIGH:** Template type triples · access control tiers · grounding ladder · lifecycle sequence · Trust Layer feature set · hallucination vs. toxicity discrimination. All drill-confirmed at 91.7%+.

**MODERATE ⚠:** Prompt defense vs. toxicity scoring for jailbreaking (newly sharpened — verify exact feature naming in Spring '26 docs) · BYOLLM model routing specifics · related-list grounding for D360 enrichment data (cross-domain — verify the exact mechanism) · few-shot examples as exam-tested best practice (concept is sound, verify Salesforce-specific terminology).

**[VERSION_ALERT: SU26] items:** Enrichment related-list grounding · BYOLLM model routing configuration · Flex resource addition mechanics. All sourced from Set 2 (77% scorer) — verify against primary docs before relying on these as exam-safe.

---
---

# DATA 360 FUNDAMENTALS (20%) — Study Notes v4-revised (Guide-Aligned)

**Exam:** Salesforce Certified Agentforce Specialist (AI-201), Spring '26
**Domain weight:** 20% (~12 of 60 questions)
**Status:** ✅ MASTERED — Wave 3: 6/6 (100%)
**Scope flags:** ✓ exam-safe · ⚠ training-edge, primary-source before exam · # distractor (wrong layer/scope) · ❌ nonexistent/colloquial

> **v4-revised delta from v4:** Purpose one-liner added to every objective. Docling parser for tabular content added to Obj 1. Multilingual embedding model added to Obj 2. Enrichment related list cross-reference added to Obj 3. File size limits [VERIFY] added to Obj 1. All new items from Set 2 (SU26) carry ⚠ [VERSION_ALERT: SU26]. No existing content removed.

**Two guide objectives (the guide collapses this domain to two broad statements); V4 expands to 4 functional objectives:**
1. Considerations of Agentforce Data Library and its concepts
2. Foundational Data 360 concepts: chunking, indexing, retrievers (search types fall under this)

---

## THE ARCHITECTURE STACK

```
Agentforce Data Library   (managed automation layer — orchestrator, no data stored)
   ↓ provisions inside ↓
Data Cloud                 (storage + compute substrate)
   ├── Chunks              (unstructured data lake object)
   ├── Search index        (embedded vectors of chunks)
   └── Retrievers          (query the index at each request)
```

**Library = the recipe. Data Cloud = the kitchen and ingredients.** Every search goes back to Data Cloud at query time — the library doesn't cache or duplicate.

**Top-down diagnosis heuristic:**
- Wrong/missing content retrieved? → **Chunking** (chunk size trade-off)
- Irrelevant category surfaced? → **Retriever** (missing metadata filter)
- No results at all? → **Search type** (keyword can't match intent)
- Wrong data shape entirely? → **Structured vs. unstructured path** confusion

---

## OBJECTIVE 1 — AGENTFORCE DATA LIBRARY

**Purpose:** Provision the managed unstructured data pipeline — choose the right library type for the content source, and understand that the library orchestrates but Data Cloud stores and indexes.

✓ **Agentforce Data Library** = managed automation layer that provisions chunking + search index + default retriever **inside Data Cloud** — simplifies, never replaces
✓ **Knowledge-based library** = syncs published Knowledge articles; respects article lifecycle (publish/unpublish flows to index, with re-sync latency)
✓ **File-based library** = uploaded documents (PDFs); no Knowledge dependency
✓ **Manual pipeline** = custom chunking + custom index + tuned retriever; finer control; **coexists** with managed libraries — neither is mandatory or deprecated

**Decision heuristic:** Published Knowledge articles → Knowledge-based. External PDFs, no Knowledge plan → File-based. Existing tuned manual pipeline → evaluate tuning loss before migrating; do NOT tear down by default.

### Data Library Configuration ⚠ [VERSION_ALERT: SU26]

**Docling parser (tabular content):**
⚠ For documents containing **tabular content** (tables, structured data within PDFs), the **Docling parser** is the appropriate approach. Standard PDF parsing may lose table structure; Docling preserves row/column relationships during ingestion into the Data Library pipeline.
Exam-tested: Set 2 Q8.

**Intelligent Context (file-based libraries):**
⚠ **Intelligent Context** is a Data Library feature that enriches file-based libraries with contextual metadata — improving retrieval relevance by associating document-level context with individual chunks. Enables the retriever to understand not just what a chunk says but where it sits within the broader document.
Exam-tested: Set 1 Q4.

**File size limits [VERIFY — conflicting exam data]:**
⚠ Set 1 Q41 marks: text/HTML = 4MB, PDF = 100MB. Set 2 Q29 marks: text/HTML = 100MB, PDF = 4MB. **These contradict.** The 93% scorer (Set 1) is more reliable (fewer wrong answers). Primary-source the Spring '26 Data Library documentation for exact limits before exam. Until verified, know that the exam DOES test file size limits and the limits differ by file type.

### Distractor bank
❌ **"Library replaces Data Cloud"** = false; library is built ON Data Cloud
❌ **"Libraries are mandatory as of Spring '26"** = false; both paths coexist
\# **"Library indexes in the Agentforce runtime"** = false; indexing happens in Data Cloud's search index
\# **Standard PDF parser** when the stem says "tabular content" — that's Docling

### Layer-Config-Symptom
| Layer | Config Items | Typical Symptom If Missing |
|-------|--------------|------------------------------|
| **Library Type** | Knowledge-based library (syncs published Knowledge articles), File-based library (uploaded PDFs), Manual pipeline (custom chunking + index + tuned retriever — coexists with managed, neither mandatory) | Wrong content source indexed / Knowledge articles not syncing (publish/unpublish lifecycle) / existing tuned pipeline torn down unnecessarily |

---

## OBJECTIVE 2 — CHUNKING & INDEXING

**Purpose:** Control how unstructured content is split and embedded — chunk size is the single most impactful tuning lever, and the fix for retrieval quality problems is always upstream in chunking, never downstream.

✓ **Chunking** = splitting unstructured content into passages before embedding
✓ **Indexing** = embedding chunks into a Data Cloud search index as vectors
✓ **Vector embedding** = numerical representation of a chunk that **encodes semantic meaning** — similar meaning = close in vector space = similarity-based retrieval without exact term matches

### Chunk size trade-off (two-sided)
| Direction | Effect | Fixes |
|---|---|---|
| **Smaller chunks** | Higher precision, narrower passages | Agent misses specific details in long docs |
| **Larger chunks** | More context, less dilution | Agent returns disconnected fragments |

**Binding rule:** the fix is always **upstream in chunking**, never downstream in post-processing (instructions/flows).

### Embedding Model Selection ⚠ [VERSION_ALERT: SU26]

**Multilingual embedding model:**
⚠ For orgs with **cross-language retrieval** requirements (e.g., user queries in Spanish, knowledge base in English), configure the **`multilingual-e5-large`** embedding model. The default English-only model will fail on cross-language similarity matching — queries and content must be embedded in the same semantic space.
Exam-tested: Set 2 Q58.

**Embedding model impact:** The embedding model determines the vector space. Changing the model **requires re-indexing all chunks** — existing embeddings are incompatible with a new model's vector space. This is a one-time migration cost, not a runtime configuration.

### Distractor bank
\# **Vector embedding as a unique identifier** = false; it's a meaning-map, not a label — this fault recurred across multiple drills, treat as high-risk
\# **Template-triggered prompt flow for chunking fixes** = wrong pipeline (Prompt Builder grounding, not agent retrieval)
\# **Default English model** when the stem says "multilingual" — wrong model, wrong vector space

### Layer-Config-Symptom
| Layer | Config Items | Typical Symptom If Missing |
|-------|--------------|------------------------------|
| **Chunking** | Chunk size configuration (smaller = higher precision, narrower passages; larger = more context, less dilution) | Agent misses specific details in long docs (chunks too large) / agent returns disconnected fragments (chunks too small) — fix is always upstream in chunking, never downstream |
| **Indexing** | Search index, vector embeddings (numerical representation encoding semantic meaning — similar meaning = close in vector space) | No retrieval possible / content ingested but not searchable (not yet embedded into index) |

---

## OBJECTIVE 3 — RETRIEVERS (Individual / Ensemble)

**Purpose:** Route queries to the right subset of indexed content — the retriever layer is where scoping, filtering, and multi-source merging happen, keeping the index itself clean and general.

✓ **Individual retriever** = one retriever against one search index
✓ **Default retriever** = auto-created per search index; shared baseline for **ALL** consumers — never edited for a scoped use case (global impact)
✓ **Custom retriever** = purpose-built on the **same index**, with metadata filters, referenced by a **specific agent action**
✓ **Ensemble retriever** = combines multiple individual retrievers; **merges and re-ranks** into a single unified result set — a composition mechanism, NOT a fourth search type
✓ **Metadata filters** = query-time restriction (region, tier, category) — configured on **custom** retrievers only

### Three binding rules
1. **Index ≠ retriever** — one index serves many retrievers
2. **Default ≠ custom** — editing the default has global impact; create a custom instead
3. **Metadata filter ≠ structural separation** — one index + filter beats per-segment indexes

### Retriever Configuration Scenarios ⚠ [VERSION_ALERT: SU26]

**Enrichment data as retriever source (cross-domain):**
⚠ Data 360 enrichment data (e.g., web activity records, behavioral signals) surfaced as a **related list on Contact** can be grounded into a prompt template via related-list grounding (cross-ref PE Obj 4). This is NOT retriever-based — it uses the PE grounding ladder, not the D360 retriever path. The exam tests whether you conflate these two mechanisms.
Exam-tested: Set 2 Q43.

**Metadata filter revision pattern:**
⚠ When the exam stem says "retriever returns irrelevant results from wrong category" → the fix is revising the **metadata filter** on a custom retriever, NOT editing the default retriever, NOT changing chunk size, NOT changing search type. The filter is the scoping mechanism.
Exam-tested: Set 2 Q37.

### Distractor bank
❌ **"Edit the default retriever for scoped use"** = global impact on all consumers
❌ **"Create per-region search indexes"** = over-engineered when a metadata filter solves it
❌ **"Ensemble is a search type"** = false; it's a composition mechanism operating ON search types
\# **Related-list grounding** when the stem asks about retriever-based content — different mechanism, different pipeline

### Layer-Config-Symptom
| Layer | Config Items | Typical Symptom If Missing |
|-------|--------------|------------------------------|
| **Retriever** | Default retriever (auto-created, shared baseline — never edit for scoped use, global impact), Custom retriever (scoped, with metadata filters, per-action), Ensemble retriever (composition mechanism merging/re-ranking multiple retrievers — NOT a search type) | Irrelevant category results (missing metadata filter on custom retriever) / global retrieval behavior broken (default retriever edited) / mixed-corpus results not merged (no ensemble) |

---

## OBJECTIVE 4 — SEARCH TYPES (Keyword / Vector / Hybrid)

**Purpose:** Match the search algorithm to the query pattern — exact-match lookups need keyword, intent-based queries need vector, mixed corpora need hybrid.

✓ **Keyword** = lexical term matching — exact codes, IDs, SKUs
✓ **Vector (semantic retrieval)** = similarity on embeddings — paraphrase, intent, vocabulary-independent
✓ **Hybrid** = keyword + vector combined, re-ranked — mixed corpus, default when both precision and recall matter

**Search types operate EXCLUSIVELY on the unstructured path.** The structured path (Data Cloud Retrieval Action) has no search type — it's an identity-keyed lookup, not a search.

### The two parallel retrieval paths (never nested)
```
STRUCTURED:   Data Cloud Retrieval Action → DMOs + Calculated Insights →
              keyed on Unified Individual ID → "what records belong to this person?"
UNSTRUCTURED: Agentforce Data Library → chunk → index → retriever → search type →
              "what content is relevant to this question?"
```
Both inject into the same prompt context (template or agent). Neither nests inside the other.

### Diagnostic pattern
| Symptom | Root layer | Fix |
|---|---|---|
| "No results" on conversational queries | Search type | Change to hybrid/vector |
| "Wrong results" — vector polluting exact-ID queries | Search type | Split into type-matched retrievers + ensemble |
| Irrelevant category results | Retriever | Add metadata filter on custom retriever |
| Missing/buried details | Chunking | Decrease chunk size |

### Distractor bank
❌ **"Vector search matches on Unified Individual ID"** = false; vector matches query text, not identity keys
❌ **"Instructions fix empty retrieval"** = false; instructions are downstream of retrieval, act on nothing if retrieval returns nothing
\# **"RAG"** as the exam label = correct concept, wrong vocabulary register — say **Agentforce Data Library**

### Layer-Config-Symptom
| Layer | Config Items | Typical Symptom If Missing |
|-------|--------------|------------------------------|
| **Search Type** | Keyword (lexical term matching — exact codes, IDs, SKUs), Vector/Semantic (similarity on embeddings — paraphrase, intent), Hybrid (keyword + vector combined, re-ranked) — operates EXCLUSIVELY on unstructured path | No results on conversational queries (keyword can't match intent) / exact-ID queries polluted by semantic matches (wrong search type) |
| **Structured Path** | Data Cloud Retrieval Action → DMOs + Calculated Insights → keyed on Unified Individual ID — parallel to unstructured path, never nested inside it | Structured record lookup fails / identity-keyed retrieval confused with semantic search (different paths, different mechanisms) |

---

## PARETO KEYWORD MAP

1. "Published Knowledge articles, auto-sync" → **Knowledge-based library**
2. "Uploaded PDFs, no Knowledge" → **File-based library**
3. "Tuned custom pipeline already exists" → **evaluate before migrating, don't force**
4. "Missed details in long docs" → **decrease chunk size**
5. "Disconnected fragments" → **increase chunk size**
6. "Scoped retrieval by region/tier/category" → **custom retriever + metadata filter**
7. "Editing shared baseline has global impact" → **never edit the default retriever**
8. "Combine keyword + vector, merge/re-rank" → **ensemble retriever**
9. "Exact codes/IDs" → **keyword search**
10. "Paraphrase/intent, vocabulary-independent" → **vector search**
11. "No results, conversational phrasing" → **search-type failure, not chunking**
12. "Structured records by identity" → **Data Cloud Retrieval Action (not search types)**
13. "Numerical representation encoding meaning" → **vector embedding**
14. "Tabular content in PDFs" → **Docling parser** (not standard PDF parser)
15. "Cross-language retrieval" → **multilingual-e5-large embedding model**
16. "Wrong category results from retriever" → **metadata filter revision** (not default retriever edit)

---

## SEVEN EXAM-SAFE LABELS (exact, no fragments)

| Label | What it is |
|---|---|
| Agentforce Data Library | Managed chunk-index-retrieve pipeline inside Data Cloud |
| Chunking | Splitting content into passages before embedding |
| Search index | Stored vector embeddings queried by retrievers |
| Default retriever | Auto-created shared baseline, never edited for scoped use |
| Custom retriever | Scoped retriever with metadata filters, per-action |
| Ensemble retriever | Composition mechanism merging/re-ranking multiple retrievers |
| Data Cloud Retrieval Action | Structured path — DMOs/CIs keyed on Unified Individual ID |

---

## DISTINCTION PAIRS

1. **Chunking vs. Indexing** = splitting vs. embedding-into-index
2. **Index vs. Retriever** = stored embeddings vs. query strategy
3. **Individual vs. Ensemble** = one index path vs. merged multi-retriever
4. **Default vs. Custom retriever** = shared unfiltered baseline vs. scoped per-action
5. **Keyword vs. Vector** = exact terms vs. meaning
6. **Structured (Retrieval Action) vs. Unstructured (Data Library)** = identity lookup vs. relevance search — parallel, never nested
7. **Managed library vs. Manual pipeline** = automated defaults vs. tuned control — coexist, neither mandatory
8. **Related-list grounding vs. Retriever-based grounding** = PE grounding ladder (child records on object) vs. D360 search pipeline (chunks from index)
9. **Docling parser vs. Standard parser** = tabular content preservation vs. flat text extraction
10. **Multilingual vs. Default embedding model** = cross-language vector space vs. English-only — model change requires full re-index

---

## CALIBRATED CONFIDENCE

**HIGH:** Library types · chunking trade-off · retriever architecture (individual/default/custom/ensemble) · search type selection · structured/unstructured path separation. All drill-confirmed at 100% under scenario pressure (Wave 3).

**MODERATE ⚠:** Docling parser (SU26 feature, verify naming) · multilingual-e5-large (verify model name against Spring '26 docs) · Intelligent Context (verify feature name and scope) · file size limits (conflicting exam data — primary-source required).

---
---

# TESTING, DEPLOYMENT & MAINTENANCE (10%) — Study Notes v4-revised (Guide-Aligned)

> **v4-revised delta from v4:** Purpose one-liner added to every objective. Generate test cases from knowledge base added to Obj 1-2. LLM-as-judge evaluation technique added to Obj 1-2. LLM API name match deployment consideration added to Obj 3. Flow activation as deployment dependency added to Obj 3. Considerations callouts added to Obj 3 and Obj 4. All new items from Set 2 (SU26) carry ⚠ [VERSION_ALERT: SU26]. No existing content removed.

**Guide objectives:** (1) test an agent using Testing Center · (2) explain how Testing Center evaluations work · (3) considerations for deploying an **agent** sandbox→production · (4) considerations for deploying a **template** sandbox→production.

> **Guide delta:** adoption monitoring / analytics / optimization has MOVED to the **Governance & Observability** section (below). This section is now scoped to Testing Center + deployment only.

## OBJECTIVE 1–2 — AGENTFORCE TESTING CENTER

**Purpose:** Validate agent behavior at scale before any user sees it — the Testing Center is the pre-deployment quality gate that catches misclassification, wrong action selection, and poor response quality in batch, not one-by-one.

✓ **Agentforce Testing Center** = batch evaluation harness for agents, used **pre-production in sandbox**; a pre-deployment quality gate, NOT a monitoring tool
✓ **Purpose:** validate the agent selects the correct **topic** and correct **action** for a given utterance — at scale, before any user sees it

### The four-step sequence (bind cold)
```
Generate test cases  (AI-generated OR CSV-uploaded OR knowledge-generated utterances)
   ↓
Run batch evaluation (Testing Center executes utterances against the agent)
   ↓
Review results       (topic match + action match + response quality per case)
   ↓
Deploy               (only after evaluation passes)
```
**Binding rule:** Generate → Run → Review → Deploy. Deployment is last, never first. "Deploy first, test in production" = wrong by construction.

### What Testing Center evaluates
✓ **Topic match** = did the agent classify the utterance to the correct topic?
✓ **Action match** = did the agent select/invoke the correct action within that topic?
✓ **Response quality** = was the generated response appropriate for the test case?

### Test case sources (three paths, same engine) ⚠ [VERSION_ALERT: SU26]
✓ **AI-generated** = synthetic utterances from topic/action definitions
✓ **CSV-uploaded** = manually curated utterances imported in bulk
⚠ **Knowledge-generated** = test cases generated from the agent's **knowledge base** content — the Testing Center can analyze the agent's grounded knowledge and produce utterances that test retrieval + response quality, not just topic/action routing.
Exam-tested: Set 2 Q56.

### Evaluation Techniques ⚠ [VERSION_ALERT: SU26]

**LLM-as-judge:**
⚠ **LLM-as-judge** is an evaluation technique where the LLM itself evaluates the quality of agent responses against a reference answer or grounded content. Used for **response quality** assessment (the third evaluation metric), not for topic match or action match (which are deterministic comparisons).
Exam-tested: Set 2 Q2.

**Evaluation metric binding:**
```
Did the agent pick the right topic?   → Topic match    (deterministic comparison)
Did the agent invoke the right action? → Action match   (deterministic comparison)
Was the response accurate/appropriate? → Response quality (LLM-as-judge or human review)
```
Each metric can pass or fail independently. "Correct topic, correct action, poor response" → response quality problem (grounding or instructions), NOT classification.
Exam-tested: Set 2 Q12.

### Scale signal (distractor separator)
```
One utterance, interactive debugging → Agent Builder preview (Plan Tracer)
Hundreds of utterances, batch eval   → Agentforce Testing Center
```

### Distractor bank
\# **Plan Tracer / Agent Builder preview** = single-utterance interactive; wrong for batch stems
\# **Agent Analytics** = post-deployment monitoring; wrong pipeline for pre-deployment testing
\# **Prompt Builder preview** = template resolution check; wrong domain
\# **"Deploy first, validate in production"** = always wrong
\# **LLM-as-judge for topic match** = wrong; topic match is deterministic, not LLM-evaluated

### Layer-Config-Symptom
| Layer | Config Items | Typical Symptom If Missing |
|-------|--------------|------------------------------|
| **Testing Center** | Agentforce Testing Center (batch evaluation harness, pre-production in sandbox), AI-generated test cases, CSV-uploaded test cases, knowledge-generated test cases, topic match + action match + response quality evaluation | Misclassification or wrong action not caught before production / no pre-deployment quality gate / "deploy first, test in production" anti-pattern |

## OBJECTIVE 3 — AGENT DEPLOYMENT (Sandbox → Production)

**Purpose:** Move the agent from sandbox to production without breaking it — understand what travels as metadata versus what must be rebuilt, and that deployment is always inactive-first.

### The fundamental rule
```
METADATA → DEPLOYS   (change sets / Metadata API / DevOps Center)
DATA     → REBUILDS  (re-created in production manually)
```

### What DEPLOYS (metadata — travels with the agent)
✓ Agent definition (topics, instructions, action configurations)
✓ Flows (custom actions) · Invocable Apex (custom actions)
✓ Prompt templates · External Services/API configurations · Permission sets

**Dependency rule:** all dependencies deploy **together** in the same unit. A missing dependency = broken agent in production.

### What REBUILDS (data/connections/state — NOT deployed)
✓ **Channel connections** (Messaging for In-App and Web, Slack, email) — rebuilt in production
✓ **Agentforce Data Library content + search indexes** — re-indexed against production data
✓ **Custom retrievers with metadata filters** — recreated in production
✓ **Agent activation state** — agents deploy **inactive**; activation is a deliberate production step

### Activation sequence
```
Deploy (inactive) → Activate in production → Connect channels → Monitor
```
**Binding rule:** agents never deploy active. Activation is always a separate step. Channels connect AFTER activation.

### Deployment mechanisms
✓ **Change sets** = org-to-org, point-and-click; standard
✓ **Metadata API** = programmatic; CI/CD
✓ **DevOps Center** = Salesforce-native pipeline tooling
⚠ **[VERIFY] GenAi* metadata types** (e.g., GenAiAgent, GenAiTopic, GenAiAction) — verify exact names against Spring '26 Metadata API docs; principle is exam-safe, exact type names are training-edge.

### Deployment Considerations ⚠ [VERSION_ALERT: SU26]

**LLM API name match:**
⚠ When deploying agent metadata from sandbox to production, the **LLM API name** configured in the agent must **match** between source and target org. If the target org has a different LLM API name (e.g., different Model Builder configuration), the agent will fail to invoke the model after deployment. Verify LLM API name alignment as a pre-deployment checklist item.
Exam-tested: Set 2 Q42.

**Flow activation dependency:**
⚠ Flows used as custom action implementations are included in the deployment package (metadata), but a flow must be **activated** in the target org to execute. A deployed-but-inactive flow → the custom action silently fails. This mirrors the agent inactive-on-deploy rule: both agent and its dependent flows require separate activation.
Exam-tested: Set 2 Q55 (flow not activated as root cause).

**What to consider when deploying agents (exam-tested checklist):**
1. All dependencies in the same change set (no orphan references)
2. LLM API name matches between source and target org
3. Agent deploys inactive — activate manually in production
4. Flows deploy but may need separate activation
5. Channels do NOT deploy — reconnect manually
6. Data Library indexes do NOT deploy — rebuild in production
7. Test in Testing Center BEFORE deploying (never deploy-then-test)

## OBJECTIVE 4 — TEMPLATE DEPLOYMENT (Sandbox → Production)

**Purpose:** Deploy prompt template metadata while understanding that activation is a separate step in the target org, and any Data Library or DMO grounding requires a rebuild — not just a metadata transfer.

✓ **Prompt templates deploy via metadata** (change sets / Metadata API) — they travel with the deployment, unlike agent Data Library content
✓ **Template versioning transfers:** the template's versions deploy, but **activation is a separate step in the target org** — a deployed template may arrive inactive/unactivated and must be activated in production before execution (mirrors the agent inactive-on-deploy rule)
✓ **Template dependencies deploy together:** grounding objects, flows used as grounding, and referenced fields must be present in the target org or resolution fails
⚠ **Cross-check:** template grounding that relies on a **Data Library or Data 360 DMO** faces the same rebuild constraint as agents — the *template metadata* deploys, but the *underlying data/indexes* must exist/rebuild in production.

**Binding rule:** template metadata (instructions, versions, grounding config) DEPLOYS; underlying Data Library indexes REBUILD; activation happens in the target org.

### Template Deployment Considerations

**What to consider when deploying prompt templates (exam-tested):**
1. Template metadata (instructions, versions, grounding config) deploys via change set
2. Activation is SEPARATE — deployed templates are NOT auto-activated
3. Grounding on Data Library/DMO requires index rebuild in target org
4. Running User FLS must be verified in the target org (different users, different permissions)
5. Referenced objects, fields, and flows must exist in target org or resolution fails

### Distractor bank
\# "Data Library deploys with the change set" = false; indexes rebuild
\# "Channel connections deploy" = false; rebuilt in production
\# "Agents/templates deploy active" = false; deploy inactive, activate manually
\# "Deploy first, then test" = false; Testing Center is the pre-production gate
\# "LLM API name doesn't matter in target org" = false; must match

### Layer-Config-Symptom
| Layer | Config Items | Typical Symptom If Missing |
|-------|--------------|------------------------------|
| **Agent Deployment** | Change sets / Metadata API / DevOps Center. DEPLOYS: agent definition, topics, instructions, action configs, flows, Apex, prompt templates, permission sets. REBUILDS: channel connections, Data Library content + search indexes, custom retrievers, activation state | Agent broken in production — missing dependency not included in change set / agent deployed but inactive (deploy ≠ activate) / channels not connected post-deploy |
| **Template Deployment** | Prompt template metadata (instructions, versions, grounding config) deploys via change set. Activation is separate step in target org. Grounding on Data Library/DMO faces same rebuild constraint | Deployed template inactive in production (not re-activated) / grounding fails (underlying Data Library indexes not rebuilt in target org) |
| **Deployment Sequence** | Build → Test (Testing Center) → Deploy (inactive) → Activate → Rebuild (data/connections) → Connect channels → Monitor | Any step skipped breaks downstream — testing skipped = defects in production, activation skipped = agent offline, rebuild skipped = no data/channels |

## DISTINCTION PAIRS
1. **Testing Center vs. Agent Builder preview** = batch evaluation vs. single-utterance interactive
2. **Deploys vs. rebuilds** = metadata travels vs. data/connections recreated
3. **Deploy vs. activate** = metadata transfer vs. go-live state (sequential)
4. **Agent deployment vs. template deployment** = agent metadata + rebuild data vs. template metadata + activate in target
5. **Change sets vs. Metadata API vs. DevOps Center** = point-and-click vs. programmatic vs. pipeline
6. **AI-generated vs. CSV-uploaded vs. Knowledge-generated test cases** = synthetic from definitions vs. manual curation vs. knowledge base analysis
7. **Topic/action match vs. Response quality** = deterministic comparison vs. LLM-as-judge evaluation
8. **Deploy-then-activate vs. Deploy-then-test** = correct (activate after deploy) vs. wrong (always test before deploy)

---
---

# GOVERNANCE & OBSERVABILITY (10%) — Study Notes v4-revised (Guide-Aligned)

> **v4-revised delta from v4:** Purpose one-liner added to both objectives. Agent Inspection, Session Tracing, and Agent Optimization distinguished as three separate Observability features in Obj 1. Intents and session metrics purpose added to Obj 2. All new items from Set 2 (SU26) carry ⚠ [VERSION_ALERT: SU26]. No existing content removed.

**Guide objectives:** (1) explain the process for **managing and monitoring agents** · (2) explain **agent analytics and agent optimization**.

> **Guide delta:** this content was previously folded into Testing/Deployment. The guide gives it its own 10% section. Same concepts, correctly re-homed here.

## OBJECTIVE 1 — MANAGING & MONITORING AGENTS

**Purpose:** Observe agent behavior across three distinct surfaces — aggregate performance (analytics), individual conversation flow (session traces), and AI content compliance (Trust Layer audit trail) — and know which surface answers which question.

### The monitoring stack (three layers, distinct purposes)
```
LAYER 1: Agent Analytics
   → adoption + performance dashboards
   → sessions, topic distribution, resolution rates, deflection, escalation rates
   → answers: "how is the agent performing at scale?"

LAYER 2: Session / Event Logs
   → utterance-level trace for individual conversations
   → answers: "what happened in this specific session?"

LAYER 3: Trust Layer Audit Trail → stored in Data 360
   → prompts, responses, masked-data records, toxicity scores
   → answers: "what did the LLM receive and return?"
```
**Three surfaces, three questions.** The exam conflates them as distractors — know which layer answers which question.

### Observability Features ⚠ [VERSION_ALERT: SU26]

The exam tests three **distinct** Observability features that V4 groups loosely. Each has a specific purpose:

**Agent Inspection:**
⚠ Provides **action-level** visibility into the agent's reasoning steps — which action was considered, why it was selected or rejected, what inputs it received, what it returned. Use when debugging a specific action's behavior within a conversation, NOT for aggregate performance.
Exam-tested: Set 2 Q13.

**Session Tracing:**
⚠ Provides **full conversation-level** visibility — the complete flow of a session from first utterance through topic routing, action invocation, grounding, response generation, and escalation. Use when you need end-to-end visibility into what happened in one session, NOT action-by-action inspection.
Exam-tested: Set 2 Q44.

**Agent Optimization:**
⚠ Provides **pattern-level** analysis — clusters similar misclassifications, groups recurring action failures, identifies systemic issues across many sessions. Use when you need to identify **what to fix**, NOT to debug a single session.
Exam-tested: Set 2 Q21.

**Three-level Observability binding:**
```
"What did this specific action do?"         → Agent Inspection    (action-level)
"What happened in this entire session?"      → Session Tracing     (conversation-level)
"What patterns appear across many sessions?" → Agent Optimization  (pattern-level)
"How is the agent performing at scale?"      → Agent Analytics     (aggregate metrics)
```

### Considerations for session processing ⚠ [VERSION_ALERT: SU26]

⚠ When evaluating Observability, consider that **session processing has latency** — session data, intents, and metrics are not available in real-time. Analytics dashboards reflect processed data, not live state. The exam tests whether you understand that Observability is post-hoc, not synchronous.
Exam-tested: Set 2 Q1.

### Distractor bank (the audit-surface conflation set — recurs across domains)
\# **Setup Audit Trail** = admin configuration changes only — NEVER AI interaction content
\# **Event Monitoring** = platform logs; requires Shield; not the generative AI observability surface
\# **Agent Analytics** = adoption metrics, NOT the AI content audit trail
\# **Prompt Builder version history** = template-level; not an agent monitoring surface
\# **Agent Inspection** when the stem asks "full session visibility" — that's Session Tracing
\# **Session Tracing** when the stem asks "cluster patterns across sessions" — that's Agent Optimization
\# **Agent Analytics** when the stem asks "debug one session" — that's Session/Event Logs

### Layer-Config-Symptom
| Layer | Config Items | Typical Symptom If Missing |
|-------|--------------|------------------------------|
| **Agent Analytics** | Post-deployment adoption + performance dashboards: sessions, topic distribution, resolution rates, deflection, escalation rates | No visibility into agent performance at scale / can't identify misclassification trends |
| **Session & Event Logs** | Utterance-level trace for individual conversations | Can't diagnose what happened in a specific session / no single-conversation debugging |
| **Trust Layer Audit Trail** | Prompts, responses, masked-data records, toxicity scores — stored in Data 360. NOT Setup Audit Trail (admin config changes only), NOT Event Monitoring (platform logs, requires Shield) | No compliance record of AI interactions / can't audit what LLM received and returned |

## OBJECTIVE 2 — AGENT ANALYTICS & AGENT OPTIMIZATION

**Purpose:** Close the feedback loop — use aggregate analytics to identify what's broken, then apply the optimization loop (diagnose → refine → re-test → re-deploy) to fix it, always passing through Testing Center before re-deployment.

✓ **Agent Analytics** = post-deployment adoption + performance dashboards (sessions, topic distribution, resolution/deflection, escalation)
✓ **Agent Optimization** ⚠ = analytics-driven refinement loop [VERIFY Spring '26 packaging under "Agentforce Observability"]
✓ **Topic classification description refinement** = the primary lever for fixing misclassification at scale
✓ **Testing Center re-run** = validates refinements before re-deployment

### The optimization loop (continuous improvement)
```
Monitor (Agent Analytics) → identify misclassified utterances / low-resolution topics
   ↓
Diagnose (Session logs / Testing Center re-run)
   ↓
Refine (topic classification descriptions, instructions, action configs)
   ↓
Re-test (Testing Center batch evaluation)
   ↓
Re-deploy (change set / Metadata API)
   ↓
Monitor again
```
**Binding rule:** the loop ALWAYS returns through Testing Center before re-deployment. Refinement without re-testing is never the correct answer.

### Intents and Session Metrics ⚠ [VERSION_ALERT: SU26]

**Why intents and session metrics matter:**
⚠ **Intents** = classified user objectives extracted from session data. Intents feed the optimization loop by revealing what users are actually asking for versus what topics are configured — a mismatch between high-frequency intents and configured topics signals a coverage gap.

⚠ **Session metrics** = per-session measurements (resolution, escalation, deflection, duration). Aggregated session metrics become the Agent Analytics dashboards. Individual session metrics feed Session Tracing for debugging.

**The reason they matter (exam-tested):** Intents and session metrics are the **input** to the optimization loop. Without them, you cannot identify misclassification patterns, coverage gaps, or performance degradation. They are the data that makes the Monitor→Diagnose→Refine→Re-test→Re-deploy cycle actionable.
Exam-tested: Set 2 Q54.

### Layer-Config-Symptom
| Layer | Config Items | Typical Symptom If Missing |
|-------|--------------|------------------------------|
| **Optimization Loop** | Monitor (Analytics) → Diagnose (Session logs / Testing Center re-run) → Refine (classification descriptions, instructions, action configs) → Re-test (Testing Center) → Re-deploy | Misclassification persists at scale / refinement deployed without re-testing (always wrong — loop must pass through Testing Center) |

## DECISION HEURISTICS
1. "Performance at scale / topic distribution / adoption" → **Agent Analytics**
2. "What happened in ONE session" → **Session / Event Logs**
3. "Prompts, responses, toxicity scores for compliance" → **Trust Layer audit trail → Data 360**
4. "Admin config changes" → **Setup Audit Trail** (never AI content)
5. "Fix misclassification at scale" → refine classification description → re-test → re-deploy
6. "What did this specific action do?" → **Agent Inspection** (not Session Tracing)
7. "Full visibility into one session's flow" → **Session Tracing** (not Agent Inspection)
8. "Cluster recurring failure patterns" → **Agent Optimization** (not Agent Analytics)
9. "What are users actually asking for?" → **Intents** (input to optimization loop)

## DISTINCTION PAIRS
1. **Agent Analytics vs. Trust Layer audit trail** = adoption metrics vs. AI content records
2. **Agent Analytics vs. Session Logs** = scale/aggregate vs. single-session detail
3. **Trust Layer audit trail vs. Setup Audit Trail** = AI interaction content (Data 360) vs. admin config changes
4. **Monitoring vs. Optimization** = observing performance vs. the refinement loop that acts on it
5. **Agent Inspection vs. Session Tracing** = action-level reasoning steps vs. full conversation flow
6. **Agent Optimization vs. Agent Analytics** = pattern clustering for what-to-fix vs. aggregate performance dashboards
7. **Intents vs. Topics** = what users actually ask (observed) vs. what the agent is configured to handle (designed)
8. **Session metrics vs. Agent Analytics** = per-session raw data vs. aggregated dashboards

---

# MULTI-AGENT ORCHESTRATION (5%) — Revised Proposals

**Guide objectives:** (1) determine whether a **Single-Agent (SOMA)** architecture is appropriate for scalability and control · (2) explain the purpose of open-standard multi-agent protocols such as **MCP** and **A2A**.

> **Guide delta:** LIGHTER than originally planned. **Agent API is NOT in this section** — it's an AI Agents objective (Objective 8 above). No A2A deep-dive — the guide asks only to "explain the purpose of" the protocols. 3 questions (5%) — surface-level definitions suffice.

> **Exam question map (6 questions across both sets):**
> - Set 1: 3 questions — A2A cross-platform (Purpose), SOMA central entry point (Scenario→Select), A2A cross-vendor (Pure Recall)
> - Set 2: 3 questions — A2A autonomous delegation (Purpose), SOMA scaling architecture (Scenario→Select), MCP primary purpose (Purpose)
> - **Stem pattern skew:** Purpose/Benefit = 50% of this domain (vs 14% overall). Aligns with guide wording: "explain the purpose of."

## OBJECTIVE 1 — SOMA (Single-Agent Architecture)

**Purpose:** Determine when a single agent handling many topics under one security/control context remains viable — and when scaling pressure demands a move to multi-agent.

**Guide:** "Determine whether a Single-Agent (SOMA) architecture is appropriate for scalability and control."

✓ **SOMA (Single-Agent architecture)** ⚠ = one agent handling many topics under one security/control context
✓ **When appropriate:** topics remain coherent within a single security context; centralized control is valued; cross-agent handoff latency is undesirable
✓ **When to move OFF SOMA (toward multi-agent):** topic count grows to the point classification descriptions compete and misclassification rises; multiple business units need separate ownership; different security contexts are required

### NEW — SOMA Decision Considerations
> **Why this matters:** 2 of 6 multi-agent questions are Scenario→Select asking which architecture to recommend. The decision criteria determine the answer.

| Stay SOMA | Move OFF SOMA |
|-----------|---------------|
| Single org, single security context | Multiple orgs or security contexts |
| Topics don't compete for classification | Classification descriptions overlap → misclassification |
| Centralized control valued | Business units need separate ownership |
| Handoff latency unacceptable | Specialization outweighs latency cost |
| One reasoning context sufficient | Federated reasoning contexts needed |

### NEW — Orchestrator Agent Pattern ⚠ [VERSION_ALERT: SU26]
> **Source:** Q49/Set2 — "Deploy a SOMA architecture using a primary Orchestrator agent to manage shared context natively and route sub-tasks to the specialized agents."

✓ **Orchestrator agent** = a primary agent within SOMA that interprets user intent and routes to specialized capabilities — NOT separate agents, but specialized topics/actions within one agent context
✓ The Orchestrator pattern preserves SOMA's centralized control while allowing internal specialization
✓ **Exam trap:** Q49/Set2 presents SOMA-with-Orchestrator as the answer for "centralized control + scalability" — the Orchestrator is the SOMA scaling mechanism, not a departure from it

### NEW — MOMA Distractor ⚠ [VERSION_ALERT: SU26]
> **Source:** Q49/Set2 distractor — "Implement a Multi-Org, Multi-Agent (MOMA) architecture connected via A2A."

✓ **MOMA (Multi-Org Multi-Agent)** = each agent in a separate Salesforce org, connected via A2A protocol
✓ **When MOMA fits:** separate orgs already exist (M&A, regional isolation, regulatory separation)
✓ **Why MOMA is wrong in Q49:** GFC operates in a single global instance — MOMA introduces unnecessary org boundaries, breaks shared context, adds A2A overhead
✓ **Exam trap:** MOMA sounds sophisticated but violates the constraint ("single Salesforce instance" + "centralized control" + "customers don't repeat themselves")

**Binding rule:** SOMA = centralized control, one reasoning context, avoids handoff latency. Scaling pressure + rising misclassification + separate-ownership needs = argument for multi-agent. Orchestrator agent = SOMA's internal scaling mechanism. MOMA = multi-org, only when org boundaries already exist. ⚠ [VERIFY] the exact "SOMA" and "MOMA" labels against Spring '26 — the concepts (single-agent vs multi-org tradeoff) are exam-safe.

### Considerations — SOMA Architecture Decision
1. **Single-org constraint** — if the question states "single Salesforce instance," MOMA is automatically wrong
2. **"Centralized control" keyword** — signals SOMA, not distributed multi-agent
3. **"Customers don't repeat themselves"** — signals shared context = SOMA (MOMA breaks shared context)
4. **Rising misclassification** — the canonical trigger for evaluating a move OFF SOMA
5. **"Separate ownership" + "different security contexts"** — the canonical triggers for multi-agent/MOMA

### Layer-Config-Symptom
| Layer | Config Items | Typical Symptom If Missing |
|-------|--------------|------------------------------|
| **SOMA** | Single-Agent architecture — one agent, many topics, one security/control context. Move OFF SOMA when: topic count causes classification competition, separate ownership needed, different security contexts required | Rising misclassification as topics grow / inability to separate business-unit ownership / security context conflicts |
| **MOMA** ⚠ | Multi-Org Multi-Agent — each agent in a separate org, connected via A2A. Only when org boundaries already exist | Unnecessary org boundaries / broken shared context / A2A overhead when single-org would suffice |

## OBJECTIVE 2 — PROTOCOLS (MCP + A2A)

**Purpose:** Distinguish MCP (agent→tools/data) from A2A (agent→agent) as disjoint open standards — and know that Agent API (app→agent) belongs to AI Agents, not here.

**Guide:** "Explain the purpose of open-standard multi-agent protocols such as MCP and A2A."

Bind as a disjoint pair — different arrows, no overlap:

✓ **MCP (Model Context Protocol)** = open standard connecting an agent to **tools and data** (agent → tools). Purpose: standardized tool discovery + invocation instead of bespoke integrations.
✓ **A2A (Agent-to-Agent protocol)** = open standard for **agent ↔ agent** communication and task delegation across platforms/vendors. Purpose: one agent delegates to or coordinates with another agent, including non-Salesforce agents.

```
agent → tools  = MCP
agent → agent  = A2A
app   → agent  = Agent API   (NOTE: this lives in AI Agents Objective 8, not here)
```

### NEW — MCP Purpose Framing (exam-tested) ⚠ [VERSION_ALERT: SU26]
> **Source:** Q64/Set2 — "What is the primary purpose of using an open standard like MCP?"

✓ **MCP primary purpose (exam-safe phrasing):** "To standardize the secure connection and delivery of context between the AI models and various local or remote data sources"
✓ Key words: **standardize** (not build custom), **secure connection** (not open access), **delivery of context** (not raw data dump), **local or remote** (both)
✓ MCP replaces bespoke integrations — the "instead of building custom integration logic and bespoke APIs for each new data source" framing is the exam's own language

**Distractor traps from Q64/Set2:**
- "Replace the need for RAG by storing all external data natively within the LLM's weights" — FALSE. MCP delivers context TO the model, it doesn't replace RAG
- "Allow the agent to autonomously negotiate task delegation with third-party agents" — FALSE. That's A2A, not MCP. Classic arrow-swap trap

### NEW — A2A Purpose Framing (exam-tested) ⚠ [VERSION_ALERT: SU26]
> **Source:** Q Set2 (A2A delegation) — "Which open standard multi-agent protocol is specifically designed to facilitate this autonomous task delegation and negotiation between independent AI agents?"

✓ **A2A differentiating keywords:** "autonomous task delegation," "negotiation," "between independent AI agents," "cross-platform," "cross-vendor"
✓ A2A = agents as peers that communicate, delegate, and negotiate — not tools being invoked
✓ **OpenAPI Specification (OAS)** appears as a distractor in Set 2 — OAS describes REST APIs, not agent-to-agent peer communication

### NEW — Three-Arrow Binding (cross-domain exam trap)
> **Source:** Pattern across both sets — Agent API placement is the highest-frequency cross-domain distractor in Multi-Agent questions.

| Arrow | Protocol | Domain |
|-------|----------|--------|
| agent → tools/data | **MCP** | Multi-Agent Orchestration |
| agent → agent | **A2A** | Multi-Agent Orchestration |
| app → agent | **Agent API** | AI Agents (Objective 8) — NOT here |

✓ The three arrows are **disjoint** — no protocol serves two arrows
✓ **Exam trap frequency:** Agent API placed in Multi-Agent domain = the #1 cross-section distractor. If a Multi-Agent question offers "Agent API" as an option for cross-platform agent communication, it's wrong — Agent API is app→agent, not agent→agent

### Considerations — Protocol Selection
1. **"Cross-platform" + "cross-vendor" + "third-party agent"** → A2A (agent→agent)
2. **"External data sources" + "tools" + "standardize integrations"** → MCP (agent→tools)
3. **"Invoke your agent from an app/external system"** → Agent API (AI Agents domain, not here)
4. **"Autonomous task delegation and negotiation"** → A2A (not MCP — MCP doesn't negotiate)
5. **"Replace bespoke integrations / custom APIs"** → MCP (standardization is MCP's purpose)

**Binding rule:** MCP connects an agent OUT to tools; A2A connects an agent to ANOTHER agent. Agent API (invoke *your* agent from an app) belongs to the AI Agents domain — a classic cross-section distractor. MCP does NOT replace RAG, and A2A does NOT invoke tools.

### Distractor bank
\# **Agent API** placed in Multi-Agent — it's an AI Agents objective; the trap is domain-placement
\# Swapping the arrowheads: "MCP = agent-to-agent" (false — that's A2A) or "A2A = agent-to-tools" (false — that's MCP)
\# Treating SOMA as "required" — it's a tradeoff decision, not a mandate
\# **OpenAPI Specification** as A2A substitute — OAS describes REST APIs, not agent peer protocols ⚠ [VERSION_ALERT: SU26]
\# **MCP replaces RAG** — false; MCP delivers context to the model, RAG retrieves from indexed knowledge ⚠ [VERSION_ALERT: SU26]
\# **MOMA in single-org scenario** — MOMA requires multiple orgs; if question says single org, MOMA is automatically wrong ⚠ [VERSION_ALERT: SU26]

## DISTINCTION PAIRS
1. **MCP vs. A2A** = agent→tools vs. agent→agent
2. **A2A vs. Agent API** = cross-platform agent peer protocol vs. app-invokes-your-agent (different domains)
3. **SOMA vs. multi-agent** = one reasoning context/centralized control vs. federated specialized agents
4. **SOMA vs. MOMA** = single-org multi-topic agent vs. multi-org separate agents connected via A2A ⚠ [VERSION_ALERT: SU26]
5. **MCP vs. OpenAPI Specification** = AI agent context delivery standard vs. REST API description standard ⚠ [VERSION_ALERT: SU26]
6. **A2A (delegation) vs. MCP (invocation)** = agent negotiates with peer vs. agent calls a tool — "negotiate" = A2A, "invoke" = MCP
7. **Orchestrator agent vs. multi-agent** = internal SOMA routing mechanism vs. separate agents with separate contexts ⚠ [VERSION_ALERT: SU26]

## CALIBRATED CONFIDENCE
**HIGH:** MCP purpose (agent→tools) · A2A purpose (agent→agent) · the domain-placement of Agent API (AI Agents, not here) · SOMA core tradeoff (centralized control vs. scaling pressure).
**MODERATE ⚠:** exact "SOMA" and "MOMA" terminology — concepts exam-safe, labels training-edge. Orchestrator agent pattern sourced from Set 2 only (n=1 sitting). Prioritize the scalability/control tradeoff over memorizing acronyms.
**LOW:** OpenAPI Specification as distractor — appeared once (Set 2 only). Worth recognizing, not worth drilling.

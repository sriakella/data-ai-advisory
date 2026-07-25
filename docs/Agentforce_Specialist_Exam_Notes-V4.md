# AI Agents (35%) — Study Notes v3 (Guide-Aligned)

**Exam:** Salesforce Certified Agentforce Specialist (AI-201), Spring '26
**Domain weight:** 35% (~21 of 60 questions — the single largest block)
**Source:** aligned to the uploaded official Exam Guide — 8 stated objectives
**Scope flags:** ✓ exam-safe (guide-grounded) · ⚠ training-edge, primary-source before exam · # distractor (wrong layer/scope) · ❌ nonexistent/colloquial

> **v3 delta from v2:** Objective 1 (Hybrid Reasoning + Agent Script) deepened to first-class weight; template expressions confirmed in determinism; Voice promoted to exam-safe; **Sales Agent removed (out of scope — guide tests Employee vs. Service only)**; Agent API kept as its own objective; calibrated-confidence + distinction-pairs added.

**The 8 guide objectives:**
1. How an agent works + basic building blocks of Agent Script
2. Hybrid reasoning components/benefits — Agent Script in Canvas vs. Script View
3. Deterministic behavior via filters, variables, **template expressions**
4. Select/configure standard + custom topics, standard + custom actions
5. Connect agents to channels: digital experience, email, **voice**, Slack
6. Security context the agent runs in + impact on action execution
7. When to use an **Employee or Service** agent
8. When to use **Agent API**

---

## THE ARCHITECTURE STACK (Top-Down Diagnosis Spine)

```
Business Need
   ↓
Agent Type          (Employee / Service — who is the audience?)
   ↓
Agent               (the AI worker, authored via NGA)
   ↓
Topic               (business capability — "what can the agent do?")
   ↓
Action              (standard / custom — the executable operation)
   ↓
Hybrid Reasoning    (Agent Script rails + Canvas/Script View + LLM logic)
   ↓
Execution           (deterministic constraints + probabilistic generation)
```

**Top-down diagnosis heuristic:**
- Wrong business task selected? → **Topic** (classification description)
- Wrong operation invoked? → **Action**
- Wrong hard-coded decision? → **Deterministic mechanism** (filter / variable / template expression)
- Wrong language generation? → **LLM / prompt**
- Wrong underlying data? → **Grounding / retriever / Data Library**

---

## OBJECTIVE 1 — FUNDAMENTALS, AGENT SCRIPT & HYBRID REASONING

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

### The Agent (NGA) Lifecycle
✓ `Ideation → Building → Testing → Deployment → Observation`
**Do NOT confuse with the Prompt Template lifecycle** (Build → Preview → Activate → Invoke). "Preview" is a template word; agents use Testing Center.

**⚠ [VERIFY] before exam:** Agent Script syntax specifics, exact Canvas/Script View mechanics, and hybrid-reasoning internal wording sit at the training edge. The *concepts* (visual vs. code view; deterministic + probabilistic together) are exam-safe; exact syntax is not — primary-source the Spring '26 guide and Trailhead.

### Distractor bank
\# **LLM** as the planner — it generates language, it does not plan the workflow
\# **Prompt** as the agent structure — the prompt is assembled input, not the Agent Script
\# Prompt Template lifecycle terms ("Preview," "Activate/Invoke") mislabeled as the agent lifecycle

### Layer-Config-Symptom
| Layer | Config Items | Typical Symptom If Missing |
|-------|--------------|------------------------------|
| **Authoring** | NGA (Next-Generation Authoring), Canvas View, Script View, Agent Script Building Blocks, block sequencing/transitions | Agent starts but doesn't execute blocks — conversation ends early without action attempt, or wrong Canvas branch chosen |

---

## OBJECTIVE 2 — DETERMINISTIC BEHAVIOR (Filters, Variables, Template Expressions)

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

### Distractor bank
\# **Instructions** = the trap for any "must always/never" stem — probabilistic
\# **LLM temperature** = generation tuning, not a workflow control
\# **Topic classification description** = influences *selection*, not hard enforcement of a value

### Layer-Config-Symptom
| Layer | Config Items | Typical Symptom If Missing |
|-------|--------------|------------------------------|
| **Deterministic Control** | Variables, Filters, Template Expressions | Same input produces different output on repeat runs — condition evaluated inconsistently |

---

## OBJECTIVE 3 — TOPICS & ACTIONS (Standard / Custom)

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

### Distractor bank
\# **Intent** = user objective, not a configured capability (Topic distractor)
\# **Flow / Apex** = *implementations* of a custom action, offered as distractors for "Action"
\# **Prompt template** = can *be* a custom action, but is a Prompt Engineering artifact — wrong when the question wants the agent-layer construct

### Layer-Config-Symptom
| Layer | Config Items | Typical Symptom If Missing |
|-------|--------------|------------------------------|
| **Topic Routing** | Standard/Custom Topics, Classification Descriptions, Topic Instructions (probabilistic guidance within a matched topic — distinct from Classification Descriptions which drive matching), Agent Router, Training Data Quality | Right topic not matched / inconsistent classification across similar utterances / agent matched correct topic but behaves inconsistently within it (Topic Instructions too vague) |
| **Action Config** | Standard/Custom Actions, Action Typology (Atomic/Composite, Deterministic/Prompt-based). Custom action sources: Flow, Invocable Apex, Prompt Templates, External Services/APIs | Right topic selected but wrong action invoked, or only one action fires on a compound request |

---

## OBJECTIVE 4 — SECURITY CONTEXT & THE AGENT USER

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

### Distractor bank
\# **Running User** = Prompt Builder merge-field resolution — the #1 agent-security distractor; never the answer for agent execution context
\# **Permission set alone** = grants access, but the *identity the agent acts as* is Agent User / logged-in user
\# **Integration User / Named Credential** = integration-layer identities, wrong layer

### Layer-Config-Symptom
| Layer | Config Items | Typical Symptom If Missing |
|-------|--------------|------------------------------|
| **Security** | Agent User record, CRUD/Permission Set grants (action execution) — NOT FLS, which governs reasoning-engine data visibility (Prompt Engineering) | Action executes without error but data doesn't change / silent permission failure |

---

## OBJECTIVE 5 — AGENT TYPES (Employee vs. Service)

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

### Distractor bank
\# Choosing **Service Agent** for internal IT/HR (internal audience → Employee)
\# Choosing **Employee Agent** for external customer support (external → Service)
\# **Sales Agent / Copilot / Chatbot** = not exam-safe answers for this objective

### Layer-Config-Symptom
| Layer | Config Items | Typical Symptom If Missing |
|-------|--------------|------------------------------|
| **Persona** | Employee Agent, Service Agent (only two types) | Agent behaves incorrectly for use case — Service agent accessing employee data, or Employee agent visible to customers |

---

## OBJECTIVE 6 — CHANNELS

**Guide:** "connecting agents to various channels such as digital experience, email, voice, and Slack."

✓ **Digital experience** = Messaging for In-App and Web / Experience Cloud embed
✓ **Email** = inbound email conversations
✓ **Voice** = synchronous telephony integration (now exam-safe per guide — no longer [VERIFY])
✓ **Slack** = agent surfaced as a Slack app / collaboration surface
✓ **Omni-Channel flow** = routes HITL escalations to the right human-agent queue

### Distractor bank
\# **Lightning App / Record Page / Console** = UI surfaces, not agent channels
\# **"Retry forever"** = never the HITL answer; HITL escalates via Omni-Channel, it does not loop

### Layer-Config-Symptom
| Layer | Config Items | Typical Symptom If Missing |
|-------|--------------|------------------------------|
| **Channel & Linkage** | Communication Channels, Flow Definition Field, Service Channel, Telephony Connection, Fallback Queue vs. HITL | Agent deployed & active but invisible in channel — channel doesn't trigger agent, user can't reach agent |

---

## OBJECTIVE 7 — AGENT API

**Guide:** "identify when it's appropriate to use Agent API." (Its own objective — NOT lumped under channels, NOT under Multi-Agent.)

✓ **Agent API** = programmatic/headless surface to invoke an Agentforce agent
**When appropriate:**
- A channel **not** natively supported by Salesforce (custom mobile app backend, proprietary terminal/portal)
- Headless integration / custom UI surface
- Bridging into external open-standard multi-agent systems

**Binding rule:** "no standard channel fits / custom or external surface / headless" → **Agent API**. If a standard channel (digital experience, email, voice, Slack) fits, use that instead.

### Distractor bank
\# Using Agent API when a standard channel already fits — over-engineering
\# Confusing Agent API (invoke *an agent*) with MCP (agent → tools) or A2A (agent → agent)

### Layer-Config-Symptom
| Layer | Config Items | Typical Symptom If Missing |
|-------|--------------|------------------------------|
| **External** | Agent API, authentication, payload structure, connection | External system can't invoke agent — API call rejected, payload not accepted, no response |

---

## QUICK DIAGNOSTICS — PARETO KEYWORD MAP

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

---

## DISTINCTION PAIRS (retention-close set)

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

---

## CALIBRATED CONFIDENCE (risk zones)

**HIGH confidence (guide-grounded, drill-confirmed):**
Topic vs. Action · Employee vs. Service selection · Agent User security context · deterministic vs. probabilistic (filters/variables vs. instructions) · reasoning engine vs. LLM · channels including Voice · Agent API when-to-use.

**MODERATE / ⚠ (training-edge — primary-source before exam):**
Agent Script syntax · exact Canvas vs. Script View mechanics · hybrid-reasoning internal wording · template-expression syntax. Prioritize the *concept* over release-specific naming; if an option describes "visual declarative authoring" that is Canvas regardless of exact label.

**Removed as out-of-scope:** Sales Agent (guide tests Employee/Service only).

---

## STEELMAN GAP-CHECK (easy to under-study)

- **Objective 1 (Agent Script / Canvas / Script View)** — newly first-class, previously under-drilled; **your biggest live gap**. Allocate disproportionate Wave 2 attention here.
- **Template expressions** — third deterministic mechanism, near-zero prior drilling.
- **Voice channel** — now exam-safe; don't treat as edge.
- **Agent API as its own objective** — bind the "when to use" decision separately from channels and from MCP/A2A.
- **Security impact on action execution** — the *silent-failure* framing (correct action, missing FLS → nothing returned) is exam-tested and easy to miss.

---
---

# PROMPT ENGINEERING (20%) — Study Notes

**Exam:** Salesforce Certified Agentforce Specialist (AI-201), Spring '26
**Domain weight:** 20% (~12 of 60 questions)
**Status:** ✅ MASTERED — Wave 2: 11/12 (91.7%)
**Scope flags:** ✓ exam-safe · ⚠ training-edge, primary-source before exam · # distractor (wrong layer/scope) · ❌ nonexistent/colloquial

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

✓ **Prompt Builder** = the tool for building **generated, dynamic** content grounded in Salesforce data
✓ **Use when:** content must be generated fresh per execution, grounded in records/data, admin-maintained, reusable
✓ **Do NOT use when:** content is static and identical for every recipient — that's a standard Lightning email template, not a prompt template

**Decision heuristic:** "identical wording, no generation, brand-approved" → static template. "Generated fresh, grounded in data, varies per execution" → Prompt Builder.

### Distractor bank
\# **Lightning email template** = correct answer when NO generation is needed — the trap is reaching for Prompt Builder by default
\# **Flow with email alert** = static logic, not generative

### Layer-Config-Symptom
| Layer | Config Items | Typical Symptom If Missing |
|-------|--------------|------------------------------|
| **Template Type** | Flex (multi-unrelated inputs OR programmatic invocation), Field Generation (single-field ✨ button), Record Summary (single-record page), Sales Email (recipient + optional related object) | Wrong template surfaced in wrong context / template unavailable where expected |

---

## OBJECTIVE 2 — ACCESS CONTROLS

✓ **Prompt Template User** = execute-only; runs active templates
✓ **Prompt Template Manager** = create, edit, manage templates in Prompt Builder
✓ **Einstein Generative AI Admin** = org-level generative AI setup; above build/run
✓ **Running user's FLS/object access** gates merge-field resolution — templates never execute in system context

**Binding rule:** Run = User + data access (two independent requirements). Build = Manager. Administer = Generative AI Admin. Three tiers, no overlap.

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

Bind as triples: **type → input shape/anchor → surface**

✓ **Flex** = (1) multiple unrelated input resources selected at run time OR (2) programmatic invocation surface (flow action / invocable Apex / agent action, e.g. automated generation with no user click)
✓ **Field Generation** = ✨-button-on-a-single-field via Lightning App Builder
✓ **Record Summary** = single-record page context, one-click, non-conversational
✓ **Sales Email** = recipient (lead/contact) + optional related object, surfaced in the email composer

**Anti-greed rule:** Flex fires ONLY on multi-unrelated-inputs OR automation — it is the last resort, not the default. Match the type to the constraint boundary of the use case; the correct answer is always the **most constrained type that still works**.

### Distractor bank
\# **Einstein Work Summaries** = channel-level service feature — never field/record-page
\# **Record Summary as flow-invocable** = false; it's a page component only
\# **Field Generation for multi-object input** = false; it's single-field only

### Layer-Config-Symptom
| Layer | Config Items | Typical Symptom If Missing |
|-------|--------------|------------------------------|
| **Template Type** | Flex (multi-unrelated inputs OR programmatic invocation), Field Generation (single-field ✨ button), Record Summary (single-record page), Sales Email (recipient + optional related object) | Wrong template surfaced in wrong context / template unavailable where expected |

---

## OBJECTIVE 4 — GROUNDING TECHNIQUES

The ladder — **lowest rung that satisfies the requirement wins:**

```
Merge fields             → direct field values from the anchor record
Related-list grounding   → child records displayed as-is
Template-triggered flow  → aggregation/logic (count, sum, filter) merge fields can't compute
Agentforce Data Library  → chunk-index-retrieve; semantic search over unstructured content
Data 360 DMO grounding   → zero-copy structured retrieval at run time, no replication
```

### Distractor bank
\# **Data Cloud Retrieval Action** = Agentforce **action layer** (structured, keyed on Unified Individual ID, injects into an **agent's** prompt context) — NOT Prompt Builder grounding
\# **Activation Target** = outward segment publishing; zero grounding role
\# **"RAG"** = architect vocabulary; exam-safe label is **Agentforce Data Library**

### Layer-Config-Symptom
| Layer | Config Items | Typical Symptom If Missing |
|-------|--------------|------------------------------|
| **Grounding** | Merge fields → Related-list grounding → Template-triggered flow (aggregation/logic) → Agentforce Data Library (semantic search, unstructured) → Data 360 DMO (zero-copy structured) | Fabricated content (hallucination) — LLM generates without grounded data / wrong data injected / aggregation missing |

---

## OBJECTIVE 5 — LIFECYCLE (Create, Activate, Execute)

```
Build → Preview (sample-record test, pre-activation) → Activate (go-live gate) → Invoke
```

✓ **Preview** = build-time resolution test with a sample record — NOT called "Validate"
✓ **Version** = every edit creates a new version; served output changes only on activation
✓ **Activation** = the go-live gate; invocation requires active status

**Binding rule:** "edited but nothing changed" = new version not activated. Never caching, never a deploy requirement, never a lock.

### Distractor bank
❌ **"Validate"** as a lifecycle step — the product label is Preview
\# **Trust Layer caching** = never the explanation for stale output after an edit

### Layer-Config-Symptom
| Layer | Config Items | Typical Symptom If Missing |
|-------|--------------|------------------------------|
| **Lifecycle** | Build → Preview (sample-record test, pre-activation) → Activate (go-live gate) → Invoke. Versioning: every edit = new version, served only on activation | Edited template but output unchanged (new version not activated) / template can't be invoked (not activated) |

---

## OBJECTIVE 6 — BEST PRACTICES

✓ **Constrain to grounded data** = explicit instructions telling the model to only use provided grounding and state when information is unavailable — fixes **hallucination** (fabricated content)
✓ **Specify output structure** = explicit format/section requirements — fixes inconsistent formatting
✓ **Role + task + constraints, specific language** = the canonical instruction pattern

**Binding rule:** Hallucination (fabricated facts) → fix with constraining instructions. Toxicity (harmful/unsafe language) → fix with toxicity scoring. Different problems, different mechanisms — never interchange them.

### Distractor bank
\# **Toxicity scoring for hallucination** = wrong dimension; toxicity ≠ factual accuracy
\# **Lower token limit** = does not fix fabrication, only truncates output

### Layer-Config-Symptom
| Layer | Config Items | Typical Symptom If Missing |
|-------|--------------|------------------------------|
| **Instructions** | Probabilistic behavior guidance: role + task + constraints pattern, constrain-to-grounded-data directive, output structure specification | Inconsistent formatting / hallucinated facts (fix: constraining instructions, NOT toxicity scoring) |

---

## OBJECTIVE 7 — TRUST LAYER

✓ **Data masking** = mask PII before the prompt leaves Salesforce; **demask in the returned response** (the full round trip — both halves required)
✓ **Zero data retention** = external provider stores nothing, trains on nothing (exact trigram)
✓ **Toxicity scoring** = detects harmful/offensive/unsafe generated language — NOT hallucinations
✓ **Audit trail** = prompts, responses, masked-data records, toxicity scores → stored in **Data 360**
✓ **Prompt defense** = injection protections on the prompt envelope
✓ **Applies org-wide** = every generation request, prompt templates AND agent responses, regardless of model

### Distractor bank
\# **Data Cloud dynamic data masking** = display-layer rendering, different product surface
\# **Shield Platform Encryption** = at-rest encryption, not LLM-boundary masking
\# **Setup Audit Trail** = admin config changes only — never AI interaction content (recurring trap across all domains)
\# **Event Monitoring** = platform logs, requires Shield, not the generative AI observability surface

### Layer-Config-Symptom
| Layer | Config Items | Typical Symptom If Missing |
|-------|--------------|------------------------------|
| **Trust Layer** | Data masking (mask before LLM + demask in response — full round trip), Zero data retention, Toxicity scoring (harmful language — NOT hallucination), Audit trail (stored in Data 360), Prompt defense (injection protection) | PII exposed to external LLM / harmful language passes through / no compliance audit record / prompt injection succeeds |

---

## OBJECTIVE 8 — MODEL MANAGEMENT

✓ **Model Builder (Einstein Setup)** = enable/restrict/register foundation models; disable a model → unavailable for template selection org-wide
✓ **BYOLLM** = register a customer-tenancy model (e.g., their own Bedrock) via Model Builder

**Binding rule (layer separation):** Model Builder governs **WHICH** models are available. Trust Layer governs **HOW** data is protected, regardless of model. Orthogonal control surfaces — never the same Setup node.

### Distractor bank
❌ **"They are the same configuration surface exposed in two Setup nodes"** = false — Model Builder and Trust Layer are always orthogonal

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

---

## CALIBRATED CONFIDENCE

**HIGH:** Template type triples · access control tiers · grounding ladder · lifecycle sequence · Trust Layer feature set · hallucination vs. toxicity discrimination. All drill-confirmed at 91.7%+.

**MODERATE ⚠:** none — this domain has no [VERIFY] flags; fully mastered against the exam guide.

---
---

# DATA 360 FUNDAMENTALS (20%) — Study Notes

**Exam:** Salesforce Certified Agentforce Specialist (AI-201), Spring '26
**Domain weight:** 20% (~12 of 60 questions)
**Status:** ✅ MASTERED — Wave 3: 6/6 (100%)
**Scope flags:** ✓ exam-safe · ⚠ training-edge, primary-source before exam · # distractor (wrong layer/scope) · ❌ nonexistent/colloquial

**Two guide objectives (the guide collapses this domain to two broad statements):**
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

✓ **Agentforce Data Library** = managed automation layer that provisions chunking + search index + default retriever **inside Data Cloud** — simplifies, never replaces
✓ **Knowledge-based library** = syncs published Knowledge articles; respects article lifecycle (publish/unpublish flows to index, with re-sync latency)
✓ **File-based library** = uploaded documents (PDFs); no Knowledge dependency
✓ **Manual pipeline** = custom chunking + custom index + tuned retriever; finer control; **coexists** with managed libraries — neither is mandatory or deprecated

**Decision heuristic:** Published Knowledge articles → Knowledge-based. External PDFs, no Knowledge plan → File-based. Existing tuned manual pipeline → evaluate tuning loss before migrating; do NOT tear down by default.

### Distractor bank
❌ **"Library replaces Data Cloud"** = false; library is built ON Data Cloud
❌ **"Libraries are mandatory as of Spring '26"** = false; both paths coexist
\# **"Library indexes in the Agentforce runtime"** = false; indexing happens in Data Cloud's search index

### Layer-Config-Symptom
| Layer | Config Items | Typical Symptom If Missing |
|-------|--------------|------------------------------|
| **Library Type** | Knowledge-based library (syncs published Knowledge articles), File-based library (uploaded PDFs), Manual pipeline (custom chunking + index + tuned retriever — coexists with managed, neither mandatory) | Wrong content source indexed / Knowledge articles not syncing (publish/unpublish lifecycle) / existing tuned pipeline torn down unnecessarily |

---

## OBJECTIVE 2 — CHUNKING & INDEXING

✓ **Chunking** = splitting unstructured content into passages before embedding
✓ **Indexing** = embedding chunks into a Data Cloud search index as vectors
✓ **Vector embedding** = numerical representation of a chunk that **encodes semantic meaning** — similar meaning = close in vector space = similarity-based retrieval without exact term matches

### Chunk size trade-off (two-sided)
| Direction | Effect | Fixes |
|---|---|---|
| **Smaller chunks** | Higher precision, narrower passages | Agent misses specific details in long docs |
| **Larger chunks** | More context, less dilution | Agent returns disconnected fragments |

**Binding rule:** the fix is always **upstream in chunking**, never downstream in post-processing (instructions/flows).

### Distractor bank
\# **Vector embedding as a unique identifier** = false; it's a meaning-map, not a label — this fault recurred across multiple drills, treat as high-risk
\# **Template-triggered prompt flow for chunking fixes** = wrong pipeline (Prompt Builder grounding, not agent retrieval)

### Layer-Config-Symptom
| Layer | Config Items | Typical Symptom If Missing |
|-------|--------------|------------------------------|
| **Chunking** | Chunk size configuration (smaller = higher precision, narrower passages; larger = more context, less dilution) | Agent misses specific details in long docs (chunks too large) / agent returns disconnected fragments (chunks too small) — fix is always upstream in chunking, never downstream |
| **Indexing** | Search index, vector embeddings (numerical representation encoding semantic meaning — similar meaning = close in vector space) | No retrieval possible / content ingested but not searchable (not yet embedded into index) |

---

## OBJECTIVE 3 — RETRIEVERS (Individual / Ensemble)

✓ **Individual retriever** = one retriever against one search index
✓ **Default retriever** = auto-created per search index; shared baseline for **ALL** consumers — never edited for a scoped use case (global impact)
✓ **Custom retriever** = purpose-built on the **same index**, with metadata filters, referenced by a **specific agent action**
✓ **Ensemble retriever** = combines multiple individual retrievers; **merges and re-ranks** into a single unified result set — a composition mechanism, NOT a fourth search type
✓ **Metadata filters** = query-time restriction (region, tier, category) — configured on **custom** retrievers only

### Three binding rules
1. **Index ≠ retriever** — one index serves many retrievers
2. **Default ≠ custom** — editing the default has global impact; create a custom instead
3. **Metadata filter ≠ structural separation** — one index + filter beats per-segment indexes

### Distractor bank
❌ **"Edit the default retriever for scoped use"** = global impact on all consumers
❌ **"Create per-region search indexes"** = over-engineered when a metadata filter solves it
❌ **"Ensemble is a search type"** = false; it's a composition mechanism operating ON search types

### Layer-Config-Symptom
| Layer | Config Items | Typical Symptom If Missing |
|-------|--------------|------------------------------|
| **Retriever** | Default retriever (auto-created, shared baseline — never edit for scoped use, global impact), Custom retriever (scoped, with metadata filters, per-action), Ensemble retriever (composition mechanism merging/re-ranking multiple retrievers — NOT a search type) | Irrelevant category results (missing metadata filter on custom retriever) / global retrieval behavior broken (default retriever edited) / mixed-corpus results not merged (no ensemble) |

---

## OBJECTIVE 4 — SEARCH TYPES (Keyword / Vector / Hybrid)

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

---

## CALIBRATED CONFIDENCE

**HIGH:** Library types · chunking trade-off · retriever architecture (individual/default/custom/ensemble) · search type selection · structured/unstructured path separation. All drill-confirmed at 100% under scenario pressure (Wave 3).

**MODERATE ⚠:** none — this domain has no [VERIFY] flags; fully mastered against the exam guide.

---
---

# TESTING, DEPLOYMENT & MAINTENANCE (10%) — Study Notes

**Guide objectives:** (1) test an agent using Testing Center · (2) explain how Testing Center evaluations work · (3) considerations for deploying an **agent** sandbox→production · (4) considerations for deploying a **template** sandbox→production.

> **Guide delta:** adoption monitoring / analytics / optimization has MOVED to the **Governance & Observability** section (below). This section is now scoped to Testing Center + deployment only.

## OBJECTIVE 1–2 — AGENTFORCE TESTING CENTER

✓ **Agentforce Testing Center** = batch evaluation harness for agents, used **pre-production in sandbox**; a pre-deployment quality gate, NOT a monitoring tool
✓ **Purpose:** validate the agent selects the correct **topic** and correct **action** for a given utterance — at scale, before any user sees it

### The four-step sequence (bind cold)
```
Generate test cases  (AI-generated OR CSV-uploaded utterances)
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

### Test case sources (two paths, same engine)
✓ **AI-generated** = synthetic utterances from topic/action definitions
✓ **CSV-uploaded** = manually curated utterances imported in bulk

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

### Layer-Config-Symptom
| Layer | Config Items | Typical Symptom If Missing |
|-------|--------------|------------------------------|
| **Testing Center** | Agentforce Testing Center (batch evaluation harness, pre-production in sandbox), AI-generated test cases, CSV-uploaded test cases, topic match + action match + response quality evaluation | Misclassification or wrong action not caught before production / no pre-deployment quality gate / "deploy first, test in production" anti-pattern |

## OBJECTIVE 3 — AGENT DEPLOYMENT (Sandbox → Production)

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

## OBJECTIVE 4 — TEMPLATE DEPLOYMENT (Sandbox → Production)

✓ **Prompt templates deploy via metadata** (change sets / Metadata API) — they travel with the deployment, unlike agent Data Library content
✓ **Template versioning transfers:** the template's versions deploy, but **activation is a separate step in the target org** — a deployed template may arrive inactive/unactivated and must be activated in production before execution (mirrors the agent inactive-on-deploy rule)
✓ **Template dependencies deploy together:** grounding objects, flows used as grounding, and referenced fields must be present in the target org or resolution fails
⚠ **Cross-check:** template grounding that relies on a **Data Library or Data 360 DMO** faces the same rebuild constraint as agents — the *template metadata* deploys, but the *underlying data/indexes* must exist/rebuild in production.

**Binding rule:** template metadata (instructions, versions, grounding config) DEPLOYS; underlying Data Library indexes REBUILD; activation happens in the target org.

### Distractor bank
\# "Data Library deploys with the change set" = false; indexes rebuild
\# "Channel connections deploy" = false; rebuilt in production
\# "Agents/templates deploy active" = false; deploy inactive, activate manually
\# "Deploy first, then test" = false; Testing Center is the pre-production gate

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

---
---

# GOVERNANCE & OBSERVABILITY (10%) — Study Notes

**Guide objectives:** (1) explain the process for **managing and monitoring agents** · (2) explain **agent analytics and agent optimization**.

> **Guide delta:** this content was previously folded into Testing/Deployment. The guide gives it its own 10% section. Same concepts, correctly re-homed here.

## OBJECTIVE 1 — MANAGING & MONITORING AGENTS

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

### Distractor bank (the audit-surface conflation set — recurs across domains)
\# **Setup Audit Trail** = admin configuration changes only — NEVER AI interaction content
\# **Event Monitoring** = platform logs; requires Shield; not the generative AI observability surface
\# **Agent Analytics** = adoption metrics, NOT the AI content audit trail
\# **Prompt Builder version history** = template-level; not an agent monitoring surface

### Layer-Config-Symptom
| Layer | Config Items | Typical Symptom If Missing |
|-------|--------------|------------------------------|
| **Agent Analytics** | Post-deployment adoption + performance dashboards: sessions, topic distribution, resolution rates, deflection, escalation rates | No visibility into agent performance at scale / can't identify misclassification trends |
| **Session & Event Logs** | Utterance-level trace for individual conversations | Can't diagnose what happened in a specific session / no single-conversation debugging |
| **Trust Layer Audit Trail** | Prompts, responses, masked-data records, toxicity scores — stored in Data 360. NOT Setup Audit Trail (admin config changes only), NOT Event Monitoring (platform logs, requires Shield) | No compliance record of AI interactions / can't audit what LLM received and returned |

## OBJECTIVE 2 — AGENT ANALYTICS & AGENT OPTIMIZATION

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

## DISTINCTION PAIRS
1. **Agent Analytics vs. Trust Layer audit trail** = adoption metrics vs. AI content records
2. **Agent Analytics vs. Session Logs** = scale/aggregate vs. single-session detail
3. **Trust Layer audit trail vs. Setup Audit Trail** = AI interaction content (Data 360) vs. admin config changes
4. **Monitoring vs. Optimization** = observing performance vs. the refinement loop that acts on it

---
---

# MULTI-AGENT ORCHESTRATION (5%) — Study Notes

**Guide objectives:** (1) determine whether a **Single-Agent (SOMA)** architecture is appropriate for scalability and control · (2) explain the purpose of open-standard multi-agent protocols such as **MCP** and **A2A**.

> **Guide delta:** LIGHTER than originally planned. **Agent API is NOT in this section** — it's an AI Agents objective (Objective 8 above). No A2A deep-dive — the guide asks only to "explain the purpose of" the protocols. 3 questions (5%) — surface-level definitions suffice.

## OBJECTIVE 1 — SOMA (Single-Agent Architecture)

✓ **SOMA (Single-Agent architecture)** ⚠ = one agent handling many topics under one security/control context
✓ **When appropriate:** topics remain coherent within a single security context; centralized control is valued; cross-agent handoff latency is undesirable
✓ **When to move OFF SOMA (toward multi-agent):** topic count grows to the point classification descriptions compete and misclassification rises; multiple business units need separate ownership; different security contexts are required

**Binding rule:** SOMA = centralized control, one reasoning context, avoids handoff latency. Scaling pressure + rising misclassification + separate-ownership needs = argument for multi-agent. ⚠ [VERIFY] the exact "SOMA" label against Spring '26 — the concept (single-agent scalability/control tradeoff) is exam-safe.

### Layer-Config-Symptom
| Layer | Config Items | Typical Symptom If Missing |
|-------|--------------|------------------------------|
| **SOMA** | Single-Agent architecture — one agent, many topics, one security/control context. Move OFF SOMA when: topic count causes classification competition, separate ownership needed, different security contexts required | Rising misclassification as topics grow / inability to separate business-unit ownership / security context conflicts |

## OBJECTIVE 2 — PROTOCOLS (MCP + A2A)

Bind as a disjoint pair — different arrows, no overlap:

✓ **MCP (Model Context Protocol)** = open standard connecting an agent to **tools and data** (agent → tools). Purpose: standardized tool discovery + invocation instead of bespoke integrations.
✓ **A2A (Agent-to-Agent protocol)** = open standard for **agent ↔ agent** communication and task delegation across platforms/vendors. Purpose: one agent delegates to or coordinates with another agent, including non-Salesforce agents.

```
agent → tools  = MCP
agent → agent  = A2A
app   → agent  = Agent API   (NOTE: this lives in AI Agents Objective 8, not here)
```

**Binding rule:** MCP connects an agent OUT to tools; A2A connects an agent to ANOTHER agent. Agent API (invoke *your* agent from an app) belongs to the AI Agents domain — a classic cross-section distractor.

### Layer-Config-Symptom
| Layer | Config Items | Typical Symptom If Missing |
|-------|--------------|------------------------------|
| **MCP** | Model Context Protocol — open standard connecting agent → tools and data. Standardized tool discovery + invocation | Agent can't access external tools / bespoke integrations instead of standardized protocol |
| **A2A** | Agent-to-Agent protocol — open standard for agent ↔ agent communication and task delegation across platforms/vendors. NOT Agent API (which is app → agent, lives in AI Agents Obj 8) | Agent can't delegate to or coordinate with another agent / cross-platform agent collaboration impossible |

### Distractor bank
\# **Agent API** placed in Multi-Agent — it's an AI Agents objective; the trap is domain-placement
\# Swapping the arrowheads: "MCP = agent-to-agent" (false — that's A2A) or "A2A = agent-to-tools" (false — that's MCP)
\# Treating SOMA as "required" — it's a tradeoff decision, not a mandate

## DISTINCTION PAIRS
1. **MCP vs. A2A** = agent→tools vs. agent→agent
2. **A2A vs. Agent API** = cross-platform agent peer protocol vs. app-invokes-your-agent (different domains)
3. **SOMA vs. multi-agent** = one reasoning context/centralized control vs. federated specialized agents

## CALIBRATED CONFIDENCE
**HIGH:** MCP purpose (agent→tools) · A2A purpose (agent→agent) · the domain-placement of Agent API (AI Agents, not here).
**MODERATE ⚠:** exact "SOMA" terminology and its precise decision criteria — concept exam-safe, label training-edge. Prioritize the scalability/control tradeoff over memorizing the acronym.

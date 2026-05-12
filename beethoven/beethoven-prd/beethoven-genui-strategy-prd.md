# HTML Generation Plan: Beethoven GenUI Strategy Doc

## Task
Convert `/Users/inbasagar.ganapathy/.claude/plans/bright-zooming-eich.md` into a rich, browser-ready HTML report using parallel agents, then assemble into one self-contained file.

## Multi-Agent Split Strategy

Split the HTML file into 4 independently writable JS string chunks + 1 assembly step.

### Output file
`/Users/inbasagar.ganapathy/eng/beethoven/beethoven-genui-strategy.html`

### Agent 1 — Shell + CSS + Navigation
Write `chunk_shell.js` (a JS file that exports a template literal):
- Full HTML skeleton (`<html>`, `<head>`, `<body>`)
- All CSS custom properties, typography, color system, layout grid
- Sticky top nav with 4 tab buttons: Overview, PRD, Architecture, Research Prompt
- Tab-switching JS logic (show/hide sections by ID)
- Sticky footer with "Copy as Markdown" export button
- Dark header banner with Beethoven title, subtitle, date badge

### Agent 2 — PRD Section (Tab 2)
Write `chunk_prd.js`:
- Executive Summary with 5-bullet callout box
- Problems 1–5 as color-coded cards (red border = critical gap)
- 4 Persona cards with pain point / what Beethoven enables layout
- Goals vs Non-Goals two-column table
- FR-1 through FR-8 requirements as an accordion (click to expand)
- NFR table (Latency / Accuracy / Availability / Security / Scalability)
- Success Metrics table with baseline → 12mo → 24mo columns, with color-coded progress bars
- Competitive position table (StateSnapshot vs StateDelta comparison)

### Agent 3 — Architecture Section (Tab 3)
Write `chunk_arch.js`:
- 3 Architecture Principles as styled callout cards with inline SVG icons
- SVG diagram: Current Architecture (baseline 5-box flow, top to bottom)
- SVG diagram: Target Architecture (8-box flow with Trust Layer, Intelligence Layer, Session Manager, Renderer Registry)
- DataProfiler feature table (8 rows, color-coded by constraint type)
- SchemaSignatureDetector table (6 SF objects → composites)
- Constraint Solver hard vs soft constraints (two columns)
- SVG diagram: Governance Proxy 6-step FLS flow (left to right)
- ComponentPlan JSON example as syntax-highlighted `<pre>`
- Renderer Contracts (4 renderers as cards)
- Session Manager + DiffEngine (REPLACE/INSERT/DELETE/UPDATE operations)
- Quality signals + ranking formula
- MCP tool definitions as syntax-highlighted JSON blocks
- SVG diagram: 3-layer protocol stack (A2A → MCP → AG-UI)
- Innovation Roadmap timeline (Near / Medium / Long term as a horizontal timeline SVG)
- Key File Map table

### Agent 4 — Gemini Research Prompt Section (Tab 4)
Write `chunk_research.js`:
- Intro paragraph explaining purpose of the prompt
- 5 research areas as collapsible `<details>` blocks, each with its sub-questions
- "Copy Full Prompt" button that copies the raw text of the Gemini prompt to clipboard
- "Open in Gemini" button (links to gemini.google.com with the prompt pre-filled via URL if feasible)

### Assembly Step (sequential, after all 4 agents complete)
- Read all 4 chunk files
- Inline them into a single `beethoven-genui-strategy.html`
- Run `open beethoven-genui-strategy.html`
- Delete the 4 chunk files

## Chunk file locations
All chunks written to `/tmp/beethoven-html/`:
- `/tmp/beethoven-html/chunk_shell.html`
- `/tmp/beethoven-html/chunk_prd.html`
- `/tmp/beethoven-html/chunk_arch.html`
- `/tmp/beethoven-html/chunk_research.html`

Each chunk is a raw HTML fragment (no `<html>` wrapper except shell). Assembly merges them.

---

# (Source document preserved below for agent reference)

# Beethoven GenUI: Enterprise Dynamic UI Platform
## Product Requirements Document + Technical Architecture

---
---

# PART I — PRODUCT REQUIREMENTS DOCUMENT

---

## 1. Executive Summary

Beethoven GenUI is a specialized orchestration engine for enterprise-grade dynamic UI generation within the Salesforce ecosystem and beyond. It sits between autonomous AI agents and front-end rendering surfaces, synthesizing the *correct* UI component — not just *a* UI component — for any given user intent, CRM data context, and organizational security policy.

The market signal is clear: AI-generated UI is not a future concept. Claude Artifacts, Gemini Canvas, ChatGPT Canvas, and Google's A2UI protocol all prove that the category exists and has user demand. But every one of these surfaces generates *generic UI* for *generic queries*. None can know that an `Opportunity.StageName` field should drive a pipeline funnel — not a bar chart. None enforce Salesforce Field Level Security before a single token is sent to an LLM. None cache the semantic *shape* of a previous generation so the next user with the same conceptual question gets an answer in 30ms instead of 10 seconds. None learn from the 10,000th successful interaction to make the 10,001st better.

Beethoven's mission is to build and hold that gap — to be the enterprise intelligence layer that general-purpose AI surfaces cannot replicate.

---

## 2. Problem Statement

### 2.1 The Fundamental Gap

Enterprise users increasingly expect AI assistants to *show* them information, not just *tell* them. When a sales rep asks "what deals should I focus on this quarter?", a plain text response is insufficient. The information lives in CRM data. The *right* answer is a prioritized deal dashboard — a composable Lightning interface with pipeline stage breakdown, deal health scoring, close date proximity weighting, and one-click actions. No general-purpose AI surface can generate that from scratch, in real-time, with correct data binding, in compliance with the user's field-level permissions.

### 2.2 What's Broken Today

**Problem 1 — Probabilistic Component Selection**: Current LLM-only approaches to UI generation achieve roughly 70-75% accuracy on component type selection. An LLM might generate a 50-slice pie chart because pie charts are common in its training set, even when the data's cardinality makes it functionally unreadable. No statistical profiling layer exists to override this.

**Problem 2 — Full Re-render Inefficiency**: Native AI surfaces (Claude Artifacts, Gemini Canvas) use a "StateSnapshot" model — every follow-up question regenerates the entire UI from scratch. A user who filters a table, then asks to highlight specific rows, gets a completely re-rendered interface with all prior interactive state lost. For multi-turn enterprise workflows, this is unacceptable.

**Problem 3 — Security Blind Spots**: General-purpose AI surfaces send raw data payloads to external LLM providers with no FLS enforcement, no PII masking, and no audit logging. In a Salesforce environment where row-level sharing rules and field-level security govern what each user can see, this creates a compliance and trust violation risk.

**Problem 4 — No Learning Loop**: Each generation is cold-start. The 1,000th sales rep asking to see their pipeline gets the same raw LLM inference time as the 1st. Template libraries don't adapt. Cache quality doesn't improve. Successful patterns from past interactions are discarded.

**Problem 5 — Ecosystem Lock-In**: A system that generates Lightning UEM exclusively cannot serve the same users on Slack, Microsoft Teams, Gemini, or any other agentic surface. Enterprise organizations are heterogeneous. A UI generation engine locked to one surface format has a ceiling.

### 2.3 The Opportunity

The constraint-based generation model — where statistical data profiling pre-constrains the component search space before any LLM call — is both academically validated (VizML/MIT CHI 2020, Draco/UW InfoVis 2018, Portal UX Agent/arxiv 2511.00843) and commercially proven (Tableau Pulse's fixed insight taxonomy achieves enterprise trust precisely *because* of its determinism). Combining this with a living, quality-evolving semantic cache and a multi-surface rendering pipeline creates a system that general-purpose platforms cannot replicate without years of investment.

---

## 3. Users and Personas

### Persona 1: The Salesforce Sales Rep (Primary End User)
**Context**: Uses Agentforce conversational AI daily. Asks questions about their pipeline, accounts, and opportunities. Expects visual answers — not walls of text.
**Pain point today**: Gets text summaries. Has to navigate to separate dashboard pages. Loses context switching between conversation and CRM views.
**What Beethoven enables**: Inline dynamic dashboards rendered directly in the Agentforce conversation. Ask "which deals are at risk?" → get a prioritized deal list with health indicators, stage progression, and coach notes. No page navigation required.

### Persona 2: The Agentforce Developer (Platform Builder)
**Context**: Building custom AI agents on Salesforce. Wants their agents to output rich UI without building a rendering pipeline from scratch.
**Pain point today**: Has to hardcode UI responses or use static templates. Cannot generate adaptive layouts based on the query + data.
**What Beethoven enables**: `generate_ui(query, response_text)` — one API call or MCP tool invocation. The developer focuses on agent logic; Beethoven handles the visual output.

### Persona 3: The Enterprise IT / Security Architect
**Context**: Responsible for approving AI tools in enterprise deployments. Primary concerns: data governance, FLS compliance, audit trails, PII exposure risk.
**Pain point today**: Generic AI surfaces have no answer for "where does my customer data go when the LLM generates the UI?" No audit log. No FLS enforcement. No zero-retention guarantee.
**What Beethoven enables**: Governance Proxy architecture — FLS masking before any LLM call, zero-retention at the generation layer, full audit log of every generated interface, GDPR-compliant cache invalidation.

### Persona 4: The Multi-Surface Enterprise Platform Engineer
**Context**: Manages AI deployments across Salesforce, Slack, Microsoft Teams, and internal portals. Each surface has a different UI format requirement.
**Pain point today**: Four different UI generation pipelines — one per surface. No shared intelligence, no shared cache, no consistent brand compliance.
**What Beethoven enables**: One ComponentPlan, four render targets. The same semantic intelligence that selects the right Lightning chart also selects the right Slack Block Kit layout.

---

## 4. Goals and Non-Goals

### Goals
- Become the standard UI generation layer for Agentforce and the broader Salesforce AI ecosystem
- Achieve ≥65% semantic cache hit rate at 24 months (reducing average generation latency below 30ms for repeat patterns)
- Achieve ≥94% component selection accuracy by combining statistical profiling with LLM reasoning
- Support ≥4 render targets: Salesforce Lightning UEM, Slack Block Kit, Microsoft Adaptive Cards, AG-UI / A2UI
- Pass Salesforce Security Review requirements as a first-class feature, not a retrofit
- Enable any MCP-compatible AI agent to generate Lightning UI via standardized tool calling
- Build a living template repository that measurably improves in quality over time with interaction data

### Non-Goals
- Beethoven is NOT a general-purpose UI builder or low-code development platform
- Beethoven is NOT a replacement for Salesforce Lightning App Builder or Flow Builder
- Beethoven does NOT handle real-time data streaming or live dashboard updates (those are consuming application concerns)
- Beethoven does NOT generate arbitrary HTML/CSS/JavaScript — only schema-validated, catalog-bound components
- Beethoven is NOT a standalone product; it is a platform capability embedded in agent pipelines

---

## 5. Requirements

### 5.1 Functional Requirements

**FR-1: Constrained Statistical Pre-Selection**
Before any LLM call, the system SHALL analyze the data structure in `response_text` to extract statistical features (cardinality, semantic type, temporal ordering, null rate, distribution shape) and use these features to hard-constrain the component search space. A dataset with cardinality > 20 SHALL never offer a pie/donut chart. A time-series dataset with > 10 temporal points SHALL always prioritize line/area charts. These are not suggestions — they are hard constraints that override LLM preference.

**FR-2: CRM Schema Signature Detection**
The system SHALL recognize Salesforce object and field patterns in `response_text` — detecting `Opportunity` records by field name signatures (`StageName`, `Amount`, `CloseDate`), `Account` records, `Case` records, etc. — and use this detection to inject semantic hints into the generation prompt and to directly pre-select domain-appropriate component composites.

**FR-3: Governance Proxy / FLS Enforcement**
Before any data is sent to an LLM, the system SHALL: (1) query the requesting user's FLS profile for the relevant Salesforce objects, (2) mask any field values for which the user lacks read access, (3) strip any PII (email, phone, SSN, address) from the LLM prompt while preserving structural metadata for UI planning, (4) re-hydrate the actual values from Salesforce on the client side after the UI schema is generated. No PII or FLS-protected data SHALL reside in the LLM prompt or in any cache tier.

**FR-4: Multi-Surface Rendering**
The system SHALL support at least the following render targets, each producing schema-valid output:
- `lightning_uem` — Salesforce Lightning UEM (existing)
- `slack_block_kit` — Slack Block Kit v2 JSON (validated against Block Kit schema)
- `adaptive_card` — Microsoft Adaptive Cards JSON (validated against Adaptive Cards schema)
- `ag_ui` / `a2ui` — AG-UI StateSnapshot JSON compliant with A2UI v0.8 adjacency list format

**FR-5: Stateful UI Sessions with Delta Updates**
The system SHALL maintain UI sessions identified by a `session_id`. A session stores the current `ComponentTree` version. Follow-up queries with the same `session_id` SHALL produce a `StateDelta` — only the minimal set of component changes required — rather than a full `StateSnapshot` regeneration. The user's prior interactive state (scroll position, filters, focused elements) SHALL be preserved across delta updates.

**FR-6: Living Template Repository**
The cache SHALL track per-template quality signals: hydration success rate, usage frequency, and (when feedback is available) explicit accept/dismiss signals. The K-nearest-neighbor search for semantic cache hits SHALL rank results by a composite score: `similarity_score × quality_score`. Templates with hydration failure rates above 30% SHALL be automatically demoted below the minimum retrieval threshold.

**FR-7: MCP Server Interface**
The system SHALL expose an MCP-compliant server with the following tools:
- `generate_ui(query, response_text, render_target, type_classes)` — main generation endpoint
- `get_component_catalog(type_classes, domain)` — retrieve available component schemas
- `explain_component_selection(component_name, data_profile)` — introspect why a component was chosen
And the following MCP Resources:
- `lightning://components/{type_class}` — component schema access
- `lightning://templates/{org_id}` — cached template registry for the current org

**FR-8: Audit Logging**
The system SHALL emit a structured audit log entry for every generation event containing: user identifier, org ID, query hash (not plain text), component plan summary, fields rendered, render target, cache tier served (L1/L2/Core/none), generation latency, and timestamp. Audit logs SHALL be append-only and SHALL NOT contain any field values.

### 5.2 Non-Functional Requirements

**NFR-1: Latency**
- Cache hit (L1): ≤ 30ms P99
- Cache hit (L2 semantic): ≤ 150ms P99
- Cache miss (full LLM generation): ≤ 8s P50, ≤ 15s P99

**NFR-2: Accuracy**
- Component type selection accuracy ≥ 88% at 12 months (vs. ~75% baseline today)
- Template hydration success rate ≥ 95% for promoted templates

**NFR-3: Availability**
- System-level availability ≥ 99.5% (fail-open on all cache tiers; LLM fallback always available)
- No single point of failure — Redis failure, LLM provider failure, and Core VegaCache failure each individually degrade gracefully to the next fallback tier

**NFR-4: Security**
- Zero PII in LLM prompts, cache keys, or cached templates
- FLS enforcement on all org-scoped generation requests
- Full audit trail with tamper-evident logs
- GDPR cache invalidation within 24 hours of subject deletion request

**NFR-5: Scalability**
- Support concurrent generation for at least 1,000 active org sessions
- Redis L2 cache to support at least 10M stored template vectors per org cluster
- MCP server to handle at least 100 concurrent tool invocations

---

## 6. Success Metrics (24-Month Horizon)

| Metric | Baseline | 12-Month Target | 24-Month Target |
|---|---|---|---|
| Semantic cache hit rate | ~15% | 45% | 65% |
| P50 latency (cache hit) | ~200ms | ~80ms | ~30ms |
| P50 latency (LLM path) | ~10s | ~7s | ~5s |
| Component selection accuracy | ~75% | ~88% | ~94% |
| Render targets | 1 | 3 | 5 |
| FLS enforcement | ✗ | Partial | Full |
| Template quality evolution | Static | Quality-tracked | RL-ranked |
| Stateful sessions (delta updates) | ✗ | ✗ | ✓ |
| MCP callable | ✗ | ✓ | ✓ |

---

## 7. Competitive Position

The Gemini deep research confirms a critical architectural distinction: **StateSnapshot vs. StateDelta**. Claude Artifacts and ChatGPT Canvas are StateSnapshot systems — every turn regenerates the full UI, discarding interactive state. A2UI v0.8's adjacency list format with path-based two-way data binding is a StateDelta system — only changes to the data model are transmitted. Beethoven's architecture must adopt the StateDelta model.

Google A2UI's path-based binding (`/form/customer_name`) maps directly to Salesforce field names. This is architecturally significant: A2UI paths are a natural encoding for Salesforce's object.field notation (`Opportunity.Amount`). Beethoven should implement A2UI as the primary open-standard output format and extend it with Salesforce-specific path conventions.

The Adaptive Cards comparison reveals the LWC/Shadow DOM challenge: Adaptive Cards cannot trigger Lightning-specific events or interact with the Lightning Data Service (LDS) cache. Microsoft's format is insufficient for Salesforce-native deployments. Beethoven's UEM format, combined with A2UI for cross-platform targets, covers the full surface matrix.

---
---

# PART II — TECHNICAL ARCHITECTURE DOCUMENT

---

## 1. Architecture Philosophy

Beethoven's generation pipeline embodies three principles drawn from academic research and production evidence:

**Principle 1 — Constrained Generation (not Free-Form Generation)**
The Portal UX Agent (arxiv 2511.00843) and Modular Layout Synthesis (arxiv 2512.18996) independently validate the two-stage architecture: LLM performs *semantic planning*; a deterministic, schema-constrained assembler performs *UI construction*. Free-form code generation (Claude Artifacts model) is unreliable at enterprise scale because it has no schema enforcement, no design system compliance, and no accessibility guarantees. Beethoven's LLM never outputs UI code — it outputs *intent*, which the assembler maps to validated component schemas.

**Principle 2 — Statistical Certainty Before Probabilistic Reasoning**
For decisions that can be resolved deterministically from data properties (cardinality → chart type, temporal ordering → axis assignment, semantic type → component variant), statistical profiling is used *before* any LLM call. LLMs are reserved for ambiguous decisions that require semantic understanding. The formal model (from Gemini research, Draco-derived):

`C = argmin_{x ∈ K} (Cost(x, P))`

Where `K` is the constraint set and `P` is the data profile. The cost function penalizes violations of hard design constraints (accessibility, cardinality limits, color-blind safety) and soft preferences (the user's historical component choices). This transforms the LLM's role from *guessor* to *confirmer*.

**Principle 3 — Delta Over Snapshot**
A2UI v0.8's adjacency list format with path-based two-way data binding enables a reactive model where only data model changes are transmitted. Beethoven's session layer adopts this model: after the initial `StateSnapshot`, all subsequent interactions produce `StateDelta` objects. This eliminates full re-renders, preserves user interactive state, and reduces generation latency for follow-up queries by an order of magnitude.

---

## 2. System Architecture

### 2.1 Current Architecture (Baseline)

```
┌─────────────────────────────────────────────────────┐
│                 REQUEST BOUNDARY                     │
│  user_query + response_text + org_id                │
└─────────────────────┬───────────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────────┐
│              CACHE MANAGER (3-Tier)                  │
│  L1: ThreadSafeCache (in-process, exact key, ~2ms)  │
│  Core: VegaCache (per-org, exact key, ~300ms)       │
│  L2: Redis HNSW (semantic similarity, ~100ms)       │
└───────────────┬─────────────────────────────────────┘
                │ cache miss
                ▼
┌─────────────────────────────────────────────────────┐
│                  ORCHESTRATOR                        │
│  Step 1: Discovery (LLM selects blocks or no-op)   │
│  Step 2: Generation (LLM selects strategy)         │
│  Step 3: UEM Assembly (deterministic)              │
└───────────────┬─────────────────────────────────────┘
                │
                ▼
┌─────────────────────────────────────────────────────┐
│              CACHE WRITE (background)               │
│  L1 write + L2 embedding generation + Redis write  │
└─────────────────────────────────────────────────────┘
                │
                ▼
             UEM BlockNode → REST response
```

**Gaps**: No trust layer, no statistical profiling, no CRM schema detection, no multi-surface output, no session state, no quality tracking, no MCP interface.

### 2.2 Target Architecture (24-Month)

```
┌──────────────────────────────────────────────────────────────────────┐
│                         REQUEST BOUNDARY                              │
│  user_query + response_text + org_id + session_id? + render_target  │
└─────────────────────────────────┬────────────────────────────────────┘
                                  │
                                  ▼
┌─────────────────────────────────────────────────────────────────────┐
│                    TRUST LAYER MIDDLEWARE                            │
│  1. FLS Query: fetch user's permitted fields for detected objects   │
│  2. PII Masking: strip sensitive field values; retain schema only   │
│  3. Audit Log Entry: record request metadata (no field values)      │
└─────────────────────────────────┬───────────────────────────────────┘
                                  │
                                  ▼
┌─────────────────────────────────────────────────────────────────────┐
│                    INTELLIGENCE LAYER                                │
│  DataProfiler: statistical features (cardinality, type, temporal)  │
│  SchemaSignatureDetector: Salesforce object recognition             │
│  ConstraintSolver: Draco-inspired hard/soft constraint evaluation   │
│  → Output: ComponentConstraints (hard exclusions + soft preferences)│
└─────────────────────────────────┬───────────────────────────────────┘
                                  │
                                  ▼
┌─────────────────────────────────────────────────────────────────────┐
│                    CACHE MANAGER (3-Tier + Quality)                 │
│  L1: exact key, ~2ms                                               │
│  Core: VegaCache exact key, ~300ms                                 │
│  L2: Redis HNSW semantic, ranked by similarity × quality, ~100ms  │
└───────────────────────────┬─────────────────────────────────────────┘
                            │ cache miss
                            ▼
┌─────────────────────────────────────────────────────────────────────┐
│                    ORCHESTRATOR (Constraint-Aware)                  │
│  Discovery: LLM selects blocks (pre-filtered by ConstraintSolver)  │
│  Generation: LLM selects strategy (constrained by DataProfiler)    │
│  Assembly: deterministic ComponentPlan construction                │
└───────────────────────────┬─────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────────────┐
│                    SESSION MANAGER                                  │
│  If session_id: compute StateDelta vs. stored ComponentTree        │
│  If new: create StateSnapshot                                      │
│  Persist session state in Redis (TTL: 24h)                        │
└───────────────────────────┬─────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────────────┐
│                    RENDERER REGISTRY                                │
│  render_target dispatch:                                           │
│  ├── lightning_uem → LightningUEMRenderer                         │
│  ├── slack_block_kit → SlackBlockKitRenderer                      │
│  ├── adaptive_card → AdaptiveCardRenderer                         │
│  └── a2ui / ag_ui → A2UIRenderer (adjacency list + path binding)  │
└───────────────────────────┬─────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────────────┐
│                    OUTPUT + BACKGROUND TASKS                        │
│  Immediate: Rendered payload → streaming response (AG-UI events)  │
│  Background: Cache write, quality signal update, audit log flush   │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 3. Intelligence Layer: Statistical Data Profiling

### 3.1 DataProfiler

The `DataProfiler` runs before any LLM call and extracts the following features from `response_text`:

| Feature | Computation | Use in Constraints |
|---|---|---|
| **Cardinality** | `len(set(values))` per dimension | Cardinality > 20: exclude pie/donut/radar |
| **Temporal ordering** | Detect ISO 8601 / Unix timestamp patterns; test monotonicity | Temporal + >10 points: prioritize line/area |
| **Semantic type** | Pattern match: currency, percentage, ID, email, phone, geo | Currency: KPI card; Geo: map component |
| **Distribution shape** | Skewness, kurtosis of numeric fields | High skew: log-scale chart; near-uniform: histogram |
| **Null rate** | `null_count / total_count` per field | Null rate > 40%: exclude components requiring completeness |
| **Dimensionality** | Count of numeric + categorical fields | 1 numeric: histogram; 2 numeric: scatter; N categorical: group bar |
| **Record count** | Total row count | Count > 500: force table over card list; count > 10K: force aggregation |
| **Hierarchy depth** | Detect parent_id self-referential patterns | Depth > 1: treemap or org chart component |

This table defines the hard constraint layer. Soft preferences (historical component performance, role-based defaults) augment this with weighted scores.

### 3.2 SchemaSignatureDetector

Detects Salesforce object types by field name signatures in `response_text`:

| Object Signature | Key Fields Detected | Domain Composite |
|---|---|---|
| `Opportunity` | StageName, Amount, CloseDate, ForecastCategory | `OpportunityPipelineDashboard` |
| `Account` | AnnualRevenue, Industry, NumberOfEmployees, HealthScore | `AccountHealthScorecard` |
| `Case` | Priority, Status, CaseNumber, Subject | `CasePriorityMatrix` |
| `Lead` | LeadSource, Status, Rating, ConvertedDate | `LeadConversionFunnel` |
| `Task/Activity` | ActivityDate, Subject, WhoId, WhatId | `ContactActivityTimeline` |
| `Forecast` | ForecastAmount, OwnerName, Period | `ForecastVsActualChart` |

When a signature match occurs, the detected domain composite is pre-selected as the generation target. The LLM's discovery step is bypassed for exact matches; it is retained for partial or ambiguous matches.

### 3.3 Constraint Solver (Draco-Inspired)

The constraint solver evaluates the statistical features against a rule set:

**Hard Constraints (violations score = ∞, always excluded)**:
- Pie/donut chart with cardinality > 15
- Bar chart on a continuous numeric field with >100 distinct values
- Color-scale visualization in a mode where colorblind-safe palettes are unavailable
- Any component rendering PII fields (email, phone, SSN) if FLS masking is active

**Soft Constraints (weighted penalty; higher = less preferred)**:
- Bar chart preferred over pie for comparative magnitude (weight: 0.3)
- Line chart preferred over scatter for temporal data (weight: 0.5)
- Table preferred over card list for record count > 100 (weight: 0.4)
- Domain composite preferred over generic component when signature match (weight: 0.8)

The solver outputs a ranked `ComponentConstraints` object injected into the LLM discovery prompt as explicit guidance. This shifts the LLM from probabilistic guessing to constraint-bounded selection.

---

## 4. Governance Proxy Architecture

The Governance Proxy is the most critical enterprise differentiator. It ensures that no PII or FLS-protected data ever reaches the LLM generation layer.

### 4.1 Request Flow with FLS Enforcement

```
Step 1 — Metadata Interception
  response_text arrives at TrustLayerMiddleware
  SchemaSignatureDetector identifies Salesforce objects present

Step 2 — FLS Query
  For each detected object:
    query Salesforce Metadata API: DescribeSObjectResult.fields[].isAccessible
    for requesting user's session token
  Build FLS-allowed field set per object

Step 3 — Value Masking
  Traverse response_text:
    For fields NOT in FLS-allowed set: replace value with "[RESTRICTED]"
    For PII fields (email, phone, SSN, address): replace value with "[MASKED]"
    For currency/amount fields: replace with anonymized range buckets if required
  Generate masked_response_text

Step 4 — LLM Generation (on masked_response_text)
  Orchestrator uses masked_response_text for component planning
  LLM sees structural metadata and anonymized values only
  ComponentPlan is generated from masked context

Step 5 — Client-Side Re-hydration
  ComponentPlan is returned to calling application
  Data binding expressions ($data.Opportunity.Amount) reference Salesforce field paths
  Calling application fetches actual values from Salesforce via user's authenticated session
  Real values are rendered client-side directly from Salesforce APIs
  LLM generation layer never held the real values

Step 6 — Audit Log
  Emit: { user_id_hash, org_id, query_hash, objects_detected, fields_in_plan,
          fields_masked, render_target, cache_tier, latency_ms, timestamp }
  Log is append-only, stored to compliance-grade storage (not Redis)
```

### 4.2 GDPR Cache Invalidation

When a "right to erasure" request arrives for a data subject:
1. The `CacheInvalidator` receives a webhook event with the affected object IDs
2. It queries the cache for all templates that reference those object field paths
3. Affected templates are expired from L1, L2 Redis, and Core VegaCache
4. An invalidation audit entry is written with the subject ID hash and affected template count

This ensures that cached UI templates do not perpetuate display of personal data after erasure.

---

## 5. Multi-Surface Rendering Engine

### 5.1 ComponentPlan: The Surface-Agnostic Representation

The `ComponentPlan` is the output of the orchestrator — a structured, surface-agnostic description of UI intent:

```
ComponentPlan {
  intent: "pipeline_overview",
  strategy: "composable",
  blocks: [
    ComponentBlock {
      semantic_role: "primary_metric",
      component_type_hint: "metric_card",
      data_binding: { value: "$data.Opportunity.Amount", label: "Open Pipeline" },
      constraints: { display_currency: true }
    },
    ComponentBlock {
      semantic_role: "stage_breakdown",
      component_type_hint: "bar_chart",
      data_binding: { x: "$data.StageName", y: "$data.Amount" },
      constraints: { orientation: "horizontal", sort_by: "y_desc" }
    }
  ],
  layout: {
    type: "two_column",
    primary_slot: 0,
    secondary_slots: [1]
  },
  data_profile: { cardinality_max: 8, record_count: 47, has_currency: true }
}
```

### 5.2 Renderer Contracts

Each renderer implements the `BaseRenderer` protocol:
- `render(plan: ComponentPlan, org_context: OrgContext) → RenderedPayload`
- `validate(payload: RenderedPayload) → ValidationResult` (schema validation)
- `render_target: RenderTarget` (declared target surface)

**LightningUEMRenderer**: Existing UEM assembly refactored into the renderer contract. Output: UEM BlockNode JSON.

**SlackBlockKitRenderer**: Maps `ComponentPlan` blocks to Slack Block Kit v2 block types. Metric cards → `section` with `fields`. Charts → `image` block with chart URL or `actions` block. Tables → `section` blocks with overflow. Validated against Block Kit schema.

**AdaptiveCardRenderer**: Maps to Microsoft Adaptive Cards JSON schema. Containers → `Container`. Metric cards → `FactSet`. Tables → `ColumnSet + Column`. Action buttons → `Action.Submit`. Note: Adaptive Cards cannot interact with Salesforce LDS cache — data binding is static at render time for this target.

**A2UIRenderer**: The primary open-standard output format. Implements A2UI v0.8 adjacency list with path-based two-way data binding. Each component in the `ComponentPlan` maps to an A2UI node with:
- `id`: stable component identifier
- `type`: A2UI component type (mapped from Beethoven semantic_role)
- `children`: adjacency list references
- `bindings`: path strings mapping to `$data.Object.Field` (Salesforce field paths directly compatible with A2UI `/form/field` convention)
- `actions`: delta event handlers for user interactions

The A2UI `dataModelUpdate` pattern maps directly to Beethoven's StateDelta — when a user filters a table, the `valueMap` contains only the changed binding paths, not the full component tree.

---

## 6. Session Manager and Delta Updates

### 6.1 UISession Model

```
UISession {
  session_id: str  # UUID
  org_id: str
  user_id_hash: str
  component_tree: ComponentTree  # current rendered state
  version: int
  created_at: datetime
  last_updated_at: datetime
  ttl_seconds: 86400  # 24 hours
}
```

Sessions are stored in Redis with key `session:{org_id}:{session_id}`.

### 6.2 Delta Computation

For follow-up queries in an existing session:
1. The new query is processed through the full Intelligence Layer and Orchestrator
2. A new `ComponentPlan` is generated
3. The `DiffEngine` computes the minimal delta:
   - `REPLACE` operations: component whose semantic_role exists in current tree but with different type or binding
   - `INSERT` operations: new semantic_roles not present in current tree
   - `DELETE` operations: semantic_roles in current tree not present in new plan
   - `UPDATE` operations: same semantic_role and type, only data binding changed
4. The delta is emitted as an AG-UI `StateDelta` event with the minimal `valueMap`
5. The session's `component_tree` is updated atomically in Redis

This means "change that bar chart to a line chart" generates a single `REPLACE` delta for one component — not a full re-render.

---

## 7. Living Template Repository: Quality Evolution

### 7.1 Quality Signals

| Signal | Collection Method | Weight |
|---|---|---|
| **Hydration success rate** | Template binding resolved without missing fields | 0.4 |
| **Usage frequency** | How often this template is retrieved and served | 0.2 |
| **User accept rate** | Explicit accept/dismiss via `POST /templates/{key}/feedback` | 0.3 |
| **Action completion** | Follow-up action taken on the rendered UI (if measurable) | 0.1 |

### 7.2 Ranking Formula

The Redis HNSW search returns top-K candidates ranked by cosine similarity. After retrieval, candidates are re-ranked by:

`score = similarity_score × (0.4 × hydration_rate + 0.2 × usage_norm + 0.3 × accept_rate + 0.1 × action_rate)`

Templates with `hydration_rate < 0.70` are assigned a floor score of 0 — they are never served regardless of semantic similarity.

### 7.3 Automatic Template Demotion

A background process runs every 6 hours:
- Templates with `usage_count > 10` AND `hydration_rate < 0.70`: delete from L2 cache
- Templates with `accept_rate < 0.30` (where accept signals exist): demote to secondary index
- Templates not accessed in 30 days: expire (TTL enforcement)
- Templates that are consistently outperformed by a newer template for the same semantic cluster: soft-delete

---

## 8. MCP Server Interface

### 8.1 Tool Definitions

**Tool: `generate_ui`**
```json
{
  "name": "generate_ui",
  "description": "Generate a dynamic UI component for a Salesforce CRM query and response.",
  "inputSchema": {
    "type": "object",
    "required": ["query", "response_text"],
    "properties": {
      "query": { "type": "string" },
      "response_text": { "type": "string" },
      "render_target": {
        "type": "string",
        "enum": ["lightning_uem", "slack_block_kit", "adaptive_card", "a2ui"],
        "default": "lightning_uem"
      },
      "type_classes": {
        "type": "array",
        "items": { "type": "string" }
      },
      "session_id": { "type": "string" }
    }
  }
}
```

**Tool: `get_component_catalog`**
Returns the available Lightning component types for the given `type_classes` and domain context, formatted as human-readable capability descriptions.

**Tool: `explain_component_selection`**
Returns the reasoning chain: data profile features → constraint solver output → LLM selection → final component. Enables developers to audit and understand why a specific component was chosen.

### 8.2 MCP Resources

- `lightning://components/{type_class}` → Full component schema (attributes, types, required fields, constraints)
- `lightning://templates/{org_id}` → Summary of cached templates for the org (count, quality stats, top patterns)

---

## 9. Protocol Stack Integration

### 9.1 The Three-Layer Enterprise Agentic Stack

```
┌─────────────────────────────────────────────────────────┐
│            A2A (Agent-to-Agent)                         │
│  Agent orchestration, task delegation, capability      │
│  discovery via Agent Cards. Beethoven publishes an     │
│  Agent Card advertising its UI generation capability.  │
└────────────────────────────┬────────────────────────────┘
                             │
┌────────────────────────────▼────────────────────────────┐
│            MCP (Model Context Protocol)                 │
│  Tool invocation, resource access, component catalog.  │
│  Beethoven is an MCP server; any LLM host can call it. │
└────────────────────────────┬────────────────────────────┘
                             │
┌────────────────────────────▼────────────────────────────┐
│            AG-UI / A2UI (Agent-to-User Interface)       │
│  StateSnapshot + StateDelta streaming to frontend.     │
│  Beethoven's session manager emits AG-UI events.       │
│  A2UIRenderer produces A2UI v0.8 adjacency list JSON.  │
└─────────────────────────────────────────────────────────┘
```

Beethoven participates in all three layers:
- **A2A**: Publishes an Agent Card (`beethoven_agent_card.json`) advertising the `generate_ui` capability with input/output schema
- **MCP**: Runs as an MCP server (`beethoven_mcp_server.py`) alongside the existing FastAPI REST server
- **AG-UI**: The FastAPI WebSocket endpoint emits AG-UI `StateSnapshot` and `StateDelta` events; the A2UIRenderer produces A2UI v0.8 payloads for any AG-UI-compatible client

---

## 10. Innovation Roadmap (Research-Grounded)

### Near-Term (Q3-Q4 2026): Foundation Hardening
1. **DataProfiler + ConstraintSolver** — Replace LLM guessing with statistical certainty for 60% of component decisions. Draco constraint model, VizML-inspired feature extraction.
2. **MCP Server** — Make Beethoven callable from Claude Code, Cursor, GitHub Copilot, and any future MCP host. First-mover advantage in Lightning-UI MCP tooling.
3. **Governance Proxy** — FLS enforcement, PII masking, audit logging. Required for enterprise procurement approval.

### Medium-Term (Q4 2026 - Q1 2027): Platform Expansion
4. **Multi-Surface Rendering** — A2UIRenderer (A2UI v0.8 adjacency list), SlackBlockKitRenderer, AdaptiveCardRenderer. One ComponentPlan → four render targets.
5. **Living Template Repository** — Quality-ranked template retrieval, automatic demotion of poor-performing templates, explicit feedback API.
6. **Stateful UI Sessions** — UISession model, DiffEngine, AG-UI StateDelta streaming. Multi-turn conversational UI without full regeneration.

### Long-Term (Q2-Q4 2027): Intelligence Compounding
7. **Per-User Adaptive Rendering** — Thompson Sampling bandit for per-user component preference learning. EASE 2025 per-user RL research applied at Salesforce scale.
8. **Predictive Pre-Generation** — Pre-warm L1 cache with top-3 predicted next queries during conversation idle time. CoALA "prospective memory" pattern.
9. **Multimodal Input** — Accept screenshots, wireframes, or existing UI images alongside text queries. UIPress token compression (arxiv 2604.09442) makes vision-model inference cost feasible.
10. **Fine-Tuned Generation Model** — Build training dataset from accumulated query → successful template pairs. Fine-tune a 7B model specifically for Lightning component selection. UIPress demonstrates 21.7M parameter adapters achieve near-SOTA at 0.26% of base model size.

---

## 11. Key File Map (Current Codebase Integration Points)

| New Component | Integrates With | Primary File to Modify |
|---|---|---|
| `DataProfiler` | Pre-discovery step | `genui/orchestration/orchestrator.py` |
| `ConstraintSolver` | Discovery prompt construction | `genui/tools/services.py:BlockDiscoveryService` |
| `SchemaSignatureDetector` | Pre-discovery step | `genui/orchestration/orchestrator.py` |
| `TrustLayerMiddleware` | API request pipeline | `genui/api/server.py` |
| `RendererRegistry` | Post-assembly output | `genui/processing/uem.py` (refactor into renderer) |
| `A2UIRenderer` | New renderer | `genui/api/models.py` (add render_target) |
| `UISession` / `DiffEngine` | WebSocket endpoint | `genui/api/server.py` |
| `TemplateQualityTracker` | Cache write path | `genui/caching/cache_manager.py`, `redis_client.py` |
| `MCPServer` | Standalone alongside FastAPI | `genui/api/server.py` (add MCP server startup) |
| `AuditLogger` | Trust middleware | New component, write to compliance store |

---

## 12. Gemini Research Prompt for Further Intelligence Gathering

The following prompt is ready to submit to Gemini Deep Research to gather additional intelligence that will further sharpen the technical specifications:

---

```
DEEP RESEARCH REQUEST: Enterprise Generative UI — Strategic Technical Intelligence
(Beethoven GenUI / Salesforce Lightning Platform)

I am building a comprehensive technical strategy for an enterprise-grade AI-powered dynamic UI generation system deeply integrated with Salesforce CRM. The system (codename: Beethoven) orchestrates LLM-based component selection, statistical data profiling, and multi-surface rendering for Agentforce and other agentic surfaces. I need exhaustive research across the following areas.

---

AREA 1: Statistical Visualization Recommendation — Production Depth

1a. What is the exact production status of VizML (MIT CHI 2020)? Has it been adopted in any commercial product, or is there a successor paper/system published 2022-2026? What were its documented limitations at scale — specifically, its failure modes on enterprise CRM data (sparse fields, high-null datasets, mixed semantic types)?

1b. Draco 2 (UW 2023) introduced learnable soft constraint weights from empirical perception studies. Is there a publicly available Python implementation (the draco PyPI package)? What is the API for defining custom soft constraints? Does it support runtime constraint weight updates without recompilation?

1c. How does ThoughtSpot's AI Analytics (Sage) technically approach automatic chart type selection? Does it use a statistical profiling layer, a fine-tuned classification model, or LLM reasoning? What are its documented failure modes on enterprise financial data?

1d. What is the most reliable method — tested in production — for automatically distinguishing between: (a) a time-series dataset vs. (b) a snapshot dataset with a date field vs. (c) a longitudinal panel dataset? Are there open-source Python libraries (pandas-profile, ydata-profiling, etc.) that handle this distinction reliably?

1e. For Salesforce specifically: does the Salesforce Metadata API provide field-level semantic type annotations (currency, percentage, date, picklist, lookup, formula) that can be used directly as statistical profiling inputs without LLM inference? If so, what is the API endpoint and field structure?

---

AREA 2: A2UI v0.8 — Specification Depth

2a. Where is the canonical public specification for Google A2UI v0.8? Is there a GitHub repository, a Google Developers blog post, or a published JSON schema? If v0.8 is not public, what is the most recent publicly available version or preview?

2b. In A2UI's adjacency list format — what is the exact JSON schema for a node entry? What fields are required vs. optional? How are parent-child relationships encoded (by ID reference, by nesting, or by explicit edge list)?

2c. How does A2UI's two-way path binding handle Salesforce's relationship fields? For example, can a path like `/opportunity/account/name` traverse a lookup relationship, or is the binding limited to flat object fields?

2d. A2UI recommends ThreadLocal for callback storage in concurrent scenarios. In a Python async context (FastAPI + asyncio), what is the equivalent concurrency-safe pattern for managing callback state across concurrent A2UI sessions? Is there documented guidance from the A2UI spec for async implementations?

2e. What renderers officially support A2UI v0.8? Is there a Flutter renderer, a Lit web component renderer, and an Angular renderer that are production-ready and openly licensed? What is the installation/integration path for each?

---

AREA 3: AG-UI Protocol — Technical Specification

3a. What is the exact JSON schema for an AG-UI StateDelta event? What fields are required? How does it represent: (a) a component property change, (b) a component insertion, (c) a component deletion, (d) a layout restructure?

3b. How does AG-UI handle optimistic UI updates — where the frontend applies a delta before the agent confirms success? Is there a rollback mechanism in the protocol?

3c. What is the relationship between AG-UI and OpenTelemetry? Can AG-UI events be instrumented as OpenTelemetry spans for distributed tracing? Is there a documented integration pattern?

3d. Is there a Python SDK for emitting AG-UI events from a FastAPI server? What is the package name and installation path?

3e. What enterprise companies have publicly documented production AG-UI deployments? What were the key architectural decisions they made?

---

AREA 4: Salesforce Platform Technical Details

4a. What is the precise technical integration path for injecting a dynamically-generated Lightning component into an Agentforce conversational response at runtime? Does Agentforce support a "rich response" format that accepts a component schema JSON alongside the text response?

4b. Does the Salesforce Metadata API expose Field Level Security settings (which fields a given profile/user can read) in a format consumable by a Python service? What is the authentication pattern for a server-side Python service to query FLS for the current user's session?

4c. What is the Lightning Data Service (LDS) cache invalidation API? When Beethoven generates a component with data bindings to Salesforce fields, can the client-side component hook into LDS refresh events to update its data without a full re-render?

4d. What are the documented technical constraints on the Salesforce Lightning Message Service (LMS) for cross-component communication in an Agentforce-embedded context? Can a dynamically-generated component publish LMS events that existing page components subscribe to?

4e. Does Salesforce's AppExchange security review process have specific requirements for AI-generated UI components? Are there documented requirements around: data masking evidence, audit log format, FLS enforcement patterns?

---

AREA 5: Market and Enterprise Deployment Evidence

5a. Are there documented production enterprise deployments of semantic caching for AI responses — specifically multi-tier caching with vector similarity search — in a Salesforce or CRM context? What were the measured cache hit rates and latency improvements?

5b. What is the documented production failure rate of freeform AI-generated UI (Claude Artifacts, Gemini Canvas) in enterprise deployments, specifically where users attempted to use these for CRM data visualization? Are there published case studies, enterprise blog posts, or analyst reports covering this?

5c. What analyst firm research (Gartner, Forrester, IDC) has been published specifically on "AI-generated enterprise UI" or "generative UI for CRM" as a market category? What are the market size estimates and adoption projections for 2025-2028?

5d. Are there venture-funded startups specifically targeting AI-generated enterprise UI that could be competitive threats or acquisition targets? What are their technical differentiation points?

5e. What is ServiceNow's Otto AI technical architecture for UI generation? Does it use a component catalog approach similar to A2UI, or is it freeform generation? What is the documented quality/reliability in production?

---

Please provide deep technical detail with sources for each answer. Prioritize production evidence over theoretical claims. Flag explicitly where information is uncertain, estimated, or not publicly documented. I need actionable technical specifications, not high-level summaries.
```

---

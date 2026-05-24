# Share package — for the recipient

You're being given two things:

1. **`blueprint-preview.html`** — a self-contained interactive dashboard. Double-click to open in any browser. No internet required, no install, no server. Works on phone or desktop. Use the tabs across the top to navigate; the **Dashboard Mockups** tab is the most visual.

2. **This prompt** (`share-package.md`) — paste the content below into a fresh Claude conversation (web, desktop, or Claude Code). The system understands the blueprint and can help you customize it, iterate on any section, or generate a fresh version for a different org context.

**Recommended Claude model:** Opus 4.7 (`claude-opus-4-7`) for the heaviest reasoning. Sonnet 4.6 (`claude-sonnet-4-6`) works for most iteration. Haiku (`claude-haiku-4-5-20251001`) for quick edits only.

---

## ⬇️ Paste everything below this line into Claude

---

# Context

You're helping me work with an AI Operating Model Blueprint for a federal program office. The blueprint and its visual preview (`blueprint-preview.html`) are already designed. Your job is to help me **understand, customize, iterate, or extend** it.

## The org the blueprint addresses

| Dimension | Value |
|---|---|
| Total headcount | 400 FTE |
| Direct reports to group head | 6 |
| Active programs | 20 |
| Programs to stand up | 10 (over 12–18 months) |
| Program-staffed FTE | 280 |
| Span function FTE | ~120 (legal, finance, performance eval, ops, comms) |
| Primary work | Policy design + new program stand-up |
| Change mechanism | Inter-agency liaison (most changes flow through external groups) |
| Mission heterogeneity | High |

## The architecture

**Two axes:**
- **6 vertical operating layers (L1–L6):** Executive Intelligence · Program Operations · Span Functions · Cross-Program Intelligence · Inter-Agency Liaison · Performance & Learning
- **7 horizontal capability planes (P1–P7):** Live Operational Dashboard · Universal Action Flow · External Intelligence Ingestion · Context Architecture · Capture Surface · Synthesis & Briefing · Knowledge Graph

Every layer × plane intersection is an explicit contract describing what flows between them.

**Four additional capabilities layered on top of the two-axis model:**

1. **Signal quality gate** — every captured item is triaged on four dimensions (relevance, actionability, novelty, source credibility) before it touches the canonical state. Low-signal items are archived, not surfaced. The gate is what keeps dashboards trustworthy rather than overwhelming.

2. **Hierarchical synthesis** — the same canonical state is synthesized to three different resolutions each morning. Program level (Haiku, 6:30am): full operational detail + peer learning cards + matched external intel. DR level (Sonnet, 7:15am): exceptions only across their span. Group head (Opus, 7:45am): 3–5 signals — decisions, cross-cutting risks, external intel digest. Four minutes to read cover to cover.

3. **Cross-program learning engine** — every program's situations are tagged (commitment slipping, procurement delay, launch delay, etc.) and matched nightly against structurally similar programs that have faced the same situation. Learning cards surface automatically to the relevant program manager — specific, citable, with source and outcome. At 6 months of history, the engine starts making pre-emptive pattern alerts.

4. **Tiered visibility** — three-layer enforcement: (a) Claude Teams/Enterprise project access control, (b) compile-time role-tier state files (staff/PM/DR/group-head/admin), (c) per-tier CLAUDE.md system prompt constraints. Each layer is independently enforced; all three together are required for correct behavior.

## The 18 sections

1. Layered Operating Model Architecture (two-axis)
2. Leverage-vs-Build Decision Matrix
3. Org Structure for the AI Function
4. Per-Program Standard Kit ("Program-in-a-Box")
5. New Program Launch Playbook
6. Inter-Agency Coordination Layer
7. Performance Intelligence System
8. Span Function Automation
9. Governance & Change Management
10. Implementation Sequencing (12-month roadmap)
11. Investment & Resources
12. Risk Register
13. Live Operational Dashboard Plane (P1)
14. Universal Action Flow Plane (P2)
15. External Intelligence Ingestion Plane (P3)
16. Context Architecture & Knowledge Graph Plane (P4 + P7)
17. Capture Surface & Synthesis Layer (P5 + P6)
18. AI-Augmented Product & Policy Design

## Reference patterns the blueprint inherits

The blueprint isn't designed from scratch. ~70% of the dashboard scaffolding reuses code already proven in production at smaller scale, across three GitHub repos:
- **Private-Yoni-Dashboard** — Flask dashboard server, HTML inject pattern, design system, compile pipeline, multi-surface routing
- **Invite-Chief-of-Staff** — knowledge graph builder, knowledge indexer/query, health check framework, cross-reference engine, capture pipelines
- **Private-Yoni-Config** — owner whitelist, document registry, alias registry

Inherited patterns include: HI1 (served-HTML contract), G2 (schema validation), G3 (owner whitelist), AA1 (tombstone discipline), EP1 (edit-in-place), U2 (readthrough cross-reference), PA1 (past-due classification), CF1 (capture freshness), CC1 (Claude Code over API), and more.

## Design principles (binding)

1. **Specificity over themes** — name the system, role, workflow, metric
2. **Leverage-first** — every "build" must answer "why not leverage X?"
3. **Edit-in-place** — augment existing systems, don't replace
4. **Schema discipline** — every artifact has a defined schema, lifecycle, owner, retention
5. **Trace back to source** — every recommendation cites the org parameter or pattern driving it
6. **So-what first** — every section leads with the bottom-line recommendation
7. **Federal-native** — respect FedRAMP, ATO, FAR/DFAR, FOIA, classification, union dynamics
8. **No magic** — default to off-the-shelf Claude Code primitives
9. **Inherit before extending** — cite the reference pattern; only extend with explicit justification
10. **One canonical state** — every dashboard, briefing, registry hydrates from the same compiled state

## What I might ask you to do

Common asks (each gets a different kind of response from you):

### "Explain what I'm looking at"
Walk me through a tab, a section, a mockup, or a capability plane. Plain English. Concrete examples. No jargon unless you define it first.

### "Customize for [my agency / different program mix / different priorities]"
Adapt the blueprint. Tell me what changes, what stays the same, and what assumptions you're making. Flag the patterns that don't translate.

### "Go deeper on Section [X]"
Produce a much more detailed version of that section using the six-part memo structure:
1. THE CORE RECOMMENDATION (1–2 paragraph bottom line)
2. DESIGN DETAIL (substance, with tables where dense)
3. LEVERAGE-VS-BUILD CALLS (reused / built / procured, with reference pattern cited)
4. OPEN QUESTIONS (what the Head of AI must resolve before execution)
5. DEPENDENCIES (other sections, agency functions, external parties)
6. SUCCESS METRIC (how we know in 90 days it's working)

### "Produce the full 18-section blueprint from scratch"
Run the master prompt below to generate a 12,000–20,000 word execution blueprint. Best with Opus 4.7.

### "Draft [a CLAUDE.md template / a skill manifest / a compile script / a deck outline]"
Generate the concrete artifact. Show me the actual file contents, not a description.

### "Mock up [a different dashboard surface]"
Sketch the HTML/CSS for a new dashboard surface in the same design language as the existing mockups (light/cream design system, KPI cards, action queues, filterable tables).

### "Red-team this"
Adversarial review. What does this miss? What stakeholder objections will surface? What patterns don't actually work at federal scale? What's the most likely failure mode?

---

# Master prompt — produces the full blueprint when invoked

Trigger this prompt when I say "produce the full blueprint" or paste it into a fresh chat. Best with **Claude Opus 4.7**.

```
You are a senior McKinsey partner co-leading a Public Sector AI Operating Model engagement, with deep technical expertise in Claude Code deployment at organizational scale (skills, hooks, scheduled tasks, subagents, MCP servers, CLAUDE.md hierarchy, Claude Agent SDK) and working knowledge of federal AI governance (NIST AI RMF, OMB M-24-10, agency CAIO authority, FedRAMP High for GenAI, IL5+ where relevant).

You are co-authoring the execution blueprint with the agency Head of AI. The reader is technical and time-constrained. Output must be specific enough to direct implementation tomorrow — not a strategy deck.

THE ORGANIZATION
- 400 FTE federal policy + program office
- 6 direct reports to group head
- 20 active programs, 10 new to stand up over 12–18 months
- 280 program-staffed, ~120 span function (legal, finance, perf eval, ops, comms)
- Primary work: policy design + new program stand-up
- Change mechanism: inter-agency liaison
- High mission heterogeneity

GOALS
1. Run as efficiently as possible across 400 people
2. Design more (and better) products
3. Extract best practices from high performers and propagate
4. Streamline inter-agency liaison (today's primary bottleneck)
5. Replace annual performance review cycles with continuous evaluation
6. Single live operational picture for every role, hydrated from one source of truth
7. Highest-quality institutional memory, retrievable and self-improving

REFERENCE IMPLEMENTATION (inherit, do not redesign)

The group head has operated a smaller version (2–3 person investment platform) for 18+ months. ~70% of architecture inherits proven patterns. Cite the pattern by name where you adopt it; flag extensions explicitly.

Document discipline (inherit verbatim):
- Six-section memo structure (Core Argument, Consensus, Tension, Open Questions, What You'd Need, Key Names, Action Items)
- Structured Action Items block (ID, absolute YYYY-MM-DD date, verb-first action, owner, parties, context, priority, dashboard routing L1–L6)
- One-sentence summary rule (<25 words)
- Absolute dates only — three-layer enforcement
- Hyperlinks on every reference
- Specificity over themes

Architectural patterns (inherit and scale):
- Tiered execution path (Claude Code direct / managed gateway / FedRAMP-cleared central)
- Per-program canonical files (G4 analog) — charter.md, status.md, actions.md, log.json, stakeholders.md
- Schema validation on compiled state (G2 analog)
- Owner whitelist enforcement (G3 analog)
- Tombstone discipline (AA1 analog)
- Edit-in-place for registered documents (EP1)
- Append-only logs with quarterly roll-off
- Past-due action classification (PA1)
- Next-milestone-future-only (NM1)
- Three-trigger pipelines (capture / synthesis / refresh) per program
- Centralized context manifest (CM1)
- System map preflight (SM1)

Operational discipline:
- Skip-on-API-failure (partial > blocked)
- Pre-push health check
- Learning ledger with stable rule IDs
- Capture freshness check (CF1)
- Tenant-leak detection (TL1/TL2)
- Per-pass model assignment (Sonnet/Opus/Haiku per skill)
- Verbal-update four-layer persistence
- Outstanding requests at every output end (OR1)

Capability planes (inherit pattern, scale infrastructure):
- P1 Live Operational Dashboard (cos-dashboard-server.py + HI1 contract)
- P2 Universal Action Flow (4-layer persistence + G3 + AA1 + PA1)
- P3 External Intelligence Ingestion (podcast/research pipelines + U2 readthrough)
- P4 Context Architecture (CLAUDE.md hierarchy + learning ledger + registries)
- P5 Capture Surface (Otter + call recorder + Gmail Mini + file router)
- P6 Synthesis & Briefing (daily/weekly briefings + six-section memo)
- P7 Knowledge Graph (people CRM + alias directory + practice patterns)

MCP integration surface inherits: Drive, Gmail, Calendar, MS Graph, Chrome DevTools, scheduled tasks. At scale: agency DM, case mgmt, data warehouse, Federal Register API, regulations.gov, MAX.gov, LRM, inter-agency workspaces, FOIA tool, contract writing, budget execution.

CURRENT-STATE INVENTORY (must enumerate before designing)

For each: Exists? / Owner / ATO status / Integration path / Leverage decision.

A. Foundational infrastructure (identity, network, GenAI access, data lake, DM, case mgmt, calendar/email, records, FOIA, eDiscovery)
B. Existing AI initiatives (CAIO posture, other pilots, shared services AI, Anthropic procurement vehicles)
C. Cross-agency coordination tools (working group calendars, OMB cadences, WH clearance, shared workspaces)
D. Workforce posture (unions, AI MOUs, training, detailees/IPAs, contractor support)
E. Existing dashboards & briefing infrastructure
F. Existing capture surface
G. Existing knowledge management

BLUEPRINT — 18 SECTIONS, REQUIRED STRUCTURE

Every recommendation specifies: (1) what it is, (2) leverage/build/procure, (3) owner, (4) sequence/dependencies, (5) ATO/compliance path, (6) success metric, (7) cost order-of-magnitude, (8) reference pattern inherited, (9) capability plane consumed/contributed.

1. Layered Operating Model Architecture (two-axis: 6 layers × 7 planes)
2. Leverage-vs-Build Decision Matrix (consolidated table; default leverage-first)
3. Org Structure for the AI Function (where AI team sits, size, roles, interfaces, talent sources, decision rights)
4. Per-Program Standard Kit "Program-in-a-Box" (CLAUDE.md template, skill set, pipelines, MCP, dashboard, KG wiring, <14d to live)
5. New Program Launch Playbook (pre-launch, stand-up, live, anti-patterns; templated checklist)
6. Inter-Agency Coordination Layer (commitment capture, stale-item surfacing, integration with PCs/DCs/IPCs, classification)
7. Performance Intelligence System (telemetry, best-practice extraction, underperformance detection, decision support, feedback loop)
8. Span Function Automation (legal, finance, perf eval, operations, comms — specific workflows)
9. Governance & Change Management (committee, approval workflow, model change mgmt, risk, audit trail, workforce, training, sunset)
10. Implementation Sequencing (4 phases over 6 months with go/no-go gates: Days 0–14, 15–45, 46–90, 91–180)
11. Investment & Resources (headcount, tech spend, contractor, training, ATO, total $)
12. Risk Register (top 10 with probability × impact, mitigation owner, action, residual)
13. Live Operational Dashboard Plane (P1 — server fork, multi-surface, PIV/CAC, HI1 contract)
14. Universal Action Flow Plane (P2 — extraction, routing, 4-layer persistence, lifecycle)
15. External Intelligence Ingestion Plane (P3 — feed taxonomy, pipelines, readthrough engine, digests)
16. Context Architecture & Knowledge Graph Plane (P4+P7 — layered CLAUDE.md, memory taxonomy, learning ledger, registries, graph)
17. Capture Surface & Synthesis Layer (P5+P6 — every input modality, multi-tier briefings)
18. AI-Augmented Product & Policy Design (analog-program-scan, design-option-generator, design-red-team, stakeholder-impact-simulator, draft-implementing-guidance, comparative-design-memo, clearance-path-mapper, authority-traceability-check; practice patterns library)

DESIGN PRINCIPLES (binding)

1. Specificity over themes
2. Leverage-first (every build justifies "why not leverage X?")
3. Edit-in-place
4. Schema discipline
5. Trace back to source
6. So-what first
7. Federal-native
8. No magic
9. Inherit before extending
10. One canonical state

ANTI-PATTERNS (reject)

- Generic "AI strategy" without workflows/owners/systems
- Build where commercial/existing options exist
- Pilots without scale-up path
- Tools without user journey
- Recommendations ignoring inter-agency or compliance dependencies
- Org structures that don't engage CIO/CDO/CISO/CAIO
- Adoption assumptions without change management
- Workflows producing official artifacts without HITL review
- Reinventing patterns the reference implementation already proves
- Parallel data sources for the same canonical fact
- Dashboards/briefings bypassing universal action flow
- Skill libraries containing program-specific data (tenant leak)
- External intelligence ingested without readthrough matching

OUTPUT FORMAT

Each of the 18 sections uses the six-part memo structure:
1. THE CORE RECOMMENDATION (1–2 paragraph bottom line)
2. DESIGN DETAIL (substance, with tables where dense)
3. LEVERAGE-VS-BUILD CALLS (explicit list with pattern cited + plane consumed)
4. OPEN QUESTIONS (what Head of AI must resolve)
5. DEPENDENCIES (other sections, agency functions, external parties)
6. SUCCESS METRIC (90-day check)

Append at end:
- MASTER LEVERAGE-VS-BUILD TABLE
- CRITICAL PATH (Mermaid graph LR with milestones + gates)
- REFERENCE PATTERN INHERITANCE MAP
- CAPABILITY PLANE × OPERATING LAYER MATRIX (42 cells)
- CANONICAL STATE SCHEMA (top-level JSON shape)
- OPEN QUESTIONS REGISTER (grouped by owner)
- ACTION ITEMS (verb-first, owner, absolute YYYY-MM-DD, dashboard routing L1–L6, priority)
- OUTSTANDING REQUESTS

DELIVERY MODE

One pass, 12,000–20,000 words. If any input is missing (specific systems, program names, classification, agency identity), make a stated assumption and flag it in OPEN QUESTIONS for that area — do not stall. Reader is the Head of AI; assume technical literacy and deep agency context. No glossary, no AI 101.

Begin.
```

---

## File index (what should be alongside this prompt)

| File | Purpose |
|---|---|
| `blueprint-2026-05-24.md` | **The full 22,000-word execution blueprint** — all 18 sections with the six-part memo structure, plus appendices (leverage-vs-build table, critical path, pattern inheritance map, 42-cell matrix, schema reference, open questions register, action items). The actual document, not a stub. |
| `blueprint-preview.html` | Self-contained interactive dashboard — open in any browser. The visual companion to the blueprint document. |
| `share-package.md` | This file — paste the section above into Claude |

If you also have access to these (optional, deeper context):
| File | Purpose |
|---|---|
| `blueprint-state.schema.json` | JSON Schema for the canonical state — defines every dashboard field |
| `blueprint-state.example.json` | Populated sample state — what the production system feeds the dashboard |
| `fork-plan.md` | Runnable spec for building the production dashboard (~4 days with Claude Code) |
| `section-18-product-design.md` | Standalone Section 18 spec (AI-augmented product/policy design) |

## Quickstart — pick one

- **"I want to understand the architecture before I read anything"** → ask Claude: *"Walk me through the two-axis model with a concrete example using a real federal program."*
- **"I want to see what a different agency would look like"** → ask Claude: *"Customize this for [agency name + mission]. Tell me what changes and what stays the same."*
- **"I want to read the full blueprint"** → open `blueprint-2026-05-24.md`. 22,000 words, 18 sections + appendices. Start with the Executive Summary, then drill into sections of interest.
- **"I want to regenerate the blueprint for a different org"** → paste the master prompt above into a fresh Opus 4.7 chat with your org parameters substituted.
- **"I want to mock up a new dashboard surface"** → ask Claude: *"Mock up a [type] dashboard using the same design language as the existing mockups."*
- **"I want to pressure-test the plan"** → ask Claude: *"Red-team this blueprint. What's the most likely failure mode in the first 90 days?"*
- **"I want to understand how information flows up and down the hierarchy"** → ask Claude: *"Walk me through the hierarchical synthesis model — what does the group head see vs. a DR vs. a program manager, using a concrete example like a slipping inter-agency commitment."*
- **"I want to understand the external intelligence capability"** → ask Claude: *"How does the external intelligence ingestion work? Walk through the Federal Register → program dashboard pipeline end to end."*
- **"I want to understand cross-program learning"** → ask Claude: *"Explain the peer learning propagation engine. How does a resolved problem in Program 14 surface as a learning card for Program 7 facing the same situation?"*
- **"I want to understand tiered visibility"** → ask Claude: *"Explain the three-layer tiered visibility enforcement. What does each layer protect, and what breaks if only one or two layers are enforced?"*

# Blueprint Dashboard — Fork & Build Plan

**Runnable spec.** Anyone with shell access to your Mac (or a McKinsey engineer with the three repo clones) can execute this sequence and have a working blueprint visualization in ~3–4 days. Everything inherits from Private-Yoni-Dashboard and Invite-Chief-of-Staff; minimal net-new code.

---

## Prerequisites

- Clones of all three repos:
  - [Private-Yoni-Dashboard](https://github.com/ygontownik/Private-Yoni-Dashboard)
  - [Invite-Chief-of-Staff](https://github.com/ygontownik/Invite-Chief-of-Staff)
  - [Private-Yoni-Config](https://github.com/ygontownik/Private-Yoni-Config)
- Python 3.11+
- The completed blueprint output from running the master prompt (the 18-section markdown)
- The two artifacts in this folder: `blueprint-state.schema.json`, `blueprint-state.example.json`

---

## Step 1 — Spin up the fork

```bash
cd ~
git clone https://github.com/ygontownik/Private-Yoni-Dashboard.git blueprint-dashboard
cd blueprint-dashboard
git checkout -b blueprint-mode
```

Set up Python env (mirrors existing):

```bash
python3.11 -m venv .venv
source .venv/bin/activate
pip install -r app/requirements.txt
pip install jsonschema  # net-new; for schema validation
```

---

## Step 2 — File mapping (what to keep, copy, modify, leave)

| File / Directory in fork | Action | Notes |
|---|---|---|
| `app/cos-dashboard-server.py` | **COPY → `app/blueprint-server.py`** | Reuse Flask app structure, port binding pattern, cache + warmup/refresh API. Edit route list to match blueprint surfaces (see Step 4). |
| `app/cos-dashboard-refresh.py` | **COPY → `app/blueprint-refresh.py`** | Reuse compile-to-JSON pattern. Source is blueprint markdown + YAML (instead of deal/recruit data). Output is `data/compiled/blueprint-state.json`. |
| `app/cos-dashboard-fetch.py` | **KEEP AS-IS** | Cache fetch is data-shape-agnostic. |
| `app/templates/cos-dashboard.template.html` | **COPY → `app/templates/blueprint-dashboard.template.html`** | Reuse the HTML inject + window-global setter pattern. Replace setters with the blueprint set (see schema `meta.served_html_contract_required_setters`). |
| `app/templates/_topnav.html` | **MODIFY** | Replace nav links: Exec Summary / Layer×Plane / Sections / Roadmap / Leverage-Build / Risks / Open Questions / Pattern Inheritance / Knowledge Graph / Admin. |
| `app/templates/admin-dashboard.html` | **COPY → `app/templates/blueprint-admin.html`** | Reuse the admin pattern; admin tab in blueprint shows compile freshness, served-HTML contract check, owner registry health. |
| `app/static/design-system.css` | **KEEP AS-IS** | Light/cream tokens already match your UI rule. |
| `app/lib/gap_detector.py` | **KEEP AS-IS** | Used by anomaly surfaces (programs at risk, blocked actions). |
| `app/lib/prioritize.py` | **KEEP AS-IS** | Two-signal gate reused for action prioritization. |
| `app/knowledge_api.py` | **MODIFY** | Point at blueprint knowledge graph seed instead of dashboards knowledge index. |
| `data/compiled/` | **REUSE PATH, NEW FILE** | Write `data/compiled/blueprint-state.json` here. |
| `data/user-state/deletions.json` | **REUSE PATTERN, NEW FILE** | Create `data/user-state/blueprint-tombstones.json` for dismissed actions/risks. |
| `routines/compile/` | **COPY ONE FILE** | Copy one compile routine as scaffold for `routines/compile/blueprint-compile.py`. |
| `tests/` | **REUSE PATTERN** | Add `tests/test_blueprint_schema.py` that validates `blueprint-state.json` against `blueprint-state.schema.json`. |

---

## Step 3 — Pull leverage from cos-pipeline (no fork; reference by submodule or copy)

| Capability | Source path | Action |
|---|---|---|
| Knowledge graph builder | `~/cos-pipeline/tools/entity_graph_build.py` | **Vendor in** at `app/lib/entity_graph_build.py`; feed it the blueprint knowledge_graph_seed |
| Knowledge indexer | `~/cos-pipeline/tools/knowledge_indexer.py` | **Vendor in**; index the 18 sections + open questions for search |
| Knowledge query | `~/cos-pipeline/tools/knowledge_query.py` | **Vendor in**; powers the Section Browser search box |
| Health check framework | `~/cos-pipeline/tools/checks/` | **Vendor in** the `checks/` directory; add `check_blueprint_g2.py`, `check_blueprint_g3.py`, `check_blueprint_hi1.py` mirroring the existing checks |
| Reference integrity audit | `~/cos-pipeline/tools/reference_integrity_audit.py` | **Vendor in**; run pre-deploy to validate every pattern_inheritance entry resolves |
| Learning ledger sync | `~/cos-pipeline/tools/sync_learnings.py` | **Vendor in** if you want the blueprint to track its own learnings as it evolves |
| Cross-reference / readthrough | `~/cos-pipeline/tools/cross_reference_briefing.py` | **Vendor in**; powers the External Intelligence → Program readthrough surface (P3 demo) |
| System map generator | `~/cos-pipeline/tools/generate-system-map.py` | **Vendor in**; generates Mermaid system map of the blueprint dashboard itself |

---

## Step 4 — Routes to wire in `app/blueprint-server.py`

**Two route groups: blueprint content (design tool) and live operational surfaces (role-stratified).**

Mirror the existing route pattern from `cos-dashboard-server.py`. Role-stratified routes require `@require_role(tier)` decorator that validates PIV/CAC SSO role attribute against the `role_visibility.route_guards` config.

```python
# ── Blueprint content (design / iteration) ───────────────────────────────
@app.route("/")
def executive_summary(): ...

@app.route("/layer-plane")
def layer_plane_matrix(): ...

@app.route("/sections")
def section_browser(): ...

@app.route("/sections/<int:section_id>")
def section_detail(section_id): ...

@app.route("/roadmap")
def roadmap_view(): ...

@app.route("/leverage-build")
def leverage_build_matrix(): ...

@app.route("/risks")
def risk_register(): ...

@app.route("/open-questions")
def open_questions(): ...

@app.route("/pattern-inheritance")
def pattern_inheritance(): ...

@app.route("/knowledge-graph")
def knowledge_graph(): ...

@app.route("/admin")
def admin(): ...

# ── Role-stratified operational surfaces (production) ─────────────────────
# Each route loads a different pre-compiled state file and synthesis layer.
# Role guard enforced by require_role() — reads PIV/CAC SSO role attribute.

@app.route("/exec")
@require_role("group_head")
def exec_surface():
    state = load_compiled("state-grouphead.json")
    return render_template("blueprint-exec.template.html", **state)

@app.route("/dr/<dr_id>")
@require_role("dr")
def dr_surface(dr_id):
    state = load_compiled("state-dr.json")
    dr_state = [d for d in state["synthesis_layers"]["dr"] if d["dr_id"] == dr_id]
    return render_template("blueprint-dr.template.html", dr=dr_state, **state)

@app.route("/program/<program_id>")
@require_role("program_manager")
def program_surface(program_id):
    state = load_compiled("state-pm.json")
    prog_state = [p for p in state["synthesis_layers"]["program"] if p["program_id"] == program_id]
    return render_template("blueprint-program.template.html", program=prog_state, **state)

@app.route("/submit")
@require_role("staff")
def staff_submit():
    state = load_compiled("state-staff.json")
    return render_template("blueprint-submit.template.html", **state)

@app.route("/intel")
@require_role("program_manager")
def external_intel():
    state = load_compiled("state-pm.json")
    return render_template("blueprint-intel.template.html", **state)

@app.route("/performance")
@require_role("dr")
def performance_pulse():
    state = load_compiled("state-dr.json")
    return render_template("blueprint-performance.template.html", **state)

@app.route("/inter-agency")
@require_role("program_manager")
def inter_agency():
    state = load_compiled("state-pm.json")
    return render_template("blueprint-interagency.template.html", **state)

# ── API ──────────────────────────────────────────────────────────────────
@app.route("/warmup", methods=["POST"])
def warmup(): ...

@app.route("/refresh", methods=["POST"])
def refresh(): ...

@app.route("/cache-status")
def cache_status(): ...
```

Bind to port `7780` (sibling of `7777`):

```python
app.run(host="0.0.0.0", port=7780)
```

---

## Step 5 — Compile pipeline (`app/blueprint-refresh.py`)

**Two-pass compile: (1) build canonical master state, (2) generate five role-tier state files + synthesis layers.**

Input sources:
- Master blueprint markdown (the 18-section output from the prompt): `data/source/blueprint-2026-05-24.md`
- Org parameters YAML: `data/source/org-parameters.yaml`
- Owner whitelist YAML: `data/source/owner-whitelist.yaml` (mirror format of `firm_context.yaml`)
- Pattern catalog YAML: `data/source/pattern-catalog.yaml` (the reference patterns to inherit)
- External intelligence YAML: `data/source/external-intel-feed.yaml` (ingested items from P3 pipeline)
- Program similarity map: `data/compiled/similarity-map.json` (generated by Opus quarterly)

Output:
- `data/compiled/blueprint-state.json` — master state (admin only; validates against schema)
- `data/compiled/state-staff.json` — staff tier
- `data/compiled/state-pm.json` — program manager tier
- `data/compiled/state-dr.json` — DR tier
- `data/compiled/state-grouphead.json` — group head tier

Pseudocode:

```python
def compile():
    # ── Pass 1: Build master canonical state ─────────────────────────────
    sections = parse_markdown_sections("data/source/blueprint-2026-05-24.md")
    org_params = load_yaml("data/source/org-parameters.yaml")
    owners = load_yaml("data/source/owner-whitelist.yaml")
    patterns = load_yaml("data/source/pattern-catalog.yaml")
    external_intel_raw = load_yaml("data/source/external-intel-feed.yaml")

    # Run signal quality triage on any new external intel items
    external_intel = run_signal_quality_triage(external_intel_raw, sections)

    state = {
        "meta": build_meta(),
        "org_parameters": org_params,
        "operating_layers": extract_layers(sections),
        "capability_planes": extract_planes(sections),
        "sections": sections,
        "roadmap": extract_roadmap(sections[9]),       # section 10
        "leverage_build_matrix": extract_lb_matrix(sections),
        "pattern_inheritance": patterns,
        "knowledge_graph_seed": load_yaml("data/source/kg-seed.yaml"),
        "risks": extract_risks(sections[11]),           # section 12
        "open_questions": extract_open_questions(sections),
        "actions": extract_actions(sections),
        "costs": extract_costs(sections[10]),           # section 11
        "owner_whitelist": owners,
        "glossary": load_yaml("data/source/glossary.yaml"),
        "tombstones": load_json("data/user-state/blueprint-tombstones.json"),
        "external_intelligence": external_intel,
        "peer_learnings": load_json("data/compiled/peer-learnings.json"),
        "best_practices": load_yaml("data/source/best-practices.yaml"),
        "role_visibility": load_yaml("data/source/role-visibility.yaml"),
        "program_similarity_map": load_json("data/compiled/similarity-map.json"),
        "synthesis_layers": {},  # filled in Pass 2
    }

    validate_against_schema(state, "blueprint-state.schema.json")
    run_g2_check(state)   # required fields
    run_g3_check(state, owners)   # owner whitelist
    run_pa1_check(state)  # past-due classification

    write_json("data/compiled/blueprint-state.json", state)

    # ── Pass 2: Generate synthesis layers (model per tier) ───────────────
    # Run in parallel — each tier is independent
    synthesis = {
        "program": run_program_synthesis(state, model="haiku"),   # 6:30am cadence
        "dr":      run_dr_synthesis(state, model="sonnet"),       # 7:15am cadence
        "group_head": run_grouphead_synthesis(state, model="opus") # 7:45am cadence
    }
    state["synthesis_layers"] = synthesis
    write_json("data/compiled/blueprint-state.json", state)  # update with synthesis

    # ── Pass 3: Generate role-tier state files ────────────────────────────
    role_visibility = state["role_visibility"]
    for target in role_visibility["compile_targets"]:
        tier_state = project_state_for_tier(state, target)
        write_json(f"data/compiled/{target['output_file']}", tier_state)

    notify_warmup_endpoint()

def run_signal_quality_triage(items, sections):
    """Four-dimension triage: relevance, actionability, novelty, source credibility.
    Items below composite threshold → routing_decision='archived'.
    Run first 30 days in audit_mode=True (log but don't suppress) to tune thresholds."""
    return [triage_item(item, sections) for item in items]

def project_state_for_tier(state, target):
    """Build a filtered state dict for a given role tier.
    Only include fields in target['included_fields'].
    Apply program_scope filter (own_only / dr_span / full_portfolio).
    Never include fields in target['excluded_fields']."""
    ...
```

---

## Step 6 — HTML templates (one per role tier + blueprint content views)

Critical: inherit HI1 pattern. **Each role-tier template loads only the window-globals for its tier.** Blueprint content templates load the full set. Contract check runs at deploy time for every template.

**Blueprint content template** (`blueprint-dashboard.template.html`) — full setter list:
```html
<script>
window.__BLUEPRINT_META__ = {{ meta | tojson }};
window.__LAYERS__ = {{ operating_layers | tojson }};
window.__PLANES__ = {{ capability_planes | tojson }};
window.__SECTIONS__ = {{ sections | tojson }};
window.__ROADMAP__ = {{ roadmap | tojson }};
window.__LEVERAGE_BUILD__ = {{ leverage_build_matrix | tojson }};
window.__RISKS__ = {{ risks | tojson }};
window.__OPEN_QUESTIONS__ = {{ open_questions | tojson }};
window.__ACTIONS__ = {{ actions | tojson }};
window.__OWNER_WHITELIST__ = {{ owner_whitelist | tojson }};
window.__EXTERNAL_INTEL__ = {{ external_intelligence | tojson }};
window.__PEER_LEARNINGS__ = {{ peer_learnings | tojson }};
window.__BEST_PRACTICES__ = {{ best_practices | tojson }};
window.__SYNTHESIS__ = {{ synthesis_layers | tojson }};
window.__ROLE_TIER__ = {{ role_tier | tojson }};
</script>
```

**Group head template** (`blueprint-exec.template.html`) — restricted setter list:
```html
<script>
window.__BLUEPRINT_META__ = {{ meta | tojson }};
window.__SYNTHESIS__ = {{ synthesis_layers.group_head | tojson }};
window.__EXTERNAL_INTEL__ = {{ external_intelligence | tojson }};
window.__BEST_PRACTICES__ = {{ best_practices | tojson }};
window.__ROLE_TIER__ = "group_head";
/* No __ACTIONS__, __OPEN_QUESTIONS__, __PEER_LEARNINGS__ — not in group head tier */
</script>
```

**DR template** (`blueprint-dr.template.html`), **PM template** (`blueprint-program.template.html`), **staff template** (`blueprint-submit.template.html`) — follow same pattern, each including only what `role_visibility.compile_targets` specifies for their tier.

Contract check — run for every template at deploy:
```bash
python app/lib/check_blueprint_hi1.py http://localhost:7780/
python app/lib/check_blueprint_hi1.py http://localhost:7780/exec
python app/lib/check_blueprint_hi1.py http://localhost:7780/dr/dr-energy
python app/lib/check_blueprint_hi1.py http://localhost:7780/program/prog-lgp
```

---

## Step 7 — Surface render functions (JS, in template `<script>` blocks or separate `app/static/blueprint.js`)

Each surface reads from window globals; no fetch per surface (canonical state already hydrated). Reuse existing render patterns from `cos-dashboard.template.html`.

**Blueprint content surfaces** (design/iteration tool, Day 2–4):
1. **Executive Summary** (re-skin of existing CoS top-of-page) — half-day
2. **Section Browser** (re-skin of existing follow-ups list with detail pane) — half-day
3. **Layer × Plane Matrix** (new; CSS grid 6×7, click loads cell content) — half-day
4. **Leverage-vs-Build Matrix** (re-skin of follow-ups table with filters) — couple hours
5. **Risk Register** (re-skin of follow-ups table sorted by P×I) — couple hours
6. **Open Questions** (re-skin of actions queue) — couple hours
7. **Roadmap Gantt** (new; horizontal swimlane HTML table or simple lib like [Frappe Gantt](https://frappe.io/gantt)) — half-day
8. **Critical Path** (hand-coded HTML/CSS timeline — no external lib) — couple hours
9. **Knowledge Graph** (new; lightweight — Vis.js Network or D3; render from `knowledge_graph_seed.edges`) — half-day
10. **Pattern Inheritance Map** (re-skin of follow-ups table; rows = patterns, columns = sections inheriting) — couple hours
11. **Admin** (port of existing admin-dashboard.html) — half-day

**Role-stratified operational surfaces** (production, Month 4–5):
12. **Group Head `/exec`** — reads `__SYNTHESIS__.group_head.signals`. 3–5 signal cards + decisions inbox + portfolio traffic light + external intel digest. No action-item detail.
13. **DR `/dr/:dr_id`** — reads `__SYNTHESIS__.dr` filtered to dr_id. Exception cards per program + cross-program coordination alerts. Click-through to program detail.
14. **Program Manager `/program/:id`** — reads `__SYNTHESIS__.program` + `__ACTIONS__` + `__PEER_LEARNINGS__` + `__EXTERNAL_INTEL__` (program-matched only). Full operational detail.
15. **External Intelligence `/intel`** — reads `__EXTERNAL_INTEL__`. Tier filter chips, relevance scores, so-what annotations, stakeholder intelligence section.
16. **Staff Submit `/submit`** — reads `__ACTIONS__` (own only). Capture forms: verbal update, status update, commitment update. Confirmation of what was stored.

---

## Step 8 — Local test plan

```bash
# 1. Compile
python app/blueprint-refresh.py

# 2. Validate
python -c "import json, jsonschema; \
  schema = json.load(open('blueprint-state.schema.json')); \
  state = json.load(open('data/compiled/blueprint-state.json')); \
  jsonschema.validate(state, schema); print('OK')"

# 3. Run health checks
python app/lib/check_blueprint_g2.py
python app/lib/check_blueprint_g3.py
python app/lib/check_blueprint_pa1.py

# 4. Start server
python app/blueprint-server.py

# 5. Smoke
open http://localhost:7780/
python app/lib/check_blueprint_hi1.py http://localhost:7780/

# 6. Test action flow (P2 demo)
curl -X POST http://localhost:7780/actions \
  -H "Content-Type: application/json" \
  -d '{"verb_first_action":"Test action","owner":"AI Lead","due_date":"2026-07-01","dashboard_routing":{"layer":"L1","surfaces":["executive"]}}'

# 7. Re-compile, confirm action appears on Executive Summary
python app/blueprint-refresh.py
curl -X POST http://localhost:7780/warmup
# refresh browser; action visible
```

---

## Step 9 — Deploy to McKinsey-shareable env

Options ranked:

| Option | Effort | Notes |
|---|---|---|
| **Tailscale to local Mac** | Hours | Already in your stack (per memory `project_subscriber_onboarding.md`). Add McKinsey engineers to your tailnet; they hit `http://blueprint-dashboard:7780/`. Zero infra spend, full control. |
| **AWS Lightsail / EC2 small instance** | Half-day | Public-internet hosting with basic auth. Throwaway after engagement. |
| **AWS GovCloud / Azure Gov FedRAMP target** | 2–4 weeks | Only when ready to demo to actual agency. ATO inheritance from existing tenant. |

Recommend: **Tailscale for McKinsey iteration → FedRAMP gov cloud only when Head of AI signs off and you're moving to real deploy.**

---

## Step 10 — Companion CAIO deck (optional, half-day)

Generate from same canonical state. Use [Marp](https://marp.app) markdown → PPT.

```bash
pip install marp-cli  # or npm
python app/blueprint-deck-gen.py > deck.md
marp deck.md --pptx -o blueprint-deck.pptx
```

Where `app/blueprint-deck-gen.py` reads `data/compiled/blueprint-state.json` and emits a 15-slide Marp markdown:
1. Title
2. The Ask
3. Org Parameters
4. The Two-Axis Architecture (L1–L6 × P1–P7)
5–11. Each layer/plane summary (one slide each, key 3)
12. 12-Month Roadmap (one Gantt screenshot exported from the dashboard)
13. Leverage-vs-Build Top 10 (table)
14. Top 5 Risks
15. The Ask (cost, headcount, ATO timeline, lighthouse program nominations)

---

## Effort Summary

| Day | Output |
|---|---|
| Day 1 | Run master prompt → blueprint markdown. Fork repo, set up env. |
| Day 2 | Compile pipeline + schema validation. Executive Summary + Section Browser surfaces live. |
| Day 3 | Layer×Plane Matrix + Leverage-Build + Risks + Open Questions surfaces. |
| Day 4 | Roadmap + Critical Path + Knowledge Graph + Pattern Inheritance + Admin. Tailscale deploy. |
| Day 5 (optional) | CAIO deck generator + half-day polish. |

**Total: 4 days for working dashboard, 5 days with deck. ~70% of the code already exists in your three repos.**

---

## What you bring to the McKinsey kickoff

1. This fork-plan.md (the runnable spec)
2. blueprint-state.schema.json (the canonical contract)
3. blueprint-state.example.json (the populated example)
4. The master prompt (the 18-section blueprint generator)
5. The three GitHub repo URLs (the leverage inventory)

The McKinsey team can execute this plan in parallel with the substantive blueprint design conversations. By the time you have the cleared 18-section blueprint, the dashboard is rendering it.

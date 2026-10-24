---
name: product-blueprint
metadata:
  compatible_agents: [claude-code]
  tags: [requirements, roadmap, architecture, mockups, html, product-planning, prototype]
description: >
  Turns a product's raw source material — a business requirements (BR) document, prototype
  screenshots, a Google Drive folder or a local docs folder — into one tabbed HTML blueprint:
  English requirements with phase, MoSCoW priority and prototype coverage, non-functional
  requirements the BR doc implies but never states, an architecture section with state machines,
  entities, the key contract and Pest tests for the riskiest behaviour, a phased roadmap with exit
  criteria, a sprint-level delivery plan with risks, and HTML mockups for the screens the prototype
  does not have yet. Use this skill whenever the user shares requirements or a prototype for a new
  product and asks for "requirements, roadmap, plan, mockups", "study this folder", "turn this BR
  into a plan", "what's missing from the prototype", "kaji folder ni", "buat requirement dan
  roadmap", "rangka pelan projek dari BR ni", or "buat mockup untuk skrin yang belum ada". Pairs
  with project-requirements for a formal SRS and project-roadmap for a standalone ROADMAP.md, and
  hands off to product-brand-kit once the product is scoped.
---

# Product Blueprint

Reads every source file a product has, then produces a single blueprint page a founder can
validate before anyone writes code. The blueprint argues for an order of work. It does not just
restate the requirements.

## Command Reference

| Command | Description |
|---|---|
| `/blueprint build` | Study the source folder and produce the full blueprint |
| `/blueprint requirements` | Requirements tab only: BRs in English, NFRs, constraints, open questions |
| `/blueprint architecture` | Architecture tab only: layers, state machines, entities, contract, tests |
| `/blueprint roadmap` | Roadmap and delivery plan tabs only |
| `/blueprint mockups` | Mockups for the screens the prototype is missing |

---

## When to Use

- A new product has a BR document (often in Bahasa Malaysia) and/or a clickable prototype
- The user wants to validate architecture and order of work before implementation
- A prototype exists and the user wants to know what it does not yet prove
- A pilot user, partner or client needs one page that explains scope, phases and risks

**Do not** use this skill for an SRS with traceability (use `project-requirements`), a roadmap
for an existing codebase (use `project-roadmap`), or naming and branding (use `product-brand-kit`).

---

## Step 1: Study Every Source File

1. List the folder recursively. On Google Drive, query `parentId = '<id>'` for each folder and
   follow every `nextPageToken`. Don't stop at the first level.
2. Read every requirements document in full, including comments. Keep the BR IDs exactly as written.
   Translate each requirement into plain English when the source is in another language.
3. Read any README or index file first. It often maps the screenshot filenames to screen names.
4. Read the screenshots. Desktop captures are enough; read the phone captures only for mobile-only
   behaviour.
5. Record the prototype's visual direction: surface and nav colours, accent, radius, tone of copy.
   The mockups must look like they belong to the same product.
6. Read the project's `CLAUDE.md` (if any) for the stack, conventions and the company identity: legal name,
   registration number, public contact. Never invent these.

---

## Step 2: Classify Each Requirement

For every BR, decide three things and keep the evidence:

| Field | Values | Rule |
|---|---|---|
| Phase | P1 … P4 | P1 is the riskiest integration that justifies the product's existence |
| Priority | Must / Should / Could | MoSCoW within its phase |
| Prototype status | Prototyped / Simulated / Partial / Not started | Simulated = screen exists, behaviour faked in the browser |

Count the statuses. The coverage tiles on the Summary tab come from these counts, never estimates.

---

## Step 3: Write the Blueprint

One self-contained HTML file with six tabs, in this order. Section contents and layout are in
`references/blueprint-sections.md`.

1. **Summary**:
   - a TL;DR of 4–6 bullets
   - what's in the folder
   - prototype coverage tiles
   - five "decisions to validate first", as a table: decision, options, recommendation, why
2. **Requirements**:
   - every BR in English, filterable by phase and prototype status
   - non-functional requirements (`references/nfr-checklist.md`)
   - external constraints (third-party APIs, review processes)
   - open questions for the team
3. **Architecture**:
   - module layers
   - separate state machines for planning and for execution
   - entity table (UUID public IDs, internal bigint)
   - the key contract, enum and invokable action
   - Pest tests for the riskiest behaviour
   - MCP surface, if AI is in scope
   - package-worthy abstractions (`references/contract-patterns.md`)
4. **Roadmap**:
   - a Gantt grid plus one card per phase: dates, BR chips, a measurable exit criterion
   - Phase 0 always validates access and decisions, and starts slow external approvals in parallel
5. **Delivery plan**:
   - roles
   - two-week sprints in detail for Phase 0–1, epics after that
   - the MVP acceptance walkthrough
   - a risks table
6. **Mockups**:
   - only the screens the prototype lacks, rendered inside the prototype's own app chrome
   - a picker, with the BR IDs each mockup covers
   - sample data visibly labelled as sample

Output target:
- **Artifact tool available** (claude.ai / Cowork): publish the page as an artifact.
- **Otherwise**: write `docs/blueprint/<product>-blueprint.html` and tell the user the path.

---

## Step 4: Hold the Line on Judgement

- Lead the MVP with the riskiest integration (the third-party API, the payment flow, the hardware). Planning screens,
  analytics and AI come after, because a prototype usually already proves them.
- When AI acts in the product, a human gate is the default. The MCP surface may draft, suggest and
  read. It never approves, publishes, pays or deletes.
- Any external limit you didn't verify this session (API caps, review timelines, caption lengths) is marked
  "re-check against live docs in Phase 0".
- Recommend where the product lives (standalone vs inside an existing app) and say why in one line.

---

## Common Rationalizations

| Rationalization | Reality |
|---|---|
| "The BR doc is the requirements, I'll just translate it" | A BR doc never states tenancy, idempotency, secrets, audit retention or privacy law. Those NFRs are where production incidents come from. |
| "The prototype covers most of it" | A browser-local prototype proves the workflow, not the integration. Count Simulated separately from Prototyped. |
| "I'll mock up every screen" | Mocking screens the prototype already has duplicates work and hides what's actually new. |
| "Phase 1 should be the planning screens, they're easiest" | Easy-first defers the risk that can kill the product. Lead with the integration nobody has proven yet. |
| "The API limits are well known" | Platform limits change. Unverified numbers go into the blueprint flagged for Phase 0. |
| "AI can publish if the user trusts it" | One bad auto-publish costs more than every draft saved. Keep approve and publish out of the AI surface. |

---

## Red Flags

- A BR ID is missing from the requirements table, or two BRs were merged
- The coverage tiles don't add up to the BR count
- A phase card has no measurable exit criterion
- Phase 0 doesn't start the slow external approvals (app review, API access, procurement)
- The mockups use a different visual language from the prototype
- Prices, hosting regions, dates or company details appear without a source
- The architecture has one state machine for both planning and execution

---

## Verification

- [ ] Every source file in the folder (including subfolders) was read, and the summary names them
- [ ] Every BR appears once, in English, with phase, priority and prototype status
- [ ] Coverage tile counts match the table
- [ ] NFRs cover access, isolation, timing, idempotency, secrets, audit, media, privacy, observability, mobile
- [ ] Each phase has dates, BR chips and an exit criterion someone could test
- [ ] Mockups exist only for screens the prototype lacks, each listing the BRs it covers
- [ ] Unverified external limits are flagged; nothing in the page is invented
- [ ] The page works at phone width and in light and dark themes

---

## Reference Files

| File | Read When |
|---|---|
| `references/blueprint-sections.md` | Building any tab: what goes in it, the tables and their columns, layout notes |
| `references/nfr-checklist.md` | Writing the non-functional requirements and the risks table |
| `references/contract-patterns.md` | Writing the architecture tab's contract, enum, action, Pest tests and MCP surface |

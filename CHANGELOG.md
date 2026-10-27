# Changelog

All notable changes to this project are documented in this file.
The format follows [Keep a Changelog](https://keepachangelog.com) and this project adheres to [Semantic Versioning](https://semver.org).

## [2.9.0] - 2026-10-27

### Added

- `docs-explainer-video` skill — turns a documentation page into a branded ~60s explainer video
  with music and SFX synced to the picture, rendered to 720p, 1080p or 4K MP4. Nine scene
  patterns (title, wizard form, single-path form, word-by-word sentence builder with in-place
  pickers, topology diagram with flowing packets, review with guards, pipeline, "it's live"
  browser scene, end card); real logo, colour variables and fonts read from the product's live
  site; every frame a pure function of time in headless Chromium; a seeded, licence-free music
  bed and SFX synthesised from the same timeline. The engine, render/audio scripts, starter
  brand and a complete G8Deck example ship as marked blocks in `references/toolkit-*.md` and
  unpack with one command, so the skill installs through the markdown-only manifest and works
  in assistants without a shell (they produce the project; the user renders locally)

## [2.8.0] - 2026-10-27

### Added

- `typescript-developer` agent — TypeScript end-to-end for Next.js App Router (Server Components
  by default, Server Actions re-validated as public endpoints, `NEXT_PUBLIC_*` hygiene), React +
  Vite (derived state over effects), Manifest V3 extensions (ephemeral service worker, least
  privilege, untrusted messages) and Node services; strict types with no `any`/`@ts-ignore`,
  Testing Library queries by role, Vitest/Playwright gate
- `go-developer` agent — idiomatic Go for workers, CLIs and HTTP services: `%w` wrapping with
  `errors.Is/As`, context-first APIs, owned goroutines with graceful shutdown, consumer-side
  interfaces, `log/slog`, server/client timeouts, table-driven tests; gofmt, vet, `-race`,
  golangci-lint and govulncheck gate
- `python-developer` agent — Python 3.12+ packaged CLIs and libraries: modern type hints,
  dataclasses, logging in libraries vs printing in the CLI, lazily-imported optional extras,
  `importlib.resources`, `yaml.safe_load`, no `shell=True`, Jinja2 autoescape; pytest
  (parametrize, `tmp_path`), ruff and mypy/pyright gate

### Changed

- Implementation phase in the SDLC coverage map now has one developer agent per runtime;
  ponytail wiring extended to the three new agents

## [2.7.0] - 2026-10-24

### Added

- `product-blueprint` skill — turns a BR document and prototype screenshots (a Drive or local
  folder) into one tabbed HTML blueprint: English requirements with phase, MoSCoW and prototype
  coverage, non-functional requirements the BR doc implies, architecture with state machines,
  the key contract and Pest tests, a phased roadmap with exit criteria, a sprint plan with risks,
  and mockups only for the screens the prototype lacks
- `product-brand-kit` skill — naming (code name vs public brand), a logo built from real font
  outlines, a contrast-checked brand kit, domain advice with a migration plan, a landing page
  with no invented claims, and an all-sizes export pack with circle-safe WhatsApp/social avatars.
  Also sources a licensed, art-directed photo set (Unsplash, Pexels, Pixabay, Openverse, Wikimedia,
  own shoots) with a credits file, and builds a marketing kit: message house in EN/BM, social post
  set, captions, carousel, video script, email, WhatsApp broadcast, blog, press release, ads, deck,
  and print pieces (flyer, poster, roll-up, bunting, booth, brochure, stickers, merch) with bleed,
  QR and UTM rules

## [2.6.0] - 2026-09-20

### Added

- `templates/browser-behavior.md` — a second managed block in `~/.claude/CLAUDE.md`
  (`claude-toolkit:browser`): anything that opens a browser uses the window as the user left
  it — their real screen size, full screen stays full screen. No `resize_window`, no viewport
  override, no mobile emulation, no shrinking the window for a screenshot. Overridden only by
  an explicit instruction in the user's own prompt, for that request only
- `--no-browser-rules` installer flag to skip that block

### Changed

- `install_output_format` generalised to `install_managed_block <name> <template> <label>` so
  each preference block is one template file plus one call. `--no-output-format` keeps its
  exact previous meaning — it gates the output-format block only

## [2.5.0] - 2026-09-13

### Added

- `debugging` and `security-hardening` skills
- `performance-tuning` and `ui-accessibility` skills
- `## Verification` checkbox gates on high-stakes skills
- Companions (graphify, ponytail) installed from upstream by `install.sh`, never vendored
- `templates/output-format.md` — TLDR + tables default written as a managed block in
  `~/.claude/CLAUDE.md`, with `/output <mode>` to switch per session

## [2.4.0] - 2026-08-28

### Added

- `schema-diagram` skill — generates a self-contained interactive ERD from a Laravel
  project's **live database**: one standalone HTML file with domain-coloured table cards,
  foreign-key edges anchored to the actual column rows, a per-table inspector showing both
  directions of every relation, and a committed JSON payload that makes schema drift
  reviewable as a diff rather than as a 1 MB generated file
- `/erd` command — `init`, `generate`, `build`, `refresh`, `check`, `domains`, `verify`
- `database-engineer` agent now loads `schema-diagram` for schema documentation work

### Notes

Distilled from a production ERD build, so the references carry the findings rather than only
the code. The failures worth naming, all of which render *successfully* and are wrong:

- **Read the database, never the migrations.** Migrations are the instructions; only the
  database is the result, and a mature project's column changes, index swaps and drops are
  exactly what a parser gets wrong — silently
- **Filter tables on `getCurrentSchemaName()`, not `getDatabaseName()`.** The two disagree on
  SQLite, where the schema is `main` and the database name is the file path: the wrong filter
  matches nothing, so the test suite passes against an empty schema instead of failing
- **Domain assignment is a hand-written map with a two-way coverage gate.** A name-prefix
  heuristic mis-files tables and gives no sign that it did. The command refuses to write and
  the Pest suite fails when a table is unaccounted for
- **Giving a React Flow node `width`/`height` makes it skip measurement**, so handle bounds
  never exist and every edge is dropped with no error at all — every card draws perfectly and
  all relations vanish. Use `initialWidth`/`initialHeight`
- **An unpainted Chrome tab throttles `ResizeObserver` and `rAF`**, producing exactly those
  symptoms on a healthy page. Screenshot first — that forces a frame — then trust the DOM

## [2.3.0] - 2026-08-13

### Added

- `project-status` skill — TLDR + table status report that cross-checks a project's planning
  tree against live GitHub issues **and the code**, on the rule that a planning document's
  claim is never status until the code confirms it
- `deploy-app` skill — deploy a Kickoff app via `bin/deploy` over SSH: verification checklist,
  a recovery path per failure mode, and the production traps (DB-backed settings overriding
  `.env`, host SMTP port blocks, unattended prompts exiting 0, `APP_KEY` rotation)
- `docs/02-development/06-laravel-boost-coexistence.md` — which skill names Laravel Boost owns
  and why a collision between the two is silent

### Changed

- **`pest-testing` renamed to `kickoff-pest-testing`** (migration entry included). Laravel
  Boost's `boost:install --skills` writes its own `pest-testing` into the same directory and
  had already overwritten the toolkit's copy in a live project, undetected. The two are
  complementary — Boost covers Pest syntax; this covers scaffolding, the arch-test baseline
  and suite performance
- `kickoff-pest-testing` — new sections on what a green suite does not prove (Alpine, Flux
  custom elements, deferred `wire:model`) and on suite performance (seeder placement in
  `$seeder` rather than `beforeEach`, `XDEBUG_MODE` pinning, Test Impact Analysis and pcov)
- `livewire-flux` — client-side gotchas that ship green: Flux renders `<ui-checkbox>` not a
  native input, `x-cloak` is inert without its CSS rule, a double quote anywhere in an Alpine
  attribute un-Alpines the subtree, and `description:trailing` for field help text
- `project-laravel` — `access-control.md` gains a tenancy section (a policy is not a scope;
  scoped finders; per-tenant uniqueness; pinning the guard on non-`web` surfaces);
  `database-conventions.md` gains engine-portability rules (MySQL leading-column FK indexes,
  non-transactional DDL, unreliable `hasIndex()`, the migrate/rollback/migrate round trip);
  `model-conventions.md` gains silent failure modes (null relations, Traitify's `user_id`
  auto-fill, seeder `unguarded()`, factory visibility branches)
- `code-quality` — Larastan blind spots: docblock placement above PHP attributes, casts
  declared via `casts()` being invisible, and baseline churn from generic relation annotations
- `project-conventions` — lessons belong in `CLAUDE.md` and tracking on GitHub, never in a
  local `tasks/todo.md` or `lessons.md`; the multi-repo workspace two-`CLAUDE.md` pattern; and
  the repository housekeeping Claude depends on (`/.claude/worktrees/` in `.gitignore`)
- `kickoff-patch` — records the Kickoff composer-script change (TIA no longer the default
  `test`), the new `build/php-ini` test tooling, `PASSKEYS_USER_HANDLE_SECRET` and the
  `/.claude/worktrees/` ignore rule; restores `laravel/doctor` in the package baseline

## [2.2.0] - 2026-08-03

### Added

- 4 agents completing the role coverage: `database-engineer` (DBA — schema, migrations, indexing, backups, tenant isolation), `project-manager` (roadmaps, milestones, sprints, risk/scope), `api-engineer` (REST design & build), `penetration-tester` (authorized offensive testing — local/staging only, scoped, minimal-impact) — roster is now 20

### Changed

- **Generalised the whole toolkit** — removed personal names, private product names (product suite/ticketing specifics) and machine-specific paths from all agents, skills, docs and manifests so the toolkit reads as a general-purpose Kickoff-based Laravel toolkit. The Kickoff baseline is kept (it is the toolkit's core value)
- Plugin/marketplace author set to CleaniqueCoders; personal email removed
- Docs (product spec, roadmap, using-agents coverage map) updated for the 20-agent roster

## [2.1.0] - 2026-08-03

### Added

- 3 agents completing full SDLC coverage: `software-architect` (design/ADRs), `laravel-developer` (implementation), `performance-engineer` (measure-first optimisation) — roster is now 16
- `docs/02-development/05-using-agents.md` — SDLC coverage map, delegation guide, typical agent chains
- `docs/04-api/02-agent-format.md` and `03-command-format.md` — format contracts for agents and commands

### Changed

- `qa-engineer` description now explicitly covers test strategy / QA planning alongside test writing (QC)
- Docs refreshed for the toolkit era: product spec 0.2, requirements 0.2 (FR-05/06/07 for agent/command/plugin formats, Bash 3.2 requirement), roadmap (2.0 shipped, 2.x planned), architecture overview, FAQ

## [2.0.0] - 2026-08-03

### Changed

- **BREAKING**: Repository renamed from `nasrulhazim/agent-skills` to `nasrulhazim/claude` — old URLs redirect, but update your clones: `git remote set-url origin https://github.com/nasrulhazim/claude.git`
- **BREAKING**: `manifest.txt` entries are now type-prefixed (`skills/…`, `agents/…`, `commands/…`); `install.sh` older than 2.0.0 cannot parse the new manifest (remote installs always fetch the latest installer — stale local clones must `git pull` first)
- `install.sh` rewritten as the Claude Toolkit Installer v2.0.0: installs skills, agents and commands to `~/.claude/{skills,agents,commands}`
- `migrations.txt` format extended with type-prefixed entries (`agents/old:agents/new`); legacy `old:new` skill entries still supported
- `generate-manifest.sh` now scans `skills/`, `agents/` and `commands/`

### Added

- `agents/` — 13 role agents: brand-designer, business-analyst, code-reviewer, courseware-developer, devops-engineer, fleet-auditor, package-maintainer, qa-engineer, security-analyst, support-analyst, tech-writer, ui-designer, upgrade-specialist
- `commands/` — 10 slash commands: analyze-repo, design-logo, docs, sales, sales-create-config, sales-get-marketing, sales-get-pricing, sales-get-quotation, upgrade-laravel, upgrade-livewire
- `.claude-plugin/` plugin + marketplace manifests — install via `/plugin marketplace add nasrulhazim/claude` then `/plugin install claude-toolkit@claude`
- `install.sh` flags: `--dry-run` and `--only <name>`
- This CHANGELOG

## [1.19.0] and earlier

See [GitHub releases](https://github.com/nasrulhazim/claude/releases) for the `agent-skills`-era history (1.0.0 – 1.19.0, skills only).

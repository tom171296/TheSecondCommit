<!--
Sync Impact Report
Version change: 1.0.0 → 1.1.0
Modified principles:
  - Principle 4 (Performance & Accessibility Budgets) unchanged semantically but quality gate enforcement text adjusted (automated aXe removed)
  - Principle 5 renamed to "Simplicity & Minimal Surface Area" (no change) plus new Principle 6 added
Added sections:
  - New Principle 6: Manual Verification & Lightweight Tooling
Removed sections:
  - Automated aXe scan requirement from Quality Gates
Templates requiring updates:
  .specify/templates/plan-template.md ✅ updated
  .specify/templates/spec-template.md ✅ no change required
  .specify/templates/tasks-template.md ✅ updated
Follow-up TODOs:
  - None (feature tasks file already migrated to manual verification)
-->

# TheSecondCommit Constitution

## Core Principles

### 1. Content & Authenticity First
All decisions prioritize delivering high‑quality podcast and written content with clarity and
authentic voice over visual gimmicks or engineering novelty. Design, automation, and
infrastructure MUST serve editorial cadence (publishing) and content permanence.

Rationale: Prevents over‑engineering; ensures effort spent increases audience value: readable,
trustworthy material and reliable podcast delivery.

### 2. Zero External Runtime Dependencies
The site MUST build and function using only: (a) a pinned Hugo (extended) version, (b) self‑authored
templates, CSS, minimal JS (only if strictly required), and (c) first‑party media assets committed
to the repository. No CDN frameworks (Bootstrap, Tailwind, jQuery), no external fonts, analytics,
or script tags. Any exception REQUIRES pre‑merge written justification referencing performance,
accessibility, or legal compliance needs.

Rationale: Guarantees reproducible, auditable builds; eliminates supply‑chain risk; improves
privacy and performance.

### 3. Reproducible & Deterministic Builds
Builds MUST be fully reproducible: same commit + documented Hugo version ⇒ identical `public/`
output (excluding timestamps in feeds). Hugo version is pinned in documentation and CI. A build
script (or documented command) MUST fail if Hugo version drifts. No manual post‑build edits.

Rationale: Enables confident rollbacks, caching, and verifiable integrity of published artifacts.

### 4. Performance & Accessibility Budgets
Each published page (article/post detail + listing + landing) MUST meet:
- Lighthouse (mobile) performance ≥ 90, accessibility ≥ 100
- Core Web Vitals targets: LCP < 2.5s, CLS < 0.1, TBT < 300ms on reference mid‑tier mobile (emulated)
- Initial critical payload (HTML + critical CSS + inline JS) ≤ 250KB (excluding media & podcast audio)
- No blocking external resources; fonts self‑hosted or system stack
- All images optimized (appropriately sized + compressed); `loading=lazy` where non‑critical
- Semantic HTML5 landmarks; ARIA only when necessary
- All interactive elements keyboard reachable; focus states visible

Rationale: Fast, accessible content raises retention, SEO, and broad audience inclusion.

### 6. Manual Verification & Lightweight Tooling
The project intentionally avoids a heavy automated test harness. Quality is ensured via:
1. Deterministic build diffing (script-based) for reproducibility.
2. Manual review checklists for accessibility (keyboard navigation, landmarks, alt text completeness).
3. Occasional ad‑hoc Lighthouse or browser devtools audits (documented but not enforced by CI gates).
4. Minimal scripts ONLY where they provide high signal per maintenance cost (front matter validation, alt text scan, deterministic diff).
5. No expansive unit/integration test suites unless a regression pattern emerges (≥3 similar production issues).

Rationale: Preserves velocity and low maintenance overhead while still catching high‑impact regressions through deterministic artifacts and targeted scripts.

### 5. Simplicity & Minimal Surface Area
Prefer plain Hugo layouts + modular partials over custom build tooling. No JS added unless it:
1) Enables podcast playback enhancements, 2) Provides progressive enhancement for otherwise
accessible content, or 3) Implements required privacy‑respecting analytics approved in writing.
Custom JS (when allowed) MUST: be framework‑free, < 5KB minified, defer execution, and not block
render. Abstract only after a demonstrated maintenance burden (≥3 concrete repetitions).

Rationale: Reduces long‑term maintenance, cognitive load, and defect risk while enabling lean iteration.

## Platform Constraints & Standards

Stack: Hugo (extended) pinned version declared in README & CI. Theme: bespoke, stored under
`/themes/secondcommit/`. Assets: Plain CSS (no preprocessors unless justified), optional minimal
post‑processing (e.g., Hugo Pipes) allowed if deterministic and in‑repo. No third‑party tracking.

Content Model: Markdown (posts, episodes) with front matter fields: `title`, `date`, `description`,
`slug`, `tags`, `draft`, plus podcast episode fields: `audio`, `duration`, `episode_number`.

Media: Audio encoded to (a) MP3 (baseline), (b) optional Opus. Images: WebP preferred, fallback PNG/JPEG
when needed. All media committed or sourced from first‑party storage path; no hotlinked images.

Security & Privacy: No injected external scripts. If analytics added, MUST be self‑hosted privacy‑respecting
solution with documented data fields collected.

Backups: The `public/` build output of each release commit archived (artifact) ensuring rollback and audit.

Licensing & Attribution: All third‑party content (if any) explicitly licensed and documented in a
`LICENSES.md` table. Absence of file implies no third‑party bundled code.

## Development Workflow & Quality Gates

Branching: `main` is always deployable; feature branches use `feat/<slug>`; content‑only updates may use
`content/<date>-<slug>`.

Pull Requests MUST include a "Constitution Check" section listing compliance statements for Principles 2–5.

Mandatory Pre‑Merge Gates:
1. Build Reproducibility: Build (locally or CI) may be executed twice; if deterministic script reports diff (excluding feed timestamps) → block.
2. Performance: A manual Lighthouse (mobile) spot check for new layout types; maintain internal log of last measured scores (not enforced automatically).
3. Accessibility: Maintainer executes keyboard navigation + screen reader landmark inspection; alt text script MUST pass.
4. Dependency Audit: Script verifies absence of external CSS/JS/font/network calls not in allowlist.
5. Content Review: Metadata completeness (title, description, audio alt text if applicable).

Failure of any gate blocks merge unless an explicit, time‑boxed waiver issue is linked (Patch bump at most).

Release Process: Merge to `main` triggers build + artifact archival + optional deployment.

Incident Response: Any post‑merge performance or accessibility regression triggers hotfix branch `fix/<issue>`.

## Governance

Authority: This Constitution supersedes ad hoc practices. Conflicts resolved in favor of stricter interpretation.

Amendments:
1. Open issue describing proposed change + rationale + expected version bump classification.
2. Submit PR modifying this file + updating impacted templates (plan, tasks, spec if needed).
3. Require ≥1 reviewer approval (not the author) verifying classification.
4. Merge PR → CI auto‑tags version in commit message (manual tag optional).

Versioning Policy (Semantic Governance Versioning):
- MAJOR (X.0.0): Backward incompatible removals or redefinitions of Principles / Governance.
- MINOR (0.Y.0): Add or materially expand a Principle or mandatory gate.
- PATCH (0.0.Z): Clarifications, wording, non‑normative examples.

Compliance Review Expectations:
- Every PR template includes a Constitution Checklist referencing Principles 2–6 explicitly.
- Quarterly (or every 10 merged PRs) run an audit: reproduce build, run a manual Lighthouse sample, confirm no new externals.
- Document deviations + remediation plan inside an `AUDIT.md` log.

Non‑Compliance Handling:
- Undocumented external dependency OR performance regression beyond budgets: immediate hotfix branch.
- Repeated (≥2 consecutive) accessibility regressions escalate to MINOR version governance review.

**Version**: 1.1.0 | **Ratified**: 2025-09-25 | **Last Amended**: 2025-09-25
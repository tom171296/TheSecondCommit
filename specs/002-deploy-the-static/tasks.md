# Tasks: Deploy Static Site via GitHub Pages Action

**Input**: Design documents from `specs/002-deploy-the-static/`
**Prerequisites**: plan.md, research.md, data-model.md, contracts/workflow-contract.md, quickstart.md

## Phase 3.1: Setup
- [X] T001 Create `CNAME` file at repo root with custom domain if not present (added `CNAME`)
- [X] T002 Add Hugo version pin (README + optional `HUGO_VERSION` file) if missing (added `HUGO_VERSION` + README update)
- [X] T005 Record concurrency group decision (documented in research; to embed in workflow header)

## Phase 3.2: (Removed)
User opted for NO automated validation scripts. Former tasks (CNAME check script, wrapper scripts) removed. Manual verification will occur outside workflow prior to push (see Polish tasks).

## Phase 3.3: Core Workflow Implementation (Minimal)
- [X] T010 Create GitHub Actions workflow `.github/workflows/deploy.yml` (triggers: push to main, workflow_dispatch)
- [X] T011 Add steps: checkout, set up Hugo (pinned version), build site, ensure `CNAME` included, deploy to Pages
- [X] T012 Add concurrency group (cancel in progress) to workflow YAML
- [X] T013 Add simple echo summary (commit SHA + build duration) – no automated validation

## Phase 3.4: Integration / Enhancements (Optional / Lightweight)
- [ ] T014 (Optional) Add Hugo resources cache step (key: hugoVersion + config hash)
- [ ] T015 (Optional) Add build size info to summary echo
- [ ] T016 Add retention note to README (latest only; rollback = rebuild prior commit)
- [ ] T017 Add manual deterministic check instructions section (two local builds) in README (documentation only, no script)

## Phase 3.5: Polish
- [ ] T018 Add `docs/deployment.md` (workflow explanation, manual verification checklist: front matter spot check, alt text spot check, optional double build instructions)
- [ ] T019 Add link/note in README or PR template (if created later) about manual deployment checklist
- [ ] T020 Add commented placeholder in workflow for future release-only trigger section
- [ ] T021 Perform manual verification after first live deploy (domain resolves, pages load, no drafts visible) and record in `docs/deployment.md`

## Dependencies
- Setup (T001–T005) precedes workflow (T010–T013)
- Enhancements (T014–T017) after core workflow
- Polish (T018–T021) last

## Parallel Execution Examples
```
# Independent in Setup:
T003 T004 can proceed after T001–T002

# Integration phase parallel candidates:
T006 T007 T008 (distinct scripts) while T009 docs update
```

## Notes
- User opted out of automated validation scripts; all checks are manual pre-push.
- Concurrency group prevents race deployments.
- Keep workflow minimal: avoid external marketplace dependencies unless essential.
- Optional deterministic check documented, not enforced in CI.

## Validation Checklist (Manual Focus)
- [ ] Workflow deploys on push to main
- [ ] CNAME included in published artifact
- [ ] Concurrency cancellation configured
- [ ] README documents manual deterministic check
- [ ] Deployment doc created with manual checklist
- [ ] No external runtime dependencies added

# Tasks: Blog Post Page & Markdown Content Pipeline

**Input**: Design documents from `/specs/001-blog-post-page/`  
**Prerequisites**: plan.md, research.md, data-model.md, contracts/

## Phase 3.1: Setup
- [X] T001 Create `tests/` directory structure: `tests/validation`, `tests/render`, `tests/accessibility`, `tests/perf`
- [X] T002 Add PowerShell script `scripts/validate-front-matter.ps1` (validate required fields, slug uniqueness, description length, lastmod >= date)
- [X] T003 [P] Add PowerShell script `scripts/deterministic-build.ps1` (run hugo twice w/ BUILD_DATE; diff `public/` excluding RSS timestamps)
- [X] T004 [P] Add PowerShell script `scripts/check-alt-text.ps1` (fail on images without alt or intentional empty alt)
- [X] T005 Add PowerShell script `scripts/list-scheduled.ps1` (output scheduled posts summary)

## Phase 3.2: Manual Verification & Script Setup (Complete before 3.3)
- [ ] T006 Create `manual-checks/accessibility-checklist.md` (landmarks, headings sequence, keyboard traversal, focus visible, alt text policy)
- [ ] T007 Create `manual-checks/performance-spot-check.md` (fields: date, measured mobile Lighthouse scores, largest HTML + CSS payload bytes)
- [ ] T008 Create `manual-checks/release-verification.md` template (includes deterministic diff confirmation, metadata completeness, alt scan pass)
- [ ] T009 [P] Run `scripts/validate-front-matter.ps1` against existing content and record results in release checklist
- [ ] T010 [P] Run `scripts/check-alt-text.ps1` and record results in accessibility checklist
- [ ] T011 [P] Execute double build with `scripts/deterministic-build.ps1` and attach summary to release verification doc
- [ ] T012 Prune obsolete automated test scaffolding (`tests/` directory) once manual docs exist

## Phase 3.3: Core Implementation (after manual docs & scripts prepared)
- [ ] T013 [P] Implement RSS template `themes/secondcommit/layouts/_default/rss.xml` per contract
- [ ] T014 Refine `single.html`: add structured data JSON-LD placeholder and ensure scheduled badge + robots logic consistent
- [ ] T015 Refine `list.html`: ensure pagination variable captures only eligible posts and includes accessible landmark roles
- [ ] T016 Enhance `baseof.html`: inline critical CSS option + skip link final semantics
- [ ] T017 Expand `critical.css` with code block high-contrast styles while keeping payload under budget (FAIL test until done)
- [ ] T018 Add deterministic build ignore list for diff (exclude known timestamp lines in feed)
- [ ] T019 Implement validation integration script `scripts/run-all-validations.ps1` orchestrating front matter, alt text, scheduled listing

## Phase 3.4: Integration / Enhancements
- [ ] T020 Image optimization pipeline (Hugo Pipes) for resizing + WebP (dry run; add tasks to future features if size > thresholds)
- [ ] T021 Implement performance budget script `scripts/check-performance.ps1` (HTML+CSS+inline JS <= 250KB) failing initially
- [ ] T022 Implement accessibility CLI integration (aXe or pa11y) placeholder script `scripts/check-accessibility.ps1` (FAIL until tool added)
- [ ] T023 Add double-build diff CI workflow file `.github/workflows/deterministic.yml` (build twice, diff)
- [ ] T024 Add parallel validation CI workflow `.github/workflows/validate.yml` (front matter, alt text, scheduled listing)

## Phase 3.5: Polish
- [ ] T025 [P] Remove CSS duplication & create `themes/secondcommit/assets/css/_tokens.css` if repetition > 3 occurrences
- [ ] T026 Reduce DOM depth in `single.html` & `list.html` (audit semantics, remove unnecessary wrappers)
- [ ] T027 [P] Update `quickstart.md` with validation scripts & deterministic build instructions
- [ ] T028 Optimize RSS feed generation (confirm stable ordering, remove extra whitespace)
- [ ] T029 [P] Add documentation `docs/content-guidelines.md` (tone, alt text style, description rules)
- [ ] T030 Manual a11y + Lighthouse pass & record results in `docs/validation-report.md`

## Dependencies
- T001–T005 before manual verification prep
- Manual artifacts (T006–T012) before T013–T019
- RSS template (T013) early in core phase before RSS optimization (T028)
- Integration tasks (T020–T024) after core tasks
- Polish (T025–T030) last

## Parallel Execution Examples
```
# Validation scripts (independent):
T003, T004 can run together after T001–T002.

# Manual prep tasks parallel candidates (independent):
T009 T010 T011 can run together after docs (T006–T008)

# Core template refinements (independent files):
T013 T014 T015 T017 can start after tests fail.
```

## Notes
- All validation scripts must exit non-zero on failure.
- Snapshot tests can store golden HTML outputs under `tests/render/__snapshots__/`.
- Deterministic diff should ignore lines matching regex for RFC 1123 timestamps.
- Performance task intentionally fails until CSS optimization completed.

## Validation Checklist
- [ ] Manual checklists created (accessibility, performance, release)
- [ ] Deterministic build script executed & logged
- [ ] Alt text & front matter scans logged
- [ ] Payload size measured and recorded
- [ ] Parallel tasks only touch distinct files
- [ ] No implementation task precedes manual verification artifacts

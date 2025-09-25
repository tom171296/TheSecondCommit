# Tasks: [FEATURE NAME]

**Input**: Design documents from `/specs/[###-feature-name]/`
**Prerequisites**: plan.md (required), research.md, data-model.md, contracts/

## Execution Flow (main)
```
1. Load plan.md from feature directory
   → If not found: ERROR "No implementation plan found"
   → Extract: tech stack, libraries, structure
2. Load optional design documents:
   → data-model.md: Extract entities → model tasks
   → contracts/: Each file → contract test task
   → research.md: Extract decisions → setup tasks
3. Generate tasks by category:
   → Setup: project init, dependencies, linting
   → Tests: contract tests, integration tests
   → Core: models, services, CLI commands
   → Integration: DB, middleware, logging
   → Polish: unit tests, performance, docs
4. Apply task rules:
   → Different files = mark [P] for parallel
   → Same file = sequential (no [P])
   → Tests before implementation (TDD)
5. Number tasks sequentially (T001, T002...)
6. Generate dependency graph
7. Create parallel execution examples
8. Validate task completeness:
   → All contracts have tests?
   → All entities have models?
   → All endpoints implemented?
9. Return: SUCCESS (tasks ready for execution)
```

## Format: `[ID] [P?] Description`
- **[P]**: Can run in parallel (different files, no dependencies)
- Include exact file paths in descriptions

## Path Conventions
- **Single project**: `src/`, `tests/` at repository root
- **Web app**: `backend/src/`, `frontend/src/`
- **Mobile**: `api/src/`, `ios/src/` or `android/src/`
- Paths shown below assume single project - adjust based on plan.md structure

## Phase 3.1: Setup
- [ ] T001 Create project structure per implementation plan
- [ ] T002 Initialize [language] project with [framework] dependencies
- [ ] T003 [P] Configure linting and formatting tools

## Phase 3.2: Manual Verification & Scripted Checks
**CRITICAL: Lightweight scripts and manual checklist prepared before broad implementation**
- [ ] T004 [P] Add deterministic build script (double build + diff) if absent
- [ ] T005 [P] Add front matter validation / metadata completeness script
- [ ] T006 [P] Add alt text scan script
- [ ] T007 Prepare manual review checklist doc (performance spot check, accessibility keyboard + landmarks, content integrity)

## Phase 3.3: Core Implementation (after scripts & checklist ready)
- [ ] T008 [P] Hugo base layout in themes/secondcommit/layouts/_default/baseof.html
- [ ] T009 [P] Post single template in themes/secondcommit/layouts/_default/single.html
- [ ] T010 [P] List (blog index) template in themes/secondcommit/layouts/_default/list.html
- [ ] T011 Header partial in themes/secondcommit/layouts/partials/header.html
- [ ] T012 Footer partial in themes/secondcommit/layouts/partials/footer.html
- [ ] T013 Critical CSS in themes/secondcommit/assets/css/critical.css
- [ ] T014 Progressive enhancement JS (if needed) in themes/secondcommit/assets/js/site.js

## Phase 3.4: Integration / Enhancements
- [ ] T015 Image optimization (Hugo Pipes) config
- [ ] T016 RSS / Podcast feed template in themes/secondcommit/layouts/_default/rss.xml
- [ ] T017 Accessibility keyboard navigation validation script improvements
- [ ] T018 Performance budget CI integration

## Phase 3.5: Polish
- [ ] T019 [P] Remove template duplication / extract partials
- [ ] T020 Verify Lighthouse scores meet budgets (mobile ≥90 perf, a11y 100)
- [ ] T021 [P] Update docs/content-guidelines.md
- [ ] T022 Remove unused CSS/JS (size budget check)
- [ ] T023 Manual accessibility keyboard & screen reader smoke test

## Dependencies
- Scripts & checklist (T004-T007) before implementation (T008-T014)
- T008 blocks T009, T015
- T016 blocks T018
- Implementation before polish (T019-T023)

## Parallel Example
```
# Launch T004-T007 together:
Task: "Contract test POST /api/users in tests/contract/test_users_post.py"
Task: "Contract test GET /api/users/{id} in tests/contract/test_users_get.py"
Task: "Integration test registration in tests/integration/test_registration.py"
Task: "Integration test auth in tests/integration/test_auth.py"
```

## Notes
- [P] tasks = different files, no dependencies
- Verify tests fail before implementing
- Commit after each task
- Avoid: vague tasks, same file conflicts

## Task Generation Rules
*Applied during main() execution*

1. **From Contracts**:
   - Each contract file → contract test task [P]
   - Each endpoint → implementation task
   
2. **From Data Model**:
   - Each entity → model creation task [P]
   - Relationships → service layer tasks
   
3. **From User Stories**:
   - Each story → integration test [P]
   - Quickstart scenarios → validation tasks

4. **Ordering**:
   - Setup → Tests → Models → Services → Endpoints → Polish
   - Dependencies block parallel execution

## Validation Checklist
*GATE: Checked by main() before returning*

- [ ] All critical templates have render tests
- [ ] Scripts/checklist prepared before implementation
- [ ] Accessibility alt text scan integrated
- [ ] Parallel tasks truly independent
- [ ] Each task specifies exact file path
- [ ] No task modifies same file as another [P] task
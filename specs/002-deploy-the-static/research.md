# Research: Deploy Static Site via GitHub Pages Action

## Decisions & Rationale

### Deployment Trigger
- Decision: Deploy on every push to `main`.
- Rationale: Ensures rapid content publishing; low risk due to static deterministic build.
- Alternatives: Tagged releases only (slower content flow), hybrid preview+release (unnecessary complexity now).

### Hosting Target
- Decision: Use GitHub Pages (pages deployment branch or actions artifact flow as supported).
- Rationale: Native integration, zero additional infra, aligns with zero external runtime dependency.
- Alternatives: Netlify / Cloudflare Pages (introduces external platform dependency conflicting with Principle 2 runtime minimalism).

### Deterministic Build Verification
- Decision: Run double build (`hugo` twice) prior to publish; diff excluding timestamp lines (feed pubDate/lastBuildDate).
- Rationale: Enforces Principle 3 reproducibility before exposing site.
- Alternatives: Skip second build (risk undetected nondeterminism), artifact hashing (extra complexity not needed yet).

### Validation Scripts Integration
- Decision: Reuse existing PowerShell scripts (front matter, alt text, deterministic) in workflow steps.
- Rationale: Single source of truth; manual + scripted lightweight checks per Principle 6.
- Alternatives: Re-implement logic in YAML bash steps (duplication risk).

### Custom Domain Management
- Decision: Commit `CNAME` file (domain TBD already chosen outside scope) each deploy from repo root into publish artifact.
- Rationale: Deterministic and versioned; prevents forgetting domain on redeploy.
- Alternatives: Manual Pages settings only (risk of drift), separate action (unnecessary abstraction).

### Retention Strategy
- Decision: Keep only latest deployment (atomic overwrite).
- Rationale: Simplifies rollback story (rebuild prior commit manually if needed), reduces branch clutter.
- Alternatives: Keep N builds (complexity), tag releases separated (can add later).

### Concurrency Handling
- Decision: Enable GitHub Actions `concurrency` group with `cancel-in-progress: true` for publish workflow.
- Rationale: Ensures only final push is deployed if rapid successive pushes happen.
- Alternatives: Serial queue (slower content availability).

### Caching Strategy
- Decision: Optional: Cache Hugo build resources (`/resources/_gen`) keyed by Hugo version + commit hash; low priority.
- Rationale: Build acceleration; safe because deterministic.
- Alternatives: No caching (slightly slower), aggressive multi-layer caching (overkill).

### Security / Least Privilege
- Decision: Use built-in GITHUB_TOKEN with minimally required Pages / contents permissions.
- Rationale: Avoid extra secrets; principle of least privilege.
- Alternatives: PAT secret (unnecessary exposure).

### Failure Visibility
- Decision: Provide concise summary step that echoes: commit SHA, diff status (pass/fail), counts of posts published, site URL.
- Rationale: Fast diagnosis; manual verification workflow synergy.
- Alternatives: Artifact logs only (slower to inspect).

## Open (Deferred) Items
- Domain value (actual string) stored/decided elsewhere—assumed present in committed `CNAME`.
- Build time budget not formalized (<2m soft target) — acceptable per low complexity.
- Future extension for release-only deployments (FR-014) deferred until workflow churn justifies branching.

## Rejected Alternatives Summary
| Topic | Alternative | Reason Rejected |
|-------|------------|-----------------|
| Trigger | Tagged releases only | Slows content iteration |
| Hosting | External static hosts | Adds external dependency risk |
| Determinism | Single build only | Could miss nondeterministic outputs |
| Domain | Manual Pages config only | Risk of drift / forgetting |
| Retention | Keep N builds | Adds management complexity with little value |
| Caching | Aggressive multi-layer | Over-optimization early stage |

## Summary
Research confirms minimal GitHub Actions workflow integrating existing scripts suffices; no additional tooling or services required at this phase.

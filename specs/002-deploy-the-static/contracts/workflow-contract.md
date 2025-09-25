# Workflow Contract: Deploy Static Site to GitHub Pages

## Overview
Defines required steps, invariants, and validation signals for the deployment workflow.

## Steps (Conceptual)
1. Checkout repository @ commit SHA.
2. Determine pinned Hugo version (from docs/README or config) and install.
3. Run validation scripts:
   - `scripts/validate-front-matter.ps1`
   - `scripts/check-alt-text.ps1`
4. Perform deterministic build check:
   - Build #1 → `public/`
   - Clean / second build #2 → `public_tmp/`
   - Diff excluding timestamp lines; if differences → FAIL.
5. Move canonical build output (from first build) to publish staging directory.
6. Ensure `CNAME` file present (exact custom domain value) in root of staging.
7. Count posts (non-draft, <= now date) → summary.
8. Publish to GitHub Pages (atomic overwrite of previous deployment).
9. Emit summary (commit SHA, post count, deterministic status, site URL).

## Inputs
| Name | Source | Required | Notes |
|------|--------|----------|-------|
| repo | Git | Yes | Current branch `main` push |
| pinnedHugoVersion | Documentation | Yes | Must match workflow install version |
| scripts | Repository `scripts/` | Yes | Validation & diff logic |
| CNAME value | Repo root `CNAME` file | Yes | Custom domain DNS already configured |

## Outputs
| Name | Type | Description |
|------|------|-------------|
| siteUrl | string | Public base URL |
| summaryLine | string | Compact status e.g., `DEPLOY ok sha=<7> posts=12 diff=pass` |
| deterministicStatus | enum | `pass` or `fail` |
| postCount | integer | Published post count |

## Invariants
- If deterministic diff fails, no publish occurs.
- Draft & future-dated posts excluded from postCount.
- CNAME file present in final artifact root.
- No external network fetch for runtime assets during build.

## Failure Conditions
| Condition | Handling |
|-----------|----------|
| Missing pinned Hugo version | Fail fast with clear log |
| Validation script error | Abort workflow, mark failed |
| Deterministic diff mismatch | Abort publish, mark failed |
| Missing CNAME file | Abort workflow |
| Publish API error | Mark failed; previous site remains |

## Logging Requirements
- Must log: commit SHA, hugo version, counts (posts, files), diff status, elapsed build time.

## Open Extensions (Not In Scope Now)
- Release-only publish mode.
- Artifact hashing manifest for integrity caching.
- Historical run archive branch.

# Data Model: Deploy Static Site via GitHub Pages Action

## Entities

### DeploymentRun
| Field | Type | Description | Constraints |
|-------|------|-------------|-------------|
| commitSha | string | Git commit deployed | Required, 40 hex chars |
| startedAt | iso-datetime | Workflow start timestamp | Required |
| finishedAt | iso-datetime | Workflow end timestamp | Required |
| success | boolean | Whether deployment completed fully | Required |
| siteUrl | string | Public base URL (custom domain) | Required, valid URL |
| postCount | integer | Number of published posts (non-draft, non-future) | >=0 |
| deterministicStatus | enum(pass,fail) | Result of double-build diff | Required |
| validationSummary | string | Aggregated script results line | <= 500 chars |

### SiteArtifact
| Field | Type | Description | Constraints |
| sizeBytes | integer | Total bytes of published artifact | >=0 |
| fileCount | integer | Total files published | >=0 |
| cname | string | Custom domain value from CNAME | Non-empty |
| hugoVersion | string | Pinned Hugo version used for build | Matches pinned version |
| buildDurationSec | number | Elapsed seconds for build step | >=0 |

## Relationships
- One `DeploymentRun` references exactly one `SiteArtifact` (1:1) for current/latest publish.

## Derived / Computed Data
- `postCount` derived by counting content pages excluding drafts & future dates.
- `deterministicStatus` derived from diff script exit code.
- `validationSummary` concatenates short status tokens (e.g., `FM=OK ALT=OK DIFF=OK`).

## State Transitions
1. INITIATED (metadata captured: commitSha, startedAt)
2. BUILDING (Hugo build in progress)
3. VALIDATING (scripts run, double build diff)
4. PUBLISHING (upload to Pages)
5. COMPLETE (success=true) or FAILED (success=false)

## Integrity Rules
- A FAILED run must not update live site (atomic overwrite policy).
- Only the most recent successful run is “current”.

## Notes
- These entities are conceptual for logging/summary; persistent storage not implemented now—captured in workflow logs and optional summary artifact.

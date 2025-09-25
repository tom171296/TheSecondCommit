# Data Model (Phase 1)

## Entities
### BlogPost
| Field | Type | Required | Notes |
|-------|------|----------|-------|
| title | string | yes | Used for H1, meta title |
| date | date (ISO) | yes | Publish date; drives scheduling logic |
| description | string | yes | Meta description & RSS description |
| slug | string | yes (unique) | URL segment; uniqueness validated |
| draft | boolean | yes | If true, excluded from build outputs |
| lastmod | date (ISO) | optional | If present, surfaced in metadata & RSS `<atom:updated>` |
| tags | list[string] | optional | Future taxonomy usage |
| body | markdown | yes | Rendered to HTML |
| scheduled | derived bool | yes (computed) | `date > build_date` |

### RSSItem (Derived)
| Field | Source | Notes |
|-------|--------|-------|
| title | BlogPost.title | |
| link | slug | `/blog/<slug>/` |
| guid | slug | Stable identifier |
| pubDate | BlogPost.date | RFC 1123 formatting |
| description | BlogPost.description | |
| atom:updated | BlogPost.lastmod (if present) | Extension element |

## Relationships
- BlogPost: independent collection; ordering by date desc.
- RSSItem: 1:1 with eligible BlogPost (published & not scheduled)

## Validation Rules
1. Required front matter: `title`, `date`, `description`, `slug`, `draft`.
2. Slug uniqueness across all blog posts (case-insensitive).
3. Alt text required for all images (validation occurs post-render or by parsing markdown).
4. Critical payload size test aggregates HTML + CSS + inline JS.
5. Scheduled determination: `scheduled = date > build_date` (build_date = current date or injected deterministic date).

## State Transitions
| From | To | Trigger |
|------|----|---------|
| Scheduled | Published | `build_date >= date` |
| Draft | Published | `draft=false` and build executed |

## Derived Views
- Blog Index Page: paginated projection (fields: slug, title, date, description, scheduled flag filtered out if true, draft filtered out).
- RSS Feed: derived collection of 50 latest published BlogPosts.

## Assumptions
- Build date defaults to system date when not provided.
- No partial publication concept (binary published or not).
- Tags not used for filtering yet (future enhancement).

## Open Items
- Missing media asset policy (warn vs fail) pending decision.

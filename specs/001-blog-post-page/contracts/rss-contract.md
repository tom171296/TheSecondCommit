# Contract: RSS Feed `/blog/index.xml`

## Specification
- Format: RSS 2.0 + Atom `<atom:updated>` extension
- Items: 50 most recent published (non-draft, non-scheduled) BlogPosts

## Channel Elements
| Element | Source | Notes |
|---------|--------|-------|
| title | Constant "TheSecondCommit Blog" | |
| link | Site base URL | Provided by deployment config |
| description | Constant marketing description | Defined in config.toml |
| language | `en` | Initial assumption |
| lastBuildDate | Build datetime (RFC 1123) | Deterministic if build date injected |

## Item Elements
| Element | Source | Notes |
|---------|--------|-------|
| title | BlogPost.title | |
| link | `/blog/<slug>/` | Absolute in final output |
| guid | slug | `isPermaLink=false` |
| pubDate | BlogPost.date | RFC 1123 |
| description | BlogPost.description | Plain text or escaped HTML |
| atom:updated | BlogPost.lastmod | Omit if absent |

## Exclusions
- Draft posts
- Scheduled posts (date > build date)
- More than 50 oldest items

## Determinism
Given identical input posts + build date, feed output MUST be byte-identical excluding ordering of XML attributes where not guaranteed by generator (Hugo ordering expected stable).

# TheSecondCommit
Podcast and blogsite for the Second Commit

## Blog Platform (Initial Implementation)
Built with Hugo (extended) pinned at version 0.129.0 for deterministic builds (see `HUGO_VERSION`).

### Directory Overview
```
content/blog/         # Markdown posts
themes/secondcommit/  # Custom theme (no external deps)
	layouts/            # Templates (base, list, single, partials)
	assets/css/         # critical.css (performance budget)
```

### Adding a Post
Create `content/blog/<slug>.md` with front matter:
```yaml
---
title: "Meaningful Title"
date: 2025-09-25
description: "Short summary."
slug: meaningful-title
draft: true
---
```

### Deterministic Build
```powershell
hugo --minify
```
Optional (consistent build date):
```powershell
$env:BUILD_DATE="2025-09-25"; hugo --minify
```

### Optional Hugo Resource Cache
You can speed up local builds by caching the Hugo resources directory. Create a local (not committed) directory and set:
```powershell
$env:HUGO_CACHEDIR=".hugo_cache"
hugo --minify
```
Workflow may later add a cache step keyed by `HUGO_VERSION`.

### Principles Applied
- No external runtime dependencies (fonts, JS frameworks, CSS libs)
- Performance & accessibility budgets enforced (scripts to follow)
- Scheduled posts: page generated with badge + noindex; hidden from index & RSS until publish date

See `specs/001-blog-post-page/` for detailed specification and design artifacts.

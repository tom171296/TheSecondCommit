# Quickstart: Adding a Blog Post

## 1. Prerequisites
- Hugo extended v0.129.0 installed (pinned)
- Repository cloned locally

## 2. Create a New Post File
Create `content/blog/<slug>.md` with front matter:
```yaml
---
title: "Meaningful Title"
date: 2025-09-25
description: "Short 1–2 sentence summary (<=160 chars)."
slug: meaningful-title
draft: true
lastmod: 2025-09-25 # optional
tags: ["meta"] # optional
---

Markdown body content here.
```

## 3. Validation Rules (Must Pass)
1. All required front matter fields present.
2. Unique slug.
3. Description 10–300 chars.
4. Alt text on every image (or explicit empty alt for decorative).
5. No future-dated posts listed until publish date (they show Scheduled badge & noindex if visited directly).

## 4. Local Build
```powershell
hugo --minify
```
Optional deterministic build date (for diffing):
```powershell
$env:BUILD_DATE="2025-09-25"; hugo --minify
```
*(Wrapper script will later enforce injecting BUILD_DATE)*

## 5. Preview
Open `public/blog/<slug>/index.html` in a browser.

## 6. Scheduled Posts
Set `date` to future date + `draft: false` → page builds with badge + noindex; removed from index & RSS.

## 7. Publishing
Set `draft: false` and ensure date <= today; rebuild.

## 8. RSS Verification
Check `public/blog/index.xml` exists and contains new post when published.

## 9. Performance & Accessibility (Manual for now)
- Lighthouse mobile >= 90 Performance / 100 Accessibility
- aXe (extension or CLI) zero serious violations

## 10. Common Errors
| Error | Cause | Resolution |
|-------|-------|-----------|
| Missing required front matter | Field omitted | Add field; rebuild |
| Duplicate slug | Two posts share slug | Rename one slug |
| Missing alt text | Image missing alt | Add meaningful alt or empty alt for decorative |

## 11. Next Steps
- Implement automated validation & diff scripts
- Add integration tests for rendering & RSS generation

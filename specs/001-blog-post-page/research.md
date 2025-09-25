# Research (Phase 0)

## Decisions
1. **Pinned Hugo Version**: 0.129.0 (extended) – ensures deterministic asset pipeline.
2. **Theme Location**: `themes/secondcommit/` custom only; no submodules.
3. **Scheduled Posts**: Generated with badge + `noindex`, excluded from index & RSS until publish date.
4. **Required Front Matter**: `title`, `date`, `description`, `slug`, `draft`; build fails if any missing.
5. **Accessibility Enforcement**: Alt text mandatory; failing images abort build; skip link included.
6. **RSS Feed**: Standard RSS 2.0 at `/blog/index.xml`, 50 latest published posts.
7. **Deterministic Build**: Optional `BUILD_DATE` environment variable or parameter to normalize date context when needed.
8. **Critical CSS Strategy**: Single `critical.css` inline or linked minimal; no external fonts.
9. **Code Block Styling**: Plain CSS token styling (no JS libs) with AA contrast.
10. **Performance Budget**: Critical payload ≤ 250KB; measure via script summing HTML + CSS + inline JS bytes.

## Rationale
- Determinism reduces drift and enables artifact diffing between builds.
- Excluding external dependencies improves privacy and reduces supply chain risk.
- Strict validation (alt/description) ensures accessibility and SEO from day one.
- RSS early inclusion supports subscriber growth and content discovery.

## Alternatives Considered
| Topic | Alternative | Rejected Because |
|-------|-------------|------------------|
| Theme | Import existing Hugo theme | Violates zero external dependency principle |
| Syntax Highlight | highlight.js / Prism | External JS/CSS + payload increase |
| Scheduling | Hide entirely (404) | Reduces author ability to preview & share |
| Build Validation | Manual checklist | Non-reproducible & error-prone |
| CSS Framework | Utility framework | Adds unused bytes + external tooling |

## Open Questions (Deferred)
- Missing media asset policy (warn vs fail) – propose: warn + aggregated report (decide pre-implementation).
- Long-term analytics (self-hosted) – out of initial scope.

## References
- Constitution v1.0.0
- Hugo documentation (content organization, templating, RSS customization)

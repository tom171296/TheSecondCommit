# Feature Specification: Blog Post Page & Markdown Content Pipeline

**Feature Branch**: `001-blog-post-page`  
**Created**: 2025-09-25  
**Status**: Draft  
**Input**: User description: "I want a blog post page that converts markdown into blog posts. Do a proposal for a design. The name of the site is TheSecondCommit. It is a podcast and blog site. I just want to focus on the blog part first."

## Execution Flow (main)
```
1. Parse user description from Input
   → If empty: ERROR "No feature description provided"
2. Extract key concepts from description
   → Identify: actors, actions, data, constraints
3. For each unclear aspect:
   → Mark with [NEEDS CLARIFICATION: specific question]
4. Fill User Scenarios & Testing section
   → If no clear user flow: ERROR "Cannot determine user scenarios"
5. Generate Functional Requirements
   → Each requirement must be testable
   → Mark ambiguous requirements
6. Identify Key Entities (if data involved)
7. Run Review Checklist
   → If any [NEEDS CLARIFICATION]: WARN "Spec has uncertainties"
   → If implementation details found: ERROR "Remove tech details"
8. Return: SUCCESS (spec ready for planning)
```

---

## ⚡ Quick Guidelines
- ✅ Focus on WHAT users need and WHY
- ❌ Avoid HOW to implement (no tech stack, APIs, code structure)
- 👥 Written for business stakeholders, not developers
 - 🔒 Respect Constitution Principle 2 (Zero External Runtime Dependencies): Do not introduce requirements that imply adding external CDNs, frameworks, or third-party scripts without explicit governance amendment.

### Section Requirements
- **Mandatory sections**: Must be completed for every feature
- **Optional sections**: Include only when relevant to the feature
- When a section doesn't apply, remove it entirely (don't leave as "N/A")

### For AI Generation
When creating this spec from a user prompt:
1. **Mark all ambiguities**: Use [NEEDS CLARIFICATION: specific question] for any assumption you'd need to make
2. **Don't guess**: If the prompt doesn't specify something (e.g., "login system" without auth method), mark it
3. **Think like a tester**: Every vague requirement should fail the "testable and unambiguous" checklist item
4. **Common underspecified areas**:
   - User types and permissions
   - Data retention/deletion policies  
   - Performance targets and scale
   - Error handling behaviors
   - Integration requirements
   - Security/compliance needs

---

## User Scenarios & Testing *(mandatory)*

### Primary User Story
As a content editor I can add a new Markdown file with required front matter so that it automatically
appears as a properly formatted blog post page on TheSecondCommit when the site is built.

### Acceptance Scenarios
1. **Given** a valid Markdown file with front matter (title, date, description, slug), **When** the build runs, **Then** the post appears at `/blog/<slug>/` with rendered HTML, metadata tags, internal navigation, and passes accessibility & performance budgets.
2. **Given** a Markdown file missing a required field (e.g., title), **When** the build script validates content, **Then** it fails with a clear error listing the missing field.
3. **Given** a post marked `draft: true`, **When** the production build runs, **Then** the draft is excluded from the output.
4. **Given** a post containing code fences, **When** rendered, **Then** code blocks are semantically marked (`<pre><code>`) without external highlight JS/CSS and remain readable in both light and dark contrast contexts.
5. **Given** pagination requirements (more than X posts on listing page), **When** viewing page 2, **Then** canonical and pagination navigation tags are present.

### Edge Cases
- Future-dated post: Accessible directly (URL known) but NOT listed until `date <= build date`; page shows "Scheduled" badge + `noindex` until publish date.
- Extremely long word/URL: Must wrap without horizontal scroll.
- Missing description: Build fails listing file(s) lacking `description`.
- Duplicate slug detection: Build fails if two posts share slug.
- Embedded media (images, audio link) missing file: Build should warn or fail? [NEEDS CLARIFICATION: Policy on missing assets]
- RSS feed size limit: Only 50 newest posts included; older still accessible via HTML archives.

## Requirements *(mandatory)*

### Functional Requirements
- **FR-001**: System MUST convert every Markdown file in `content/blog/` with required front matter into an HTML page at `/blog/<slug>/`.
- **FR-002**: System MUST enforce required front matter fields: `title`, `date`, `description`, `slug`, `draft` (boolean), and MUST fail build with clear error if any are missing (unless `draft` defaults to false).
- **FR-003**: System MUST exclude posts where `draft: true` OR (future date AND scheduling not enabled) from production build.
// Clarified by Clarifications Session 2025-09-25: scheduling enabled, behavior adjusted below.
- **FR-003**: System MUST treat future-dated posts as "Scheduled":
   - Generate the post page at `/blog/<slug>/` with a visible "Scheduled" badge
   - Add `<meta name="robots" content="noindex">` while date is in the future
   - Exclude from blog index pagination and RSS (if RSS in scope)
   - Automatically include in index and remove badge + `noindex` once build date >= post date.
- **FR-004**: System MUST generate SEO meta tags (title, description, og:title, og:description, og:type=article, article:published_time) per post using front matter.
- **FR-005**: System MUST create an accessible article layout: semantic landmarks (main, header, nav), heading hierarchy starting with a single H1 (post title).
- **FR-006**: System MUST provide internal navigation: link back to blog index and optional previous/next post links ordered by date descending.
- **FR-007**: System MUST validate slug uniqueness across all blog posts; duplicate slug detection MUST cause build failure.
- **FR-008**: System MUST generate a blog index page `/blog/` showing paginated list (default page size 10) with title, publish date, short description/excerpt.
- **FR-009**: System MUST create page-level breadcrumb or skip link enabling keyboard users to bypass navigation.
- **FR-010**: System MUST render code blocks without external libraries (no CDN highlight) and maintain WCAG AA contrast.
 - **FR-011**: System MUST fail the build if any Markdown image lacks an alt text. Decorative images MUST use an explicit empty alt (`![](...)`) indicating intentional emptiness; absence is treated as an error with a list of offending file paths.
- **FR-012**: System MUST add canonical URL tag `<link rel="canonical">` for each post.
- **FR-013**: System MUST generate an RSS 2.0 feed at `/blog/index.xml` containing only published (non-draft, non-future) posts, ordered newest first, limited to 50 entries; each item includes title, link, guid (slug-based), pubDate (RFC 1123), description (front matter), and `<atom:updated>` if `lastmod` present.
- **FR-014**: System MUST keep total critical payload for a post page ≤ 250KB excluding images/audio.
- **FR-015**: System MUST pass accessibility automated baseline (0 serious aXe violations) during CI.
- **FR-016**: System MUST produce deterministic output given identical inputs and pinned Hugo version.
- **FR-017**: System MUST provide a mechanism to mark a post as updated (e.g., optional `lastmod` field) and surface this in metadata.
- **FR-018**: System MUST ensure future extension to podcast episode layout does not require rework of blog templates (separation of concerns in partials). [Non-technical phrasing: design prepared for later podcast expansion.]
// New requirement derived from scheduling clarification.
- **FR-019**: System MUST expose a deterministic flag (e.g., in template context) indicating scheduled status to allow conditional UI (badge + exclusion logic) without code duplication.

*Ambiguity Markers / Clarifications Needed:*

### Key Entities *(include if feature involves data)*
- **Blog Post**: Represents a single article. Attributes: title (string), date (ISO date), description (string), slug (string), body (Markdown content), tags (list, optional), draft (bool), lastmod (optional date), audio (future expansion, ignored now). Relationships: None in this scope beyond ordering/pagination.
- **Blog Index Page**: Derived aggregation of Blog Post entities with pagination metadata (page number, total pages, items per page).

### Key Entities *(include if feature involves data)*
- **[Entity 1]**: [What it represents, key attributes without implementation]
- **[Entity 2]**: [What it represents, relationships to other entities]

---

## Review & Acceptance Checklist
*GATE: Automated checks run during main() execution*

## Clarifications
### Session 2025-09-25
- Q: How should the system treat future-dated blog posts? → A: Include page, badge, noindex, hide from index until date
- Q: What is the policy when a Markdown image is missing alt text? → A: Fail build (must supply alt or explicit empty alt)
- Q: Should generating a blog RSS feed be part of this feature? → A: Yes, include standard blog RSS now
- Q: If a post is missing a description in front matter, what should happen? → A: Fail the build (description required)


### Content Quality
- [ ] No implementation details (languages, frameworks, APIs)
- [ ] Focused on user value and business needs
- [ ] Written for non-technical stakeholders
- [ ] All mandatory sections completed

### Requirement Completeness
- [ ] No [NEEDS CLARIFICATION] markers remain
- [ ] Requirements are testable and unambiguous  
- [ ] Success criteria are measurable
- [ ] Scope is clearly bounded
- [ ] Dependencies and assumptions identified

---

## Execution Status
*Updated by main() during processing*

- [ ] User description parsed
- [ ] Key concepts extracted
- [ ] Ambiguities marked
- [ ] User scenarios defined
- [ ] Requirements generated
- [ ] Entities identified
- [ ] Review checklist passed

---

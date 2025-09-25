# Quickstart: Deploy Static Site via GitHub Pages

## Goal
Automated publish of Hugo site to GitHub Pages on every push to `main` with deterministic validation.

## Prerequisites
- Pinned Hugo version documented (update README if missing).
- `CNAME` file present at repo root containing custom domain.
- Validation scripts in `scripts/` directory.

## Manual Dry Run (Local)
1. Run front matter validation:
   ```powershell
   pwsh scripts/validate-front-matter.ps1 -ContentPath content
   ```
2. Run alt text scan:
   ```powershell
   pwsh scripts/check-alt-text.ps1 -ContentPath content
   ```
3. Deterministic build test:
   ```powershell
   pwsh scripts/deterministic-build.ps1
   ```
4. Confirm `CNAME` file exists:
   ```powershell
   Get-Content CNAME
   ```
5. Build site:
   ```powershell
   hugo --cleanDestinationDir
   ```

## Expected Outcomes
- No validation errors.
- Deterministic script reports no diff.
- `public/` contains built site with no drafts/future posts in indexes.

## After Workflow Added
- Push commit to `main` and monitor Actions for summary line.
- Visit custom domain to verify updated content.

## Troubleshooting
| Symptom | Possible Cause | Action |
|---------|----------------|--------|
| Diff failure | Non-deterministic timestamp or feed line missed | Extend ignore filter in script |
| Missing domain live | DNS not propagated / CNAME absent | Verify DNS + ensure CNAME included |
| Draft post visible | Front matter `draft` false or script logic gap | Fix front matter, rebuild |
| Build slow >2m | Large media or no cache | Consider adding resource caching |

## Next Enhancements (Future)
- Release-only mode toggle.
- Hash manifest & integrity check.
- Publish changelog artifact listing diffs.

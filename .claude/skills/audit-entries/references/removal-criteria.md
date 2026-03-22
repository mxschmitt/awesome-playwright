# Removal Criteria for Existing Entries

Ordered by severity. Higher severity = stronger case for removal.

## Auto-remove

### 1. Broken link
URL returns 404, is unreachable, or redirects to a generic/unrelated page.
- GitHub repos that return 404 from the API (deleted or made private)
- Websites that are down or parked

### 2. Archived repo
GitHub repo is explicitly marked as archived by the owner. The project is officially done/abandoned.

### 3. Deprecated by author
README, description, or repo notice explicitly says "deprecated", "unmaintained", "no longer maintained", or "use X instead."
- Note the suggested successor if one exists
- If the successor is not yet on the list, consider adding it

### 4. Project pivoted away from Playwright
The project no longer targets or supports Playwright. Removed from deps, docs no longer mention it, etc.

## Remove (with judgment)

### 5. Abandoned
No commits in 2+ years AND low adoption (under ~50 stars). The project never gained traction and is no longer maintained.
- Check issue tracker too - unanswered issues for 1+ year reinforces this
- Exception: very small, focused tools that are "feature-complete" may not need updates

### 6. Superseded
An official or clearly superior alternative now exists on the list.
- Example: unofficial integration when an official one was later released
- Only remove if the superseding entry is actually on the list already
- If both are listed and the unofficial one is abandoned, remove the unofficial one

## Flag for review (don't auto-remove)

### 7. Stale but popular
No commits in 2+ years but still has significant adoption (100+ stars, meaningful npm downloads).
- These may still work fine and be widely used
- Flag for the maintainer to decide - they may want to add a note or find a maintained fork

### 8. Unclear maintenance
Sporadic commits, no releases, but not clearly dead. Hard to tell if maintained.

## NOT removal criteria

These alone are NOT reasons to remove an entry:

- **Low stars** - if the project is maintained and useful, stars don't matter
- **Commercial/SaaS product** - if it was accepted onto the list, being commercial isn't grounds for removal
- **Slow release cadence** - some tools are "done" and stable; not every project needs monthly releases
- **Old entry** - being on the list for a long time is fine; longevity can be a positive signal
- **Showcase age** - showcase entries are historical; the project was notable when added

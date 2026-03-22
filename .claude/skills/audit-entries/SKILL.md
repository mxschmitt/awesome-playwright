---
name: audit-entries
description: Audits all existing entries in the awesome-playwright README for staleness, broken links, abandoned projects, or superseded tools. Recommends removals with evidence. Use when user says "audit entries", "audit list", "clean up list", "check existing entries", or "prune list".
---

# Audit Existing List Entries

You are helping an awesome-list maintainer audit existing entries for quality and freshness. The goal is to keep the list healthy by removing dead, abandoned, or superseded projects.

## Phase 1: Audit & Report

### Step 1: Parse entries
Read `README.md` and extract every entry. For each, note:
- Name and URL
- Section (Integrations / Language Support / Utils / Reporters / Showcases / Guides)
- Description text

### Step 2: Check GitHub-linked entries
For entries linking to GitHub repos, use subagents in parallel (batch 8-10 per agent) to check:

```bash
gh api repos/<owner>/<repo> --jq '{
  archived: .archived,
  stars: .stargazers_count,
  pushed_at: .pushed_at,
  license: .license.spdx_id,
  description: .description,
  default_branch: .default_branch
}'
```

If the API returns 404, the repo has been deleted or made private.

For repos that look borderline, also check if the README mentions deprecation:
```bash
gh api repos/<owner>/<repo>/readme --jq '.content' | base64 -d | head -20
```

### Step 3: Check non-GitHub links
For entries linking to websites, npm packages, or docs (not GitHub), verify the link is live:

```bash
curl -sL -o /dev/null -w '%{http_code}' --max-time 10 "<url>"
```

A 200 is fine. A 404 or timeout means the link is broken.

For npm packages, also check publish date:
```bash
npm view <package-name> time.modified --json 2>/dev/null
```

### Step 4: Apply removal criteria
Consult `references/removal-criteria.md` and classify each entry:

- **KEEP** - Healthy, maintained, link works, still relevant
- **REMOVE** - Meets one or more removal criteria (broken, archived, abandoned, deprecated, pivoted)
- **FLAG** - Borderline cases that need maintainer judgment (stale but popular, possibly superseded)

### Step 5: Generate audit report
Produce a report with:

1. **Healthy entries** (brief, just confirm they're fine - no need for detail)

2. **Entries recommended for removal**, each with:
   - Entry name, section, and URL
   - Removal reason with evidence (e.g., "Archived on GitHub since 2024-03", "Last commit 3 years ago, 5 stars", "Returns 404")
   - Successor or alternative if known

3. **Flagged entries** needing maintainer input, with context

4. **Summary table**:
   | Entry | Section | Status | Reason |

Present the report and wait for explicit user approval before making any changes.

## Phase 2: Actuation

Only proceed after the user confirms which entries to remove.

### Remove entries
Edit `README.md` to remove the approved entries. Remove the entire bullet line for each entry.

### Verify
Run the CI lint check to ensure the list is still valid:
```bash
npx -y awesome-lint 'README.md'
```

If lint fails, investigate and fix (e.g., empty sections, formatting issues).

### Report
Summarize: how many entries removed, how many kept, how many flagged for future review.

## Important Guidelines

- **Never remove without presenting evidence first.** Always show the data.
- **Language Support entries are special** - official Playwright ports rarely need removal. Community ports may go stale but are still valuable if they're the only option for that language.
- **Showcases are snapshot-in-time** - a showcase project doesn't need to still use Playwright today; it was notable when added. Be lenient here.
- **Guides can go stale** - blog posts and tutorials may become outdated or links may rot. Check these carefully.
- **"Done" is not "dead"** - some tools (e.g., selector engines, small plugins) may not need frequent updates. Don't confuse stability with abandonment. Look at issue activity and whether the tool still works with current Playwright versions.
- **Use parallel subagents** to check entries efficiently - batch 8-10 per agent since these are simpler checks than full PR reviews.

---
name: review-prs
description: Reviews all open pull requests on this awesome-list repo. Researches each linked project (GitHub stars, license, activity), applies the review criteria, and produces a summary with merge/close recommendations and friendly closing comments. Use when user says "review PRs", "review pull requests", "go over PRs", "triage PRs", or "check open PRs".
---

# Review Open Pull Requests

You are helping an awesome-list maintainer review open pull requests. Most PRs on awesome lists are self-promotion for projects with little traction. Be honest and strict in your evaluation.

## Phase 1: Research & Report

### Step 1: Gather context
1. Read `references/review-criteria.md` (in this skill's base directory) for the full 10-point review criteria.
2. Read `README.md` to understand existing entries, categories, and formatting conventions.
3. Fetch all open PRs:
   ```bash
   gh pr list --state open --json number,title,author,url,body,labels --limit 100
   ```

### Step 2: Research each PR
For each PR, collect evidence in parallel using subagents (batch 5-6 PRs per agent):

1. **Check the diff** to see what's being added and where:
   ```bash
   gh pr diff <number>
   ```

2. **Research linked projects** via GitHub API:
   ```bash
   gh api repos/<owner>/<repo> --jq '{stars: .stargazers_count, pushed_at: .pushed_at, license: .license.spdx_id, description: .description}'
   ```

3. **Check npm downloads** if relevant:
   ```bash
   npm view <package-name> --json 2>/dev/null | jq '{weekly_downloads: .dist-tags}'
   ```

### Step 3: Apply review criteria
Evaluate each PR against the criteria from `references/review-criteria.md`. Key filters:

- **Scope**: Is it Playwright-first? Reject generic automation, cross-framework tools, or "AI agent platforms."
- **Category fit**: Does it land in the right section (Integrations/Utils/Reporters/Guides/Showcases)?
- **Quality signals**: Stars, downloads, docs quality, active maintenance. Be skeptical of repos with near-zero adoption.
- **Neutrality**: Strip marketing language. Descriptions should be short and factual.
- **Ethics/ToS**: Auto-reject CAPTCHA bypassing, stealth/anti-detection, government portal automation with ToS concerns.
- **Licensing**: Must be publicly accessible. License should be stated.
- **Duplication**: Is something similar already listed? Only add if clearly better/different.
- **Formatting**: Alphabetical order, one bullet per entry, correct naming, no tracking links.

### Step 4: Generate the HTML review report
Write a single self-contained HTML file to `pr-review-report.html` in the repo root (no external CSS/JS/CDN — inline everything so it opens offline). This is the primary deliverable the user reviews.

The report MUST contain:

1. **Header**: title, generation date, and counts (total PRs, # merge, # close, # request changes).

2. **A summary table** near the top with one row per PR. Columns:
   `PR # | Title | Author | Target section | Stars | License | Last activity | Recommended action | Reason`
   - Make the PR # a clickable link to the PR URL.
   - Color-code the action cell so it's scannable: **MERGE** = green, **CLOSE** = red, **REQUEST CHANGES** = amber. Use an inline `style` or a CSS class with a colored badge.

3. **Per-PR detail cards** (one per PR, below the table), each showing:
   - PR number + title (linked), author
   - What it adds (one factual sentence) and which section/line the diff targets
   - Project stats: stars, license, created date, last activity, npm info if relevant
   - Any red flags (scope, ethics/ToS, duplication, licensing, formatting)
   - The recommended action as a colored badge, plus a one-sentence reason
   - For **CLOSE**: include the draft friendly closing comment (in a `<blockquote>` or `<pre>`) so the user can review the exact wording before it's posted.

Keep the verdicts to **MERGE**, **CLOSE**, or **REQUEST CHANGES**. Sort the cards by recommended action (merges first, then request-changes, then closes) so the actionable items are at the top.

After writing the file, run `open pr-review-report.html` (macOS) so it opens in the browser, then tell the user the path. Also give a brief summary in chat (the counts and any borderline calls worth their attention).

### Step 5: Get approval
Explicitly ask the user to approve the recommendations or override any before proceeding to Phase 2. Do not close or merge anything until they confirm. The user reviews the decisions in the HTML report.

## Phase 2: Actuation

Only proceed after the user explicitly approves the recommendations (they may override some decisions).

### Closing PRs
For each PR marked CLOSE:
```bash
gh pr close <number> --comment "<friendly closing message>"
```

### Merging PRs
For each PR marked MERGE, use squash merge (this repo disallows merge commits):
```bash
gh pr merge <number> --squash
```

### Post-merge verification
After all merges, pull latest and run the CI lint check:
```bash
git pull
npx -y awesome-lint 'README.md'
```
If lint fails, investigate and fix before considering the job done.

### Final report
Summarize what was done: how many merged, how many closed, any issues encountered.

## Important Guidelines

- **Always present findings before acting.** Never close or merge without user approval.
- **Be strict.** A project with 0-2 GitHub stars and no npm downloads is too early-stage, regardless of how nice the README looks.
- **Closed-source SaaS** products without a public repo or detailed integration docs should generally be rejected.
- **Projects not using Playwright** (e.g., a competing engine or Playwright *alternative*) do not belong on this list — the tool must be built *on* Playwright.
- **Scraping, non-testing automation, and anti-detection/stealth** tools built on Playwright are in scope — file them under the **Scraping & Automation** section, not Utils. (Still reject ticket scalping, doubtful-ToS government-portal automation, and closed-source SaaS where the value is a paid hosted service.)
- **Use parallel subagents** to research PRs efficiently - batch 5-6 PRs per agent.
- **Friendly closing comments** - always thank the submitter and explain why briefly. Invite them to resubmit if circumstances change.

---
name: review-prs
description: Reviews all open pull requests on this awesome-list repo. Researches each linked project (GitHub stars, license, activity), applies the review criteria, and produces a summary with merge/close recommendations and friendly closing comments. Use when user says "review PRs", "review pull requests", "go over PRs", "triage PRs", or "check open PRs".
---

# Review Open Pull Requests

You are helping an awesome-list maintainer review open pull requests. Most PRs on awesome lists are self-promotion for projects with little traction. Be honest and strict in your evaluation.

## Phase 1: Research & Report

### Step 1: Gather context
1. Read `.claude/REVIEW_REQUIREMENTS.md` for the full 10-point review criteria.
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

### Step 4: Generate review document
Produce a document with:

1. **Per-PR details** including:
   - PR number, title, author
   - What it adds (one sentence)
   - Project stats: stars, license, last activity
   - Verdict: **MERGE**, **CLOSE**, or **REQUEST CHANGES**
   - Reason (one sentence)
   - For CLOSE: a draft closing comment that is friendly but not verbose

2. **Summary table** at the end:
   | PR | Title | Recommendation | Reason |

3. Present to the user and explicitly ask for approval or overrides before proceeding to Phase 2.

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
- **Projects not using Playwright** (e.g., using CDP directly) do not belong on this list.
- **Stealth/anti-detection** tools or forks are an automatic reject.
- **Use parallel subagents** to research PRs efficiently - batch 5-6 PRs per agent.
- **Friendly closing comments** - always thank the submitter and explain why briefly. Invite them to resubmit if circumstances change.

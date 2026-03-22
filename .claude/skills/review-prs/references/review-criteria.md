# Awesome-Playwright Review Criteria

## 1) Is it in-scope for this list?

Accept if it's clearly one of:
- Playwright-first tool/library/integration (not "works with everything")
- Widely useful to Playwright users (testing, tooling, ecosystem)
- Guide/resource that is Playwright-specific and high quality
- Showcase that's notable and broadly credible

Reject / be skeptical if it's:
- Generic automation/scraping infra, "AI agent platforms," or cross-framework directories
- A niche script for a single site or personal automation project
- Mostly marketing for a SaaS product without a clear Playwright integration

## 2) Category fit

Make sure the PR lands in the right section:
- Integrations: frameworks/plugins that integrate Playwright into another ecosystem
- Utils: tangible tools/libraries you can use with Playwright
- Reporters: Playwright Test reporters / result sinks
- Guides: articles, tutorials, newsletters, docs
- Showcases: notable projects/products using Playwright (generally recognizable / credible)

If it's "content for AI agents," "prompt packs," or "skills," it usually belongs in Guides (if at all), not Utils.

## 3) Quality & maturity signals

Prefer entries with at least some proof of life:
- Reasonable README/docs (what it does, how to use it)
- Active maintenance (recent commits/releases)
- Some adoption (stars/downloads/users) or clear credibility (well-known org)

Be cautious with:
- Brand-new repos with near-zero adoption
- Unclear maintenance, incomplete docs, "demo only"
- "Enterprise-grade" claims without substance

## 4) Neutrality (no marketing copy)

Descriptions should be short and factual.
- Avoid: "AI-powered," "revolutionary," "best," "enterprise-grade," pricing callouts
- Prefer: "Uploads Playwright traces/screenshots to X" / "CLI to generate..." etc.

## 5) Ethics / ToS / "anti-bot" red flags

Auto-reject or heavily scrutinize anything centered on:
- CAPTCHA solving/bypassing, stealth mode, anti-detection, ticket scalping
- Automating government portals or services where ToS compliance is doubtful
- Anything that reads like "bypass restrictions"

If it's even borderline, keep it out of the list.

## 6) Licensing & accessibility

- Must be publicly accessible and not paywalled to evaluate
- License should be stated (especially for repos); avoid overly restrictive licenses if it limits usefulness (not an auto-reject, but a factor)
- Links must be stable (no "just a tweet," broken docs, etc.)

## 7) Duplication / fragmentation

- Is something similar already listed? If yes, only add if clearly better/different.
- Avoid adding multiple near-identical tools (especially in trendy categories like "AI test generation").

## 8) Clean formatting

- Alphabetical order if that section uses it (or consistent with nearby items)
- One bullet per entry
- Correct naming/capitalization
- No tracking/referral links
- Keep descriptions to one line if possible

## 9) Evidence check (2-minute verification)

Reviewer should always check:
- The link works
- README confirms the claim in the description
- It actually uses/targets Playwright (not just mentions it)

## 10) Suggested decision outcomes

- **Merge as-is**: perfect fit, neutral description
- **Request changes**: good fit, but description/category/format needs tweaks
- **Close politely**: out of scope, too niche, questionable ethics/ToS, or too early-stage

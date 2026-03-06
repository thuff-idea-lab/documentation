# Sprint 0 Checklist — ✅ COMPLETE (March 5, 2026)

> **Sprint 0 is done.** All infrastructure is live. The nightly pipeline runs automatically at 2am ET.
> First confirmed run: 4 ideas written to Supabase, 4 evaluations scored (WATCH 55/100). No BUILD ideas yet.
> **Next work is Sprint 1 — see bottom of this file.**

## Quick Reference
```
[x] GitHub PAT with models scope → saved locally (add to .env next)
[x] Supabase project created → 3 env vars copied
[x] venture-studio repo created + cloned locally
[x] commit.gpgsign false set on new repo
[x] node --version confirms v18+ (running v22)
[x] Create .env file with all saved values
[x] Run schema.sql in Supabase SQL editor
[x] Add SUPABASE_URL + SUPABASE_SERVICE_ROLE_KEY as GitHub Actions secrets
[x] npm run pipeline → confirmed — 4 ideas written, 4 evaluations scored (WATCH 55/100)
```

---

## ✅ Step 1 — GitHub Personal Access Token (DONE)
Token generated with `models: read` scope. Saved locally — add to `.env` in next step.

---

## ✅ Step 2 — Supabase Account (DONE)
Project `venture-studio` created. Three values saved locally:
- `SUPABASE_URL`
- `SUPABASE_ANON_KEY`
- `SUPABASE_SERVICE_ROLE_KEY`

---

## ✅ Step 3 — Create the Code Repo (DONE)
Repo `venture-studio` created on GitHub, cloned locally, `commit.gpgsign false` set.

---

## ✅ Step 4 — Verify Node.js (DONE)
Running Node v22.14.0 — well above v18 requirement.

---

## ✅ Step 5 — Create .env File (DONE)
Create the real `.env` file in `venture-studio/` with all saved values:
```bash
cd "/Users/tylerhuffman/Documents/DEV PROJECTS/thuff-idea-lab/venture-studio"
cp .env.example .env
# then fill in real values
```

Values to paste in:
- `GITHUB_TOKEN` — your GitHub PAT
- `SUPABASE_URL` — your project URL
- `SUPABASE_ANON_KEY` — your publishable key
- `SUPABASE_SERVICE_ROLE_KEY` — your secret key

---

## ✅ Step 6 — Run Database Schema in Supabase (DONE)
1. Go to **supabase.com → your venture-studio project → SQL Editor**
2. Paste the contents of `venture-studio/prisma/schema.sql`
3. Click **Run**
4. Verify tables appear: `ideas`, `evaluations`, `projects`

---

## ✅ Step 7 — Add GitHub Actions Secrets (DONE)
So the nightly pipeline can connect to Supabase when it runs in the cloud:
1. Go to **github.com/thuff-idea-lab/venture-studio → Settings → Secrets and variables → Actions**
2. Add two secrets:

| Secret Name | Value |
|-------------|-------|
| `SUPABASE_URL` | your project URL |
| `SUPABASE_SERVICE_ROLE_KEY` | your secret key |

(`GITHUB_TOKEN` is automatic — no setup needed)

---

## ✅ Step 8 — First Pipeline Run (DONE)
```bash
cd "/Users/tylerhuffman/Documents/DEV PROJECTS/thuff-idea-lab/venture-studio"
npm run pipeline
```
✅ Done when: ideas appear in your Supabase `ideas` table.

---

## Accounts to Set Up Later (Don't Do These Yet)

| Tool | When you need it | Sign up at |
|------|-----------------|-----------|
| PostHog | When first project goes live | posthog.com |
| Beehiiv | When first project launches | beehiiv.com |
| Resend | When email capture is needed | resend.com |
| Firecrawl | When Scout agent is running | firecrawl.dev |
| SerpAPI | When Evaluator needs competition scoring | serpapi.com |
| Buffer | When Distributor agent is built | buffer.com |
| Product Hunt | When first project launches | producthunt.com |
| Indie Hackers | When first project launches | indiehackers.com |

---

## Already Done
- [x] GitHub account (personal)
- [x] Vercel account

---

## Sprint 1 — Improve Signal Quality

**Goal:** Get the pipeline producing at least one `BUILD`-worthy idea (score ≥ 65).

Priority order:

1. **Fix Reddit RSS fetch** — `studio/agents/scout/sources/reddit.ts` currently fails silently. Debug why and restore it as a data source.
2. **Add more targeted sources** — `studio/config/sources.json` has generic feeds. Add niche RSS feeds (Indie Hackers, specific subreddits via RSS) that surface stronger business signals.
3. **Tune evaluator scoring** — `studio/agents/evaluator/index.ts` uses rule-based scoring. Understand why ideas max out at 55/100 and adjust weights or keyword signals in `studio/config/scoring_weights.json`.
4. **Add LLM scoring pass** — Once rule-based scoring is understood, add a GitHub Models LLM call to the evaluator for ideas that score 45–64 to see if they deserve a higher score.

Done when: `npm run pipeline` produces at least one idea with recommendation `BUILD` and a row appears in the `projects` table.

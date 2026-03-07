# AI Internet Venture Studio — Repo Architecture (Copilot-Friendly)

This document is the **single source of truth** for building the Venture Studio in VS Code with GitHub Copilot.

## Goal
A portfolio system of AI agents that continuously:
1) discover opportunities
2) evaluate/prioritize
3) generate project plans
4) build MVPs
5) test traction
6) scale winners / kill losers

Supports **any internet business asset** (websites, directories, SaaS, YouTube channels, trading bots, newsletters, data products, etc.).

---

## 0) High-level decisions
- **Monorepo** for the venture studio “control plane” + per-project folders.
- Agents communicate via **typed JSON contracts** stored in the database and as files in `/studio/out/`.
- Start with a **Minimum Viable Studio** (3 agents): Scout → Evaluate → Plan. Add Board/Build/Test/Scale after.
- The full pipeline is: Scout → Evaluate → **Board** → Plan → Build → Test → Scale/Kill.
- Keep everything deterministic + testable: agents should be small scripts with clear inputs/outputs.

### Portfolio heuristic
- Default bias: find **base hits** — ideas that are easy to build, easy to plan, easy to validate, and plausibly monetizable with low ongoing operational burden.
- Allow **homeruns** when the signal is unusually strong, the pain is clear, and the path to monetization/distribution is concrete.
- Reject the middle zone: ideas that are neither easy base hits nor exceptional upside opportunities.
- Optimize agent outputs for **builder-ready context**, not just clever idea titles. A good idea must include enough specificity that downstream Planner/Builder agents can execute with minimal guesswork.

---

## 1) Folder structure

```
venture-studio/
  README.md
  .env.example
  package.json
  tsconfig.json

  docs/
    VENTURE_STUDIO_CANVAS.md
    AGENT_CONTRACTS.md
    RUNBOOK.md
    AI_STRATEGY.md           # LLM strategy, GitHub Models, lib/llm.ts
    TOOLS_AND_INTEGRATIONS.md # Every free tool + when to use it
    VALIDATION_STRATEGY.md   # How to validate ideas without Reddit risk

  studio/
    config/
      niches.json              # optional: constraints/tags (not required)
      sources.json             # where Scout pulls from (reddit feeds, rss, etc.)
      scoring_weights.json     # evaluator scoring weights

    contracts/
      idea.schema.json
      evaluation.schema.json
      board_decision.schema.json
      project.schema.json
      mvp.schema.json
      experiment.schema.json
      observatory_report.schema.json

    agents/
      scout/
        index.ts
        prompts.ts
        sources/
          reddit.ts
          trends.ts
          forums.ts

      evaluator/
        index.ts
        scoring.ts

      planner/
        index.ts
        project_types.ts

      builder/                 # phase 2
        index.ts
        templates/
          nextjs-tool/
          directory-shell/
          landing-page/
          newsletter-kit/
          youtube-kit/
          trading-bot-kit/

      distributor/             # phase 2
        index.ts
        channels/
          reddit.ts
          x.ts
          email.ts

      analytics/               # phase 2
        index.ts
        metrics.ts

      expander/                # phase 3
        index.ts
        strategies/

      board/                   # phase 2 — deliberation gate between Evaluator and Planner
        index.ts               # orchestrates the board session
        ceo.ts                 # strategic fit, portfolio balance
        cmo.ts                 # distribution, channels, audience
        coo.ts                 # build complexity, timelines, risks
        cfo.ts                 # unit economics, CAC, LTV, margins
        deliberation.ts        # synthesis + vote tallying → GO | NO_GO | WATCH

      observatory/             # phase 3 — self-improving quality loop
        index.ts
        quality_scorer.ts      # scores agent outputs over time
        prompt_versioner.ts    # tracks prompt versions + outcome correlation
        drift_detector.ts      # alerts when agents degrade or repeat
        improvement_report.ts  # surfaces suggested prompt changes for human review

    lib/
      llm.ts                   # GitHub Models/local routing (see docs/AI_STRATEGY.md)
      db.ts                    # DB client (currently Supabase)
      logger.ts
      validate.ts              # JSON schema validation
      urls.ts
      time.ts

    out/
      ideas.ndjson
      evaluations.ndjson
      board_decisions.ndjson
      projects.ndjson
      mvp.ndjson
      experiments.ndjson
      observatory_reports.ndjson

  apps/
    dashboard/
      (Next.js admin UI)

  projects/
    _templates/
      web_tool_nextjs/
      directory_nextjs/
      youtube_channel/
      newsletter/
      trading_bot/
      micro_saas/

    active/
      <project-slug>/
        project.json
        notes.md
        mvp/
        build/

    archived/
      <project-slug>/

  prisma/
    schema.prisma
    migrations/

  scripts/
    run-agent.ts               # run one agent
    run-pipeline.ts            # run Scout→Evaluate→Board→Plan
    run-board.ts               # run Board deliberation only
    run-observatory.ts         # run Observatory quality report
    promote-project.ts         # move from lab to “active”
    archive-project.ts

  .github/
    workflows/
      scout.yml
      evaluate.yml
      board.yml
      plan.yml
      nightly-pipeline.yml
      observatory.yml
```

---

## 2) The “Minimum Viable Venture Studio” (start here)

### Phase 1 (Weekend build)
- ✅ Scout Agent (discover)
- ✅ Evaluator Agent (score)
- ✅ Planner Agent (choose project type + draft build plan)
- ⏳ Dashboard (simple table view + human approval gate)

### Current repo status
- ✅ Scout is implemented and actively sourcing ideas from multiple lanes.
- ✅ Evaluator is implemented and aligned to the current portfolio heuristic.
- ✅ Planner is implemented and producing builder-ready project plans.
- ✅ Dashboard is implemented as the review queue and temporary human approval gate.
- ✅ Human approval gate is implemented through dashboard-managed project statuses.
- ⏳ Builder agent is the current implementation focus.
- ⏳ Board and Observatory are not built yet.

### Immediate operating model
- Treat the dashboard as the temporary approval gate before Builder exists.
- Current flow in code: Scout → Evaluator → Planner.
- Near-term desired flow: Scout → Evaluator → Planner → Human review dashboard → Builder.
- Later target flow: Scout → Evaluator → Board → Planner → Human review dashboard → Builder.

### Phase 2 (Next)
- Builder Agent (generate MVP scaffold in `/projects/active/<slug>`)
- Distributor Agent (draft posts + optional ad kit)
- Analytics Agent (score traction)
- **Board of Agents** — CEO, CMO, COO, CFO deliberation gate between Evaluator and Planner
  - Only `WATCH`/`BUILD` ideas go to the Board for a GO / NO_GO / WATCH vote
  - Each persona has a constrained system prompt (deliberately blind to other domains until synthesis)
  - Grounded in hard data from DB — not opinion, not creative writing

### Phase 3 (Scale)
- Expander Agent (clone/expand winners)
- Spinout Agent (split repo/domain)
- **Observatory Agent** — monitors all agent output quality over time
  - Scores output signal/noise per agent per run
  - Detects drift (e.g., Scout recycling same ideas for weeks)
  - Versions prompts and correlates versions with outcome quality
  - Surfaces improvement suggestions for human review before any prompt is changed
  - Does **not** self-modify autonomously — human approval required

---

## 3) Data model (Prisma)

### Tables
- `Idea`
  - `id`, `title`, `summary`, `evidence`, `sources[]`, `keywords[]`, `tags[]`, `assetTypeHint`, `createdAt`
- `Evaluation`
  - `id`, `ideaId`, `scoreTotal`, `scoreBreakdown(json)`, `recommendation` (DROP | WATCH | BUILD), `notes`, `createdAt`
- `Project`
  - `id`, `ideaId`, `slug`, `projectType`, `status` (PLANNED | APPROVED | BUILDING | LIVE | PAUSED | KILLED | ARCHIVED), `priority`, `decisionNotes`, `approvedAt`, `killedAt`, `buildStartedAt`, `buildFinishedAt`, `lastBuildError`, `plan(json)`, `createdAt`
- `MVP`
  - `id`, `projectId`, `spec(json)`, `repoPath`, `deployTarget`, `createdAt`
- `Experiment`
  - `id`, `projectId`, `channel` (REDDIT | X | ADS | EMAIL | SEO), `setup(json)`, `metrics(json)`, `result` (KILL | ITERATE | SCALE)
- `BoardDecision`
  - `id`, `evaluationId`, `ceoPov(json)`, `cmoPov(json)`, `cooPov(json)`, `cfoPov(json)`, `verdict` (GO | NO_GO | WATCH), `rationale`, `createdAt`
- `ObservatoryReport`
  - `id`, `runAt`, `agentScores(json)`, `driftAlerts(json)`, `promptVersions(json)`, `suggestedImprovements(json)`, `approvedAt`

### Key principle
Everything an agent outputs must be stored as structured JSON in DB **and** optionally in `/studio/out/*.ndjson` for debugging.

---

## 4) Agent contracts (JSON)

### 4.1 Idea contract (`IdeaRecord`)
```json
{
  "title": "string",
  "summary": "string",
  "evidence": [{"type":"link|metric|quote","value":"string"}],
  "sources": [{"platform":"string","url":"string","context":"string"}],
  "keywords": ["string"],
  "tags": ["string"],
  "assetTypeHint": "website|directory|saas|youtube|newsletter|bot|data|unknown"
}
```

### 4.2 Evaluation contract (`EvaluationRecord`)
```json
{
  "ideaTitle": "string",
  "scoreTotal": 0,
  "scoreBreakdown": {
    "demand": 0,
    "monetization": 0,
    "competition": 0,
    "automationPotential": 0,
    "buildComplexity": 0
  },
  "recommendation": "DROP|WATCH|BUILD",
  "notes": "string"
}
```

### 4.3 Board decision contract (`BoardDecisionRecord`)
```json
{
  "evaluationId": "string",
  "ideaTitle": "string",
  "ceoPov": {
    "strategicFit": "string",
    "portfolioBalance": "string",
    "vote": "GO|NO_GO|WATCH"
  },
  "cmoPov": {
    "targetAudience": "string",
    "distributionChannels": ["string"],
    "marketingRisk": "string",
    "vote": "GO|NO_GO|WATCH"
  },
  "cooPov": {
    "buildComplexity": "LOW|MEDIUM|HIGH",
    "estimatedHours": 0,
    "topRisks": ["string"],
    "vote": "GO|NO_GO|WATCH"
  },
  "cfoPov": {
    "estimatedCAC": "string",
    "revenueModel": "string",
    "breakEvenTimeline": "string",
    "vote": "GO|NO_GO|WATCH"
  },
  "verdict": "GO|NO_GO|WATCH",
  "rationale": "string"
}
```

### 4.4 Project contract (`ProjectRecord`)
```json
{
  "slug": "string",
  "projectType": "website|directory|saas|youtube|newsletter|bot|data",
  "oneSentencePitch": "string",
  "targetUser": "string",
  "primaryMonetization": ["ads|affiliate|leadgen|subscription|product|data"],
  "mvpDefinition": {
    "deliverables": ["string"],
    "timeboxHours": 6,
    "successMetric": "string"
  },
  "buildPlan": {
    "steps": ["string"],
    "tech": ["string"],
    "risks": ["string"],
    "constraints": ["string"]
  },
  "executionContext": {
    "trigger": "string",
    "coreInputs": ["string"],
    "coreOutputs": ["string"],
    "mustHaveV1Features": ["string"],
    "outOfScopeForV1": ["string"],
    "suggestedIntegrations": ["string"],
    "operationalNotes": ["string"]
  },
  "testPlan": {
    "channels": ["REDDIT","X","ADS","EMAIL","SEO"],
    "budget": 0,
    "tracking": ["string"],
    "goNoGo": ["string"]
  }
}
```

### 4.5 MVP build artifact contract (`MVPBuildRecord`)
```json
{
  "projectSlug": "string",
  "projectType": "website|directory|saas",
  "status": "BUILDING|PAUSED",
  "template": "nextjs-studio-v1",
  "generatedAppPath": "projects/active/<slug>/build",
  "artifacts": ["README.md", "app/page.tsx", "package.json"],
  "validation": {
    "installCommand": "npm install",
    "buildCommand": "npm run build",
    "buildPassed": true,
    "notes": ["string"]
  },
  "generatedAt": "ISO-8601 string"
}
```

---

## 5) How Copilot should be used (practical)

### Rule
**Never** paste long chat threads into Copilot.

### Use this doc as the anchor
Prompt Copilot like:
- “Read `/docs/VENTURE_STUDIO_CANVAS.md` and implement the Prisma schema.”
- “Using the `IdeaRecord` schema in `/studio/contracts/idea.schema.json`, implement `studio/agents/scout/index.ts`.”
- “Implement `scripts/run-pipeline.ts` to run Scout→Evaluator→Planner and write outputs to DB + `/studio/out/*.ndjson`.”

### Work file-by-file
Copilot performs best when each task is a single file with a clear contract.

---

## 6) Execution: scripts you can run

### Run a single agent
```
node scripts/run-agent.ts scout
node scripts/run-agent.ts evaluator
node scripts/run-agent.ts board
node scripts/run-agent.ts planner
node scripts/run-agent.ts observatory
```

### Run the pipeline
```
node scripts/run-pipeline.ts
```

### Run the board deliberation only
```
node scripts/run-board.ts
```

### Run the observatory report
```
node scripts/run-observatory.ts
```

---

## 7) GitHub Actions schedules

- `scout.yml` — daily
- `evaluate.yml` — daily
- `board.yml` — daily (runs after evaluate; gates Planner)
- `plan.yml` — daily (only processes `GO` verdicts)
- `nightly-pipeline.yml` — nightly end-to-end
- `observatory.yml` — weekly (quality report + drift detection)

Each workflow:
- installs deps
- runs the script
- stores logs/artifacts

---

## 8) “Anything goes” without SEO chaos

This studio is **not** one public website.
It’s a portfolio generator.

- Some outputs become websites.
- Some become directories.
- Some become channels.
- Some become bots.

The **studio dashboard** is the control center; each project lives in `/projects/active/<slug>`.

---

## 9) Next build steps (do in order)

### Step 1 — Repo + DB
1) Create the folders above
2) Add Prisma schema + migrations
3) Add `studio/lib/db.ts`

Current status:
- Mostly done.
- The live repo currently uses Supabase directly in `studio/lib/db.ts` rather than a Prisma client.

### Step 2 — Scout Agent (MVP)
- Start with 1–2 sources (e.g., Reddit RSS + Google Trends)
- Output `IdeaRecord` entries

Current status:
- Done and materially expanded beyond the original MVP.

### Step 3 — Evaluator Agent
- Read new Ideas from DB
- Produce Evaluation records

Current status:
- Done.

### Step 4 — Planner Agent
- For `BUILD` ideas now, and later for `GO` board verdicts, generate `ProjectRecord`
- Create `/projects/active/<slug>/project.json`

Current status:
- Done.
- Planner currently writes planned projects directly without a human approval gate.

### Step 5 — Dashboard + Human Review Gate
- Show planned projects with joined idea + evaluation context
- Add `Approve` / `Kill` actions on projects
- Make Builder consume only approved projects
- Treat this as the temporary decision gate until Board exists

Current status:
- Not started.
- This is the next logical build step.

MVP dashboard requirements:
- Views for `PLANNED`, `APPROVED`, `BUILDING`, and `KILLED` projects.
- Show project plan, idea summary, evaluation notes, and source URLs so a human can inspect the original thread or listing.
- Allow setting a numeric or ordinal priority on approved projects.
- `KILLED` should behave as mostly permanent, but remain visible in an archive view for later review.
- Builder should not start every approved project at once. It should claim work from the approved queue by priority with an explicit concurrency limit.
- End-state goal: Builder automatically pulls from the approved queue without requiring manual "start build" clicks.

Recommended status semantics:
- `PLANNED`: waiting for human review.
- `APPROVED`: approved for build queue, but not yet claimed by Builder.
- `BUILDING`: Builder has claimed the project and is actively working on it.
- `LIVE`: build completed and project is deployed or otherwise active.
- `PAUSED`: needs human review before build resumes.
- `KILLED`: rejected from the active portfolio, but still visible in archive/history.

Recommended dashboard actions:
- From `PLANNED`: `Approve`, `Kill`.
- From `APPROVED`: update priority, move back to `PLANNED`, or `Kill`.
- From `BUILDING`: view progress and optionally `Pause`.
- From `KILLED`: view archive details and optionally restore to `PLANNED`.

Recommended Builder queue behavior:
- Builder should only read from `APPROVED` projects.
- Builder should claim one project at a time by highest priority first, with a configurable concurrency limit.
- Claiming work must atomically change a project from `APPROVED` to `BUILDING`.
- For MVP, default Builder concurrency should be `1`.
- If a build fails, prefer moving the project to `PAUSED` with `lastBuildError` populated.

### Step 6 — Builder Agent (v1)
- Only support `saas`, `directory`, and `website` project types for the first version.
- Claim work from `APPROVED` projects by priority with concurrency `1`.
- Allow a manual debug override by project slug for the first build iterations.
- Generate code into `projects/active/<slug>/build` so the studio repo remains the control plane.
- Use a single Next.js template family with small variants instead of free-form app generation.
- Validate each generated app by running dependency install and production build.
- On success, move the project to `PAUSED` for human review of the generated MVP.
- On failure, move the project to `PAUSED` and write `lastBuildError` plus a build report.

Current status:
- In progress.

### Step 6 — Board of Agents (Phase 2)
- Implement `studio/agents/board/` with CEO, CMO, COO, CFO personas
- Each persona has a **constrained system prompt** — it only reasons within its domain
- `deliberation.ts` synthesizes the 4 views and outputs a `BoardDecisionRecord`
- Insert Board as a gate: Evaluator → Board → Planner
- Pipeline only continues to Planner for `GO` verdicts
- Wire `run-board.ts` script + `board.yml` GitHub Action

### Step 7 — Observatory Agent (Phase 3)
- Implement `studio/agents/observatory/`
- Score each agent's output quality weekly (signal/noise, uniqueness, outcome correlation)
- Version all prompts and track which versions produce better GO→LIVE conversion
- Generate `ObservatoryReport` with drift alerts and improvement suggestions
- **Human approval required** before any prompt change is applied
- Wire `run-observatory.ts` script + `observatory.yml` GitHub Action

---

## 10) Quality guardrails
- Every agent output must validate against its JSON schema.
- Prefer deterministic templates over “free-form” generation.
- Log minimally; store raw outputs for debugging.
- **Human review must gate building** until Board and Builder are implemented. `PLANNED` projects should not auto-progress into build execution.
- **Builder v1 must stay narrow** — it should scaffold only a small set of supported web project types and fail clearly on unsupported plans.
- **Builder output must be isolated per project** in `projects/active/<slug>/build` so winners can be spun out into their own repos later.
- **Board agents must be grounded in DB data** — no persona reasoning without real numbers (actual scores, benchmarks, prior outcomes). Without this, the board is just expensive creative writing.
- **Observatory never self-modifies** — it surfaces suggestions only. A human must approve and apply any prompt changes. Autonomous self-modification is explicitly out of scope.
- **Board personas must be constrained** — each persona's system prompt is scoped to its domain only. If all four agents can see all dimensions, they converge to agreement (LLM confirmation bias × 4).
---

## 11) Definition of done (Phase 1)
You are done with Phase 1 when:
- The nightly pipeline runs on schedule
- New ideas appear daily
- They get scored
- A few become planned projects with a clear MVP + test plan
- You can review planned projects in the dashboard
- You can explicitly approve or kill projects before any build step begins

---

## 12) Strategy docs (read before building)

| Doc | What it covers |
|-----|---------------|
| [`strategy/AI_STRATEGY.md`](./strategy/AI_STRATEGY.md) | LLM approach, GitHub Models, `lib/llm.ts`, zero API cost |
| [`strategy/TOOLS_AND_INTEGRATIONS.md`](./strategy/TOOLS_AND_INTEGRATIONS.md) | Every free tool, when to use it, env vars |
| [`strategy/VALIDATION_STRATEGY.md`](./strategy/VALIDATION_STRATEGY.md) | How to validate ideas without Reddit risk |
| [`strategy/SPRINT_0_CHECKLIST.md`](./strategy/SPRINT_0_CHECKLIST.md) | Accounts + admin steps before writing any code |
| [`strategy/AGENT_CONTRACTS.md`](./strategy/AGENT_CONTRACTS.md) | JSON schemas for all agent I/O |
| [`strategy/RUNBOOK.md`](./strategy/RUNBOOK.md) | How to operate the studio day-to-day |


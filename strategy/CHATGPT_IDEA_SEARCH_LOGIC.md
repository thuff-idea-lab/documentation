# SCOUT_AGENT_REFACTOR_PROMPT.md

You are refactoring the Venture Studio Scout Agent.

Your job is NOT to find interesting posts, popular posts, or trending topics.

Your job IS to find:
- repeated pain
- clear user segments
- obvious manual workflows
- buying/research confusion
- weak or expensive incumbents
- opportunities that can become a realistic internet business

The Scout Agent should behave like an opportunity researcher, not a content summarizer.

---

## Core Objective

Find 5–20 high-quality opportunity candidates per nightly run.

Each candidate must satisfy:
1. clear user segment
2. specific recurring pain
3. plausible small V1
4. realistic monetization path
5. good founder fit
6. not deep-specialist / regulated / trust-critical from day one

The Scout Agent should prioritize:
- ugly boring problems
- repetitive tasks
- manual workflows
- comparison confusion
- fragmented information
- expensive incumbents
- limited-feature incumbents
- obvious buying or operational friction

The Scout Agent should avoid:
- news
- generic discussions
- outage chatter
- broad trends
- “interesting technology”
- hype
- popularity-first content
- technically deep ideas outside founder execution range

---

## Exact Search Strategy

### Principle
Search pain-rich sources, not popularity-rich sources.

### Tier 1 sources (use every run)

#### 1. Reddit
Use niche/problem-oriented subreddits and search for posts where users describe:
- what takes too long
- what they do manually
- what tool they wish existed
- what tool they hate
- what is too expensive / too bloated / too confusing

#### 2. Review aggregators
Use sources like:
- G2
- Capterra

Look for:
- missing features
- limited features
- expensive
- integrations issues
- scheduling issues
- limitations
- learning curve
- too complex

#### 3. Tool/app marketplaces
Use:
- Chrome Web Store
- other plugin/tool marketplaces where relevant

Look for:
- proof users already install helpers for a workflow
- opportunities to simplify or narrow scope
- evidence that a category exists but products are bloated, ugly, or fragmented

### Tier 2 sources (add after refactor)
These are recommended additions:
- YouTube comments under niche product/problem videos
- Amazon reviews and Q&A for complex products or buying-confusion categories
- app store reviews (Apple App Store / Google Play) for workflow tools
- niche forums
- software alternatives/comparison sites
- Product Hunt comments only when focused on complaints / unmet needs, not hype
- public-data portals for directory opportunities
- search/autocomplete style question sources for recurring demand

Do not use generic front pages or trending feeds as primary discovery inputs.

---

## Exact Query Patterns

The Scout Agent should search using patterns like these.

### Reddit query patterns
- "I wish there was a tool for"
- "how do you handle"
- "what do you use for"
- "takes too long"
- "manually"
- "spreadsheet"
- "too expensive"
- "too complicated"
- "looking for a better way to"
- "hate using"
- "need to automate"
- "is there a tool for"
- "best software for"
- "how do you track"
- "how long does it take"

### Review-site query patterns
- "missing features"
- "limited features"
- "expensive"
- "integration issues"
- "limitations"
- "learning curve"
- "too complex"
- "wish it had"
- "not worth the price"
- "hard to use"

### Marketplace query patterns
Search for workflow/category keywords and inspect:
- titles
- descriptions
- reviews
- adjacent tools in the same category

Look for:
- lots of tools doing narrow slices of a workflow
- evidence users need helpers
- room for simpler / better-scoped products

### Public-data / directory query patterns
Search for:
- official csv export
- official api
- public records database
- searchable public registry
- inspection database
- licensing database
- government open data portal
- state directory
- county lookup
- downloadable dataset

Use this only when the source appears official or durable enough to support a real directory/data product.

### Calculator / decision-support query patterns
Search for problems involving:
- choosing between options
- estimating costs
- sizing / planning
- comparing complex products
- ROI / break-even / fit questions
- “which one do I need?”
- “how much should I budget?”
- “what’s the best option for my situation?”

### Automation / SaaS query patterns
Search for workflows involving:
- reminders
- follow-ups
- recurring reports
- repetitive content generation
- data entry
- syncing between tools
- notifications
- dashboards
- client/admin coordination
- quote / invoice / booking / task pipelines

---

## Exact Opportunity Extraction Rules

For every raw item, extract:

- user_segment
- pain_point
- current_workaround
- frequency
- source_type
- source_excerpt
- possible_business_models
- monetization_paths
- mvp_idea
- founder_fit_reason
- expansion_paths

Reject the item immediately if any of these cannot be clearly inferred:
- user_segment
- pain_point
- mvp_idea
- monetization_paths
- founder_fit_reason

Do NOT output generic title + summary records.

---

## Exact Qualification Rules

A candidate opportunity only survives if ALL are true:

### Rule 1 — Specific user segment
The user group must be narrow and identifiable.

Good:
- solo sellers managing listings
- service businesses handling invoices
- buyers comparing complex products
- teams stuck between disconnected tools
- operators doing repetitive reporting
- users researching fragmented markets

Bad:
- everyone
- businesses
- consumers
- creators
- developers
- internet users

### Rule 2 — Clear recurring pain
The pain must be specific, actionable, and repeated.

Good:
- this task takes too long
- this is done manually every week
- current tools are bloated / expensive / missing key functionality
- the buying process is confusing
- data is fragmented and hard to compare

Bad:
- opinions
- general discussion
- trend commentary
- “is anyone else seeing this”
- one-time issues

### Rule 3 — Plausible small V1
A small, useful first version must be imaginable.

Possible V1 shapes:
- niche SaaS
- automation
- dashboard
- comparison/research product
- affiliate site
- directory/data layer
- AI-assisted workflow helper
- search/filter/discovery tool
- template/data/prompt product
- media/content asset with monetization path

### Rule 4 — Monetization
At least one clear monetization path must exist:
- subscription
- affiliate
- ads
- lead gen
- digital product
- sponsorship
- data sales
- premium reports
- software-enabled services

### Rule 5 — Founder fit
The idea must fit a strong product-minded software builder.

Favor ideas that reward:
- product scoping
- UX
- workflow design
- data organization
- automation
- APIs
- dashboards
- search/filter UX
- comparison/research logic
- content leverage

Reject or heavily penalize ideas whose core value depends on:
- cryptography
- security expertise
- heavy regulation/compliance
- enterprise trust from day one
- research-grade AI
- core infrastructure engineering
- novel quant/scientific edge
- large-team ops before launch

---

## Exact Scoring

Use a 100-point scale.

### Positive
- pain_clarity: 20
- user_segment_clarity: 15
- monetization_potential: 15
- mvp_feasibility: 15
- repeatability: 10
- distribution_clarity: 10
- expansion_potential: 5
- product_leverage: 5
- founder_fit: 5

### Negative penalties
- too_technical_or_specialist_heavy: -20
- regulated_or_trust_critical: -20
- no_clear_buyer_or_revenue: -15
- too_broad_or_vague: -15
- requires_large_team_execution: -15
- generic_discussion_or_news: -25

### Thresholds
- 80+: PROMOTE
- 65–79: WATCH
- <65: REJECT

---

## Multi-signal requirement

Prefer opportunities that appear in more than one place.

Examples:
- Reddit pain + review complaints
- review complaints + marketplace tools
- buying confusion + multiple comparison questions
- repeated posts around the same workflow problem

A single strong post can be WATCH.
Multiple corroborating signals can become PROMOTE.

---

## Exact Output Format

Return JSON only.

{
  "opportunity_name": "string",
  "user_segment": "string",
  "pain_point": "string",
  "current_workaround": "string",
  "frequency": "string",
  "source_type": "string",
  "source_excerpt": "string",
  "possible_business_models": ["string"],
  "monetization_paths": ["string"],
  "mvp_idea": "string",
  "founder_fit_reason": ["string"],
  "expansion_paths": ["string"],
  "score_breakdown": {
    "pain_clarity": 0,
    "user_segment_clarity": 0,
    "monetization_potential": 0,
    "mvp_feasibility": 0,
    "repeatability": 0,
    "distribution_clarity": 0,
    "expansion_potential": 0,
    "product_leverage": 0,
    "founder_fit": 0
  },
  "penalties": [
    {
      "reason": "string",
      "points": 0
    }
  ],
  "total_score": 0,
  "status": "PROMOTE | WATCH | REJECT"
}

---

## Critical instruction

Do not optimize toward example industries.
Generalize from the pattern:

clear user segment
+ repeated pain
+ small V1
+ monetization
+ founder fit

The Scout Agent is an opportunity extraction system, not a trend summarizer.
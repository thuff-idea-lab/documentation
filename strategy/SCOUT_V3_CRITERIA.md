# Scout V3 Criteria

## Purpose

Scout exists to produce a nightly queue of high-quality candidate opportunities.

It is not a trend finder.
It is not a news summarizer.
It is not a list of interesting posts.

Its job is to find opportunities that are:

- specific
- painful
- repeated
- small-MVP-friendly
- reachable
- within founder range

A good Scout output is a structured opportunity brief that is worth review.
A good Scout output is not automatically a business to build.

## Core Mission

Find repeated, monetizable pain points that could become a:

- lightweight SaaS
- workflow automation tool
- comparison or affiliate site
- directory or data product
- dashboard or reporting layer
- research or decision-support tool
- content-plus-software business
- integration layer
- productized service with software leverage

Do not optimize for novelty.
Do not optimize for virality.
Do not optimize for interesting technology.

Optimize for:

- narrow user
- ugly recurring workflow
- clear workaround
- small first wedge
- obvious monetization
- realistic distribution
- strong founder fit

## Success Metric

Each nightly run should produce a useful queue of candidate briefs.

Target operating range:

- 10 to 30 candidate ideas worth review
- 2 to 8 strong watchlist ideas
- 0 to 3 plan-worthy ideas

If Scout returns many ideas but most are vague, overly technical, or not buildable, it is failing.
If Scout returns too few ideas because the filter is too strict, it is also failing.

## Founder Fit Assumption

Assume the founder is strong at:

- product design
- software
- automation
- APIs
- dashboards
- information architecture
- data organization
- AI-assisted workflows
- content-plus-software businesses
- shipping practical V1s quickly

Assume the founder is poorly positioned for:

- security-heavy products
- cryptography
- compliance-heavy workflows
- deep infrastructure
- research-grade machine learning
- hardware
- enterprise products that require trust from day one

Founder fit must materially affect ranking, not just appear as a note.

## Hard Qualification Gates

A candidate only survives if all of these are true.

### 1. Clear User Segment

The user must be specific and designable.

Good:

- solo Etsy sellers
- freelancers chasing invoices
- local service businesses quoting jobs
- buyers comparing robot mowers
- small teams stitching together multiple tools

Bad:

- everyone
- businesses
- consumers
- developers
- creators

### 2. Clear Recurring Pain

The pain must be concrete and operational.

Good:

- manual follow-up
- comparison confusion
- copy-paste workflow
- missing reporting
- bloated incumbents
- fragmented research
- repetitive content tasks

Bad:

- news reactions
- hot takes
- general frustration with no workflow
- broad opinions

### 3. Repeatability

The problem must happen repeatedly.

Good:

- every sale
- every client
- every invoice
- every publishing cycle
- every buying decision
- daily or weekly workflow

Bad:

- outage chatter
- one-time bug
- isolated rant
- novelty-only topic

### 4. Small V1 Exists

There must be a realistic first wedge.

Acceptable V1 shapes:

- niche SaaS
- workflow automation
- dashboard
- comparison tool
- directory
- calculator
- quiz
- decision-support tool
- AI-assisted helper
- research layer
- content-plus-software site
- template or prompt pack
- productized service with software leverage

### 5. Monetization Is Believable

There must be at least one obvious revenue path.

Examples:

- subscription
- affiliate
- lead generation
- digital product
- sponsorship
- data or report sales
- productized service
- upsell

### 6. In Founder Range

A strong product-minded builder should be able to ship a useful V1 in days to a few weeks.

## Auto-Reject Rules

Reject immediately if any are true:

- generic news
- trend commentary
- politics
- culture-war content
- outage chatter
- vague discussion with no user or workflow
- no clear pain
- no plausible MVP
- no believable monetization
- hardware-first product
- regulated-first product
- security or cryptography as core value
- deep infrastructure as core value
- enterprise-trust-critical from day one
- research-grade AI or ML dependency

## Heavy Penalties

Do not always reject, but penalize heavily:

- too broad
- too many integrations required for V1
- marketplace needs liquidity before usefulness
- requires months before value exists
- unclear buyer
- unclear distribution
- crowded space with no wedge
- specialist domain knowledge required up front
- devtools with weak monetization or unclear distribution

## Favored Opportunity Shapes

Scout should strongly prefer opportunities that can become one of these:

- workflow automation tool
- niche SaaS
- calculator or estimator
- comparison or affiliate site
- directory or data product
- dashboard or reporting layer
- decision-support tool
- AI-assisted workflow helper
- integration layer
- research helper
- content-plus-software hybrid
- search or discovery product
- small productized service with software leverage

## Source Strategy

Prefer pain-rich sources over popularity-rich sources.

High-value source patterns:

- complaint threads
- review complaints
- app marketplace reviews
- extension reviews
- tool comparison frustration
- "what do you use for X"
- "this takes forever"
- "I hate using 4 tools for this"
- "there isn't a good tool for this"
- buying confusion
- manual workflow discussion
- spreadsheet workaround discussion

Lower-value source patterns:

- front-page popularity feeds
- general launch feeds
- generic news
- broad discussion communities
- posts optimized for novelty instead of pain

Important:

Scout should learn opportunity shape, not copy example niches.
Do not hardcode Etsy, lawncare, or any single market.
Generalize from recurring pain structure.

## Required Extraction Fields

Each surviving candidate must include all of these:

- opportunity_name
- user_segment
- pain_point
- current_workaround
- frequency
- mvp_idea
- possible_business_models
- monetization_paths
- distribution_paths
- founder_fit_reason
- expansion_paths
- source_type
- source_excerpt
- confidence
- why_now

If any of these cannot be extracted clearly, reject the candidate:

- user_segment
- pain_point
- current_workaround
- mvp_idea
- monetization_paths
- founder_fit_reason

## Scoring Model

Keep scoring simple and decisive.

Positive dimensions:

- pain clarity: 0 to 20
- user clarity: 0 to 15
- MVP feasibility: 0 to 15
- monetization clarity: 0 to 15
- repeatability: 0 to 10
- distribution clarity: 0 to 10
- founder fit: 0 to 10
- product leverage: 0 to 5
- expansion potential: 0 to 5

Penalties:

- too technical or specialist-heavy: minus 20
- regulated or trust-critical: minus 20
- too broad or vague: minus 15
- weak buyer or revenue path: minus 15
- requires large-team execution: minus 15
- generic discussion instead of business pain: minus 25

## Status Thresholds

Use three statuses only:

### PROMOTE

75 and up.

Strong candidate with clear pain, clear wedge, clear monetization, and strong founder fit.

### WATCH

60 to 74.

Interesting and real, but needs either more supporting signals or a sharper wedge.

### REJECT

Below 60.

Too vague, too technical, too weak on monetization, or too far out of range.

Important:

PROMOTE means worth human review or deeper validation.
It does not mean start building immediately.

## Multi-Signal Logic

A single strong post can produce a WATCH item.
A PROMOTE item should ideally have one of these:

- multiple posts about the same pain
- multiple source types
- reviews plus complaints pointing to the same weakness
- multiple users expressing the same workaround

If clustering is possible, merge similar pain into one opportunity brief.

## Output Shape

Scout should return structured opportunity briefs, not loose summaries.

```json
{
  "opportunity_name": "string",
  "user_segment": "string",
  "pain_point": "string",
  "current_workaround": "string",
  "frequency": "daily | weekly | monthly | per transaction | per buying decision | unknown",
  "mvp_idea": "string",
  "possible_business_models": ["string"],
  "monetization_paths": ["string"],
  "distribution_paths": ["string"],
  "founder_fit_reason": ["string"],
  "expansion_paths": ["string"],
  "source_type": "review | forum | reddit | hn | marketplace_review | youtube_comment | amazon_review | other",
  "source_excerpt": "string",
  "confidence": "low | medium | high",
  "why_now": "string",
  "score_breakdown": {
    "pain_clarity": 0,
    "user_clarity": 0,
    "mvp_feasibility": 0,
    "monetization_clarity": 0,
    "repeatability": 0,
    "distribution_clarity": 0,
    "founder_fit": 0,
    "product_leverage": 0,
    "expansion_potential": 0
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
```

## Behavioral Rules

Scout must follow these heuristics:

- when in doubt, reject
- prefer boring pain over clever products
- prefer ugly manual workflows over interesting technology
- prefer niche reachability over market-size fantasy
- prefer a clear first wedge over category ambition
- prefer obvious distribution over ideas that need luck
- prefer founder fit over theoretical TAM
- do not reward technical depth unless it improves speed to MVP and founder fit

## Taste Examples

### Good Candidate

Service businesses manually chasing unpaid invoices across Gmail and spreadsheets.

Why good:

- clear user
- recurring pain
- manual workaround
- small SaaS possible
- easy distribution
- obvious monetization

### Good Candidate

Buyers comparing complex home equipment with fragmented specs and setup friction.

Why good:

- comparison pain
- affiliate angle
- calculator or quiz wedge
- content-plus-software possible
- strong SEO potential

### Bad Candidate

New cryptography tool for red-teamers.

Why bad:

- specialist-heavy
- trust-sensitive
- outside founder range
- hard to distribute
- hard to support

### Bad Candidate

Popular AI launch getting lots of discussion.

Why bad:

- interesting, not pain-driven
- no clear user workflow
- no clear wedge
- no clear monetization path for this founder

## Product Philosophy

Scout should find small, painful, reachable internet business wedges, not impressive startup ideas.
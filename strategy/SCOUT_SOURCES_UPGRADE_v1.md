# Scout Source Refactor Spec

## Goal

Refactor the Scout Agent so it finds **better internet business opportunities**.

Right now the scout is too biased toward:
- finance/admin workflows
- bookkeeping-style pain
- operational B2B software
- startup/maker chatter
- ideas that are valid pains but annoying or heavy to build

We want the scout to find a broader and more valuable mix of opportunities, especially ideas that are:
- easier to ship as a V1
- lower effort
- more elegant
- more leverageable
- more likely to become:
  - calculators
  - estimators
  - quizzes
  - comparison sites
  - directories
  - searchable databases
  - affiliate/research sites
  - creator/seller helpers
  - lightweight SaaS
  - decision-support tools
  - data products
  - search/filter/discovery products

---

## Core Problem With Current Scout

The current source mix is still too close to:

> where internet/startup/business people talk

But it should be closer to:

> where people reveal pain, complaints, buying confusion, repeated manual work, weak incumbents, and missing discovery layers

Current sources are over-weighting:
- Hacker News
- Product Hunt
- dev.to
- Reddit subreddits that skew toward SaaS, consulting, bookkeeping, and operations software

This produces too many ideas like:
- reconciliation tools
- dispute management
- transaction history tools
- tax automation
- proposal/document management
- consultant resource scheduling

Those ideas are not always bad, but they are too narrow, too ops-heavy, and too often more painful to build than the kinds of internet businesses we want.

---

## New Source Strategy

Scout should be redesigned around **source lanes**, not just individual feeds.

### Lane 1 — Pain Communities
These reveal:
- repetitive tasks
- workflow pain
- creator pain
- seller pain
- service-business pain
- manual work
- ugly workarounds

Sources:
- Reddit intent search
- niche forums later

### Lane 2 — Complaint Ecosystems
These reveal:
- weak incumbents
- missing features
- expensive software
- bloated tools
- usability issues
- opportunities for narrower better-scoped products

Sources:
- G2
- Capterra
- app store reviews later
- Chrome Web Store reviews later

### Lane 3 — Buying Confusion
These reveal:
- comparison pain
- research friction
- setup confusion
- "which one do I need"
- decision-support opportunities
- affiliate opportunities
- calculator / quiz / estimator opportunities

Sources:
- Amazon reviews
- Amazon Q&A
- YouTube comments later
- Reddit buying/product communities

### Lane 4 — Discovery / Data Gaps
These reveal:
- directory opportunities
- searchable database opportunities
- lead-gen opportunities
- public-data opportunities
- fragmented local/service categories

Sources:
- public/open data portals
- official registries
- inspection databases
- licensing databases
- government CSV exports / APIs
- official searchable public records

### Lane 5 — Startup/Product Ecosystem (Secondary Only)
These are not primary sources of opportunity.
They are useful for:
- validation
- category awareness
- checking if something already exists
- seeing saturation

Sources:
- Hacker News Ask only, filtered
- Product Hunt as secondary only

These should not dominate scout results.

---

## What To Remove / Downgrade

### Remove from core scout:
- dev.to RSS

Reason:
- too content-driven
- too developer-centric
- weak opportunity signal
- too far from monetizable pain extraction

### Downgrade:
- Product Hunt
- Hacker News Show
- generic Hacker News discovery

Reason:
- too much launch chatter
- too much hype
- too much “interesting”
- too little repeated monetizable pain
- too many ideas outside realistic scope

### Keep only in limited/secondary form:
- Ask HN
- maybe Show HN only when filtered for complaints / obvious gaps

---

## What To Keep

### Keep Reddit intent search as a core source
Reddit is still one of the best core sources because people naturally reveal:
- pain
- frustration
- ugly manual work
- tool complaints
- buying confusion
- repeated questions

But it needs better subreddit mix, better query mix, and stronger prefiltering.

---

## Reddit Refactor Requirements

### Problem with current Reddit setup
The current subreddit set is too biased toward:
- business admin
- finance/admin workflows
- internal ops software
- founder chatter

The current query set is too biased toward:
- software
- automation
- spreadsheets
- business process pain

This makes the scout overproduce workflow SaaS / admin software.

### New Reddit design
Reddit should be organized into **opportunity buckets**.

#### Bucket 1 — Workflow Pain
Keep, but do not let this dominate.

Goal:
- narrow workflow problems
- repetitive admin pain
- automation opportunities
- creator/seller workflow pain

#### Bucket 2 — Buying Confusion
Add and prioritize more.

Goal:
- comparison businesses
- calculators
- estimators
- affiliate opportunities
- product quizzes
- decision-support tools

#### Bucket 3 — Discovery / Directory / Fragmented Information
Add and prioritize more.

Goal:
- directories
- public-data products
- searchable databases
- local/category lead-gen businesses
- fragmented provider comparisons

#### Bucket 4 — Creator / Seller Tasks
Keep and strengthen.

Goal:
- AI helpers
- content workflow products
- listing optimizers
- creator productivity tools
- seller workflow tools
- templates, prompt packs, small SaaS

---

## Reddit Query Strategy

Do not use one flat query list.
Split query families by opportunity type.

### Workflow queries
These are still useful, but should only be one lane.

Examples:
- I wish there was a tool for
- how do you handle
- what do you use for
- takes too long
- I do this manually
- looking for a better way to
- is there a tool for
- need to automate

### Buying confusion queries
These need to be added heavily.

Examples:
- which one should I buy
- best for my situation
- what do I need for
- comparison
- worth it
- how much does it cost
- calculator
- estimator
- for beginners
- for small space
- for large yard

### Discovery / directory queries
These are currently underrepresented and should be added.

Examples:
- how do I find
- where can I find
- directory
- database
- list of
- public records
- official registry
- search by zip
- compare providers
- licensed in my area

### Creator / seller queries
These should be explicit, not incidental.

Examples:
- writing descriptions takes forever
- how do you make titles
- how do you come up with keywords
- how do you organize content
- how do you repurpose
- how long does it take you to
- I hate doing captions
- I still do this manually

---

## Reddit Subreddit Strategy

Do not use one flat subreddit pool.
Use subreddit buckets aligned with the opportunity buckets.

### Workflow subreddits
Keep some of these:
- r/entrepreneur
- r/smallbusiness
- r/freelance
- r/microsaas
- r/consulting

But reduce their dominance.

### Buying / consumer research subreddits
Add / emphasize:
- r/BuyItForLife
- r/HomeImprovement
- r/Tools
- r/Appliances
- r/lawncare
- r/robotmowers

Goal:
- buying confusion
- setup questions
- comparison opportunities
- calculator / quiz / affiliate ideas

### Discovery / fragmented-market subreddits
Add / emphasize:
- r/RealEstate
- r/realestateinvesting
- r/Parents
- r/Homeowners
- some category-specific service communities over time

Goal:
- directory ideas
- fragmented provider categories
- searchable database opportunities
- local/service discovery pain

### Creator / seller subreddits
Keep / strengthen:
- r/EtsySellers
- r/youtubers
- r/ecommerce
- r/Flipping
- r/printondemand
- possibly later: r/shopify, r/AmazonSeller, r/blogging, r/podcasting

Goal:
- seller workflows
- creator workflows
- content pain
- repetitive text/image/video tasks
- helper tools
- prompt/template products

---

## Source Additions To Build

Build these new source adapters in roughly this order:

### Priority 1 — Review / Complaint Lane
Add:
- G2
- Capterra

Why:
These are extremely strong for finding:
- missing features
- limited features
- expensive incumbents
- bloated software
- opportunities to build better-scoped products

This is one of the highest-value additions.

### Priority 2 — Public Data / Registry Lane
Add:
- government open data portals
- official registries
- licensing databases
- inspection databases
- public searchable records
- CSV exports / APIs

Why:
This is the highest-value lane for:
- directories
- searchable databases
- lead-gen products
- data products

This lane is currently missing and should be treated as first-class.

### Priority 3 — Amazon Buying-Confusion Lane
Add:
- Amazon product pages
- reviews
- Q&A

Why:
This is extremely good for:
- comparison sites
- quizzes
- calculators
- affiliate businesses
- decision-support tools

This lane helps surface easier-to-produce ideas.

### Priority 4 — Chrome Web Store
Add:
- extension listing pages
- descriptions
- reviews where possible

Why:
This is good for:
- narrow workflow wedges
- seller/creator tools
- helper products
- opportunities where users already install small workflow assistants

### Priority 5 — YouTube Comments
Add later, not first.

Why:
Useful for:
- repeated questions
- buying confusion
- creator pain
- product confusion
- comparison opportunities

But parsing and noise handling is messier.

---

## New Scout Philosophy

The Scout Agent should stop optimizing for:

- interesting
- popular
- heavily discussed
- startup-y
- technically impressive

It should optimize for:

- repeated pain
- weak incumbent
- manual workaround
- fragmented information
- comparison confusion
- obvious small V1
- realistic monetization
- easy-to-produce first version

---

## Easy-To-Produce Bias

Scout should explicitly prefer opportunities where the likely V1 looks like:

- calculator
- estimator
- quiz
- comparison site
- directory
- searchable database
- niche affiliate site
- AI-assisted helper
- workflow assistant
- dashboard
- template product
- prompt/data pack
- search/filter/discovery layer

Scout should explicitly penalize opportunities where the likely V1 sounds like:

- compliance system
- reconciliation engine
- dispute workflow
- enterprise back-office platform
- tax-heavy system
- security/infrastructure product
- deep finance system
- enterprise admin software

This is one of the most important behavioral changes.

---

## Prefilter Heuristics

Before sending anything to the LLM opportunity extraction stage, add lightweight heuristics.

### Easy-build boosts
Boost candidates containing signals like:
- calculator
- estimator
- quiz
- compare
- directory
- database
- finder
- template
- guide
- planner
- generator
- dashboard
- keyword
- listing
- caption
- title
- ROI
- cost
- fit
- sizing

### Hard-build penalties
Penalize candidates containing signals like:
- tax
- compliance
- reconciliation
- dispute
- payroll
- legal
- security
- encryption
- infrastructure
- observability
- trading bot
- quant
- enterprise
- ERP
- banking
- regulatory
- audit

This does not need to be perfect.
It is a prefilter bias to improve the candidate pool.

---

## Classifier Requirement

Add a lightweight classifier before LLM extraction.

Classify each candidate into one of:
- workflow_pain
- buying_confusion
- discovery_gap
- creator_task
- tool_complaint
- generic_chatter

Only send useful classes forward.
Reject or heavily penalize:
- generic_chatter
- broad discussion
- rant-only content
- outage chatter
- trend chatter

---

## Balanced Sampling Requirement

Do not let the scout randomly over-sample one type of source or one type of pain.

Instead:
- sample intentionally across source lanes
- sample intentionally across opportunity buckets
- sample intentionally across subreddit/query groups

The output of each nightly run should be a balanced mix of:
- workflow opportunities
- buying confusion opportunities
- discovery/directory opportunities
- creator/seller opportunities
- complaint-driven product opportunities

This is critical.
The scout should not produce 80 percent finance/admin workflow ideas.

---

## Recommended Source Weighting

Use approximate source weights like this:

### Primary
- Reddit intent search
- G2 / Capterra
- public-data / registry discovery
- Amazon reviews / Q&A

### Secondary
- Chrome Web Store
- YouTube comments
- Hacker News Ask

### Low priority / validation only
- Product Hunt

### Remove
- dev.to

---

## Concrete Refactor Tasks

Refactor Scout to do the following:

1. Remove dev.to from core scout ingestion.
2. Downgrade Product Hunt to secondary/validation use only.
3. Reduce Hacker News to Ask HN only or heavily filtered use.
4. Keep Reddit as a core source, but refactor it into balanced opportunity buckets.
5. Add source adapters for:
   - G2 / Capterra
   - public-data / registry discovery
   - Amazon reviews / Q&A
6. Add easy-build boosts and hard-build penalties.
7. Add a pre-LLM classifier.
8. Enforce balanced sampling across opportunity types.
9. Bias the scout toward easier-to-produce internet businesses.
10. Preserve source metadata so later stages know:
   - source lane
   - opportunity bucket
   - intent class
   - easy-build score
   - hard-build penalty
   - prefilter score

---

## Desired Outcome

After this refactor, scout results should shift away from:
- finance/admin sludge
- enterprise-ish back-office tools
- generic startup chatter
- technically deep ideas

And toward:
- better-scoped products
- easier V1s
- more elegant internet businesses
- more directories/data ideas
- more comparison/calculator opportunities
- more affiliate/research products
- more creator/seller helpers
- more discovery/search/filter businesses
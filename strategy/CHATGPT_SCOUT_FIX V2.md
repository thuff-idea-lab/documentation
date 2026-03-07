import axios from 'axios';
import { logger } from '../../../lib/logger';
import type { RawPost } from '../types';

type OpportunityBucket = 'workflow' | 'buying' | 'discovery' | 'creator';
type PostIntent =
  | 'workflow_pain'
  | 'buying_confusion'
  | 'discovery_gap'
  | 'creator_task'
  | 'tool_complaint'
  | 'generic_chatter';

interface RedditSearchItem {
  data: {
    title: string;
    selftext: string;
    url: string;
    permalink: string;
    subreddit_name_prefixed: string;
    score: number;
    num_comments: number;
  };
}

interface EnrichedRawPost extends RawPost {
  bucket: OpportunityBucket;
  intent: PostIntent;
  easyBuildScore: number;
  hardBuildPenalty: number;
  prefilterScore: number;
  rejectReason?: string;
}

const QUERY_BUCKETS: Record<OpportunityBucket, string[]> = {
  workflow: [
    'I wish there was a tool for',
    'how do you handle',
    'what do you use for',
    'takes too long',
    'I do this manually',
    'looking for a better way to',
    'is there a tool for',
    'need to automate',
  ],
  buying: [
    'which one should I buy',
    'best for my situation',
    'what do I need for',
    'comparison',
    'worth it',
    'how much does it cost',
    'calculator',
    'estimator',
    'for beginners',
    'for small space',
    'for large yard',
  ],
  discovery: [
    'how do I find',
    'where can I find',
    'directory',
    'database',
    'list of',
    'public records',
    'official registry',
    'search by zip',
    'compare providers',
    'licensed in my area',
  ],
  creator: [
    'writing descriptions takes forever',
    'how do you make titles',
    'how do you come up with keywords',
    'how do you organize content',
    'how do you repurpose',
    'how long does it take you to',
    'I hate doing captions',
    'I still do this manually',
  ],
};

const SUBREDDIT_BUCKETS: Record<OpportunityBucket, string[]> = {
  workflow: [
    'r/entrepreneur',
    'r/smallbusiness',
    'r/freelance',
    'r/microsaas',
    'r/consulting',
  ],
  buying: [
    'r/BuyItForLife',
    'r/HomeImprovement',
    'r/Tools',
    'r/Appliances',
    'r/lawncare',
    'r/robotmowers',
  ],
  discovery: [
    'r/RealEstate',
    'r/realestateinvesting',
    'r/Parents',
    'r/Homeowners',
    'r/smallbusiness',
  ],
  creator: [
    'r/EtsySellers',
    'r/youtubers',
    'r/ecommerce',
    'r/Flipping',
    'r/printondemand',
  ],
};

const FETCH_HEADERS = {
  'User-Agent':
    'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/122.0.0.0 Safari/537.36',
  Accept: 'application/json',
};

const REJECT_PATTERNS = [
  /outage|down for anyone|not working/i,
  /politics|election|government/i,
  /^introducing myself|new (here|member)/i,
  /just venting/i,
  /ask hn|show hn/i,
  /anyone else/i,
  /am i the only one/i,
];

const EASY_BUILD_PATTERNS = [
  /calculator|estimator|quiz|directory|database|search|compare/i,
  /template|prompt|generator|dashboard|tracker/i,
  /guide|finder|checklist|planner/i,
  /keyword|listing|title|description|caption/i,
  /cost|price|roi|sizing|fit/i,
];

const HARD_BUILD_PATTERNS = [
  /tax|compliance|reconciliation|dispute|payroll|legal/i,
  /security|encryption|infrastructure|observability/i,
  /trading bot|quant|hedge fund/i,
  /enterprise|erp|banking|accounting/i,
  /claims processing|audit trail|regulatory/i,
];

const GENERIC_CHATTER_PATTERNS = [
  /anyone else/i,
  /what do you think/i,
  /rant/i,
  /vent/i,
  /unpopular opinion/i,
  /career advice/i,
  /burned out/i,
];

function shuffle<T>(arr: T[]): T[] {
  return [...arr].sort(() => Math.random() - 0.5);
}

function isRejected(title: string, body?: string): string | null {
  const text = `${title} ${body ?? ''}`;
  for (const pattern of REJECT_PATTERNS) {
    if (pattern.test(text)) return `Rejected by pattern: ${pattern}`;
  }
  return null;
}

function countPatternMatches(text: string, patterns: RegExp[]): number {
  return patterns.reduce((count, pattern) => count + (pattern.test(text) ? 1 : 0), 0);
}

function classifyIntent(title: string, body?: string): PostIntent {
  const text = `${title} ${body ?? ''}`.toLowerCase();

  if (
    /which one|best for|worth it|comparison|what do i need|how much does it cost|estimator|calculator/.test(
      text
    )
  ) {
    return 'buying_confusion';
  }

  if (
    /directory|database|public records|official registry|where can i find|search by zip|licensed in my area/.test(
      text
    )
  ) {
    return 'discovery_gap';
  }

  if (
    /descriptions|titles|keywords|captions|repurpose|content|thumbnail|listing/.test(text)
  ) {
    return 'creator_task';
  }

  if (
    /too expensive|too complicated|missing feature|limitations|wish it had|hate using/.test(text)
  ) {
    return 'tool_complaint';
  }

  if (
    /manually|spreadsheet|track|follow up|automate|takes too long|workflow|handle/.test(text)
  ) {
    return 'workflow_pain';
  }

  return 'generic_chatter';
}

function scoreEasyBuild(title: string, body?: string): number {
  const text = `${title} ${body ?? ''}`;
  return countPatternMatches(text, EASY_BUILD_PATTERNS) * 8;
}

function scoreHardBuildPenalty(title: string, body?: string): number {
  const text = `${title} ${body ?? ''}`;
  return countPatternMatches(text, HARD_BUILD_PATTERNS) * 10;
}

function scoreEngagement(score: number, comments: number): number {
  // Small engagement boost, but keep it secondary to opportunity quality.
  const scoreBoost = Math.min(score / 20, 5);
  const commentBoost = Math.min(comments / 10, 5);
  return scoreBoost + commentBoost;
}

function scoreIntent(intent: PostIntent): number {
  switch (intent) {
    case 'buying_confusion':
      return 12;
    case 'discovery_gap':
      return 12;
    case 'creator_task':
      return 10;
    case 'tool_complaint':
      return 8;
    case 'workflow_pain':
      return 6;
    case 'generic_chatter':
    default:
      return -20;
  }
}

function computePrefilterScore(
  intent: PostIntent,
  easyBuildScore: number,
  hardBuildPenalty: number,
  score: number,
  comments: number
): number {
  return scoreIntent(intent) + easyBuildScore - hardBuildPenalty + scoreEngagement(score, comments);
}

function shouldKeepPost(post: EnrichedRawPost): boolean {
  if (post.intent === 'generic_chatter') return false;
  if (post.hardBuildPenalty >= 20 && post.easyBuildScore < 8) return false;
  if (post.prefilterScore < 8) return false;
  return true;
}

async function searchReddit(
  subreddit: string,
  query: string
): Promise<RedditSearchItem[]> {
  const url = `https://www.reddit.com/${subreddit}/search.json?q=${encodeURIComponent(
    query
  )}&sort=new&restrict_sr=1&limit=10&t=month`;

  const response = await axios.get<{ data: { children: RedditSearchItem[] } }>(url, {
    headers: FETCH_HEADERS,
    timeout: 10000,
  });

  return response.data?.data?.children ?? [];
}

function buildEnrichedPost(
  bucket: OpportunityBucket,
  item: RedditSearchItem['data']
): EnrichedRawPost | null {
  const title = item.title ?? '';
  const body = item.selftext?.slice(0, 500);

  const rejectReason = isRejected(title, body);
  if (rejectReason) {
    return null;
  }

  const intent = classifyIntent(title, body);
  const easyBuildScore = scoreEasyBuild(title, body);
  const hardBuildPenalty = scoreHardBuildPenalty(title, body);
  const prefilterScore = computePrefilterScore(
    intent,
    easyBuildScore,
    hardBuildPenalty,
    item.score ?? 0,
    item.num_comments ?? 0
  );

  const post: EnrichedRawPost = {
    title,
    body,
    url: `https://www.reddit.com${item.permalink}`,
    platform: item.subreddit_name_prefixed,
    points: item.score,
    bucket,
    intent,
    easyBuildScore,
    hardBuildPenalty,
    prefilterScore,
  };

  if (!shouldKeepPost(post)) {
    return null;
  }

  return post;
}

function pickBalancedQueries(bucket: OpportunityBucket, count = 3): string[] {
  return shuffle(QUERY_BUCKETS[bucket]).slice(0, count);
}

function pickBalancedSubreddits(bucket: OpportunityBucket, count = 3): string[] {
  return shuffle(SUBREDDIT_BUCKETS[bucket]).slice(0, count);
}

export async function fetchRedditIntentSearch(): Promise<RawPost[]> {
  const posts: EnrichedRawPost[] = [];
  const seen = new Set<string>();

  const bucketPlan: OpportunityBucket[] = ['workflow', 'buying', 'discovery', 'creator'];

  for (const bucket of bucketPlan) {
    const queries = pickBalancedQueries(bucket, 3);
    const subreddits = pickBalancedSubreddits(bucket, 3);

    for (const subreddit of subreddits) {
      for (const query of queries) {
        try {
          const children = await searchReddit(subreddit, query);

          for (const child of children) {
            const item = child.data;
            if (!item.title || !item.permalink) continue;

            const postUrl = `https://www.reddit.com${item.permalink}`;
            if (seen.has(postUrl)) continue;

            const enriched = buildEnrichedPost(bucket, item);
            if (!enriched) continue;

            seen.add(postUrl);
            posts.push(enriched);
          }
        } catch (err: any) {
          logger.warn(
            'scout',
            `Reddit search failed [${bucket} | ${subreddit} | "${query}"]: ${
              err?.message ?? err
            }`
          );
        }

        await new Promise((r) => setTimeout(r, 500));
      }
    }
  }

  // Sort strongest prefilter candidates first before handing to LLM layer.
  posts.sort((a, b) => b.prefilterScore - a.prefilterScore);

  logger.info(
    'scout',
    `Reddit intent search: ${posts.length} filtered posts across balanced opportunity buckets`
  );

  // If your downstream pipeline only accepts RawPost, strip enriched fields here.
  // If possible, update the downstream types to preserve these fields for the LLM step.
  return posts.map((post) => ({
    title: post.title,
    body: post.body,
    url: post.url,
    platform: post.platform,
    points: post.points,
    // Optional: include metadata if RawPost supports arbitrary fields
    metadata: {
      bucket: post.bucket,
      intent: post.intent,
      easyBuildScore: post.easyBuildScore,
      hardBuildPenalty: post.hardBuildPenalty,
      prefilterScore: post.prefilterScore,
    },
  })) as RawPost[];
}
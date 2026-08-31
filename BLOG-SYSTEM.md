# The ABCD Desk

System design for the blog pipeline. Agreed 30 Aug 2026, not built yet.
Replaces the paid MonitoRSS Discord setup. Everything here is free.

Companion artifact (same content, laid out): https://claude.ai/code/artifact/47e50d9f-8d6c-43a5-9428-6f84246ea421

## The premise

Fetching RSS is fifty lines of Python. It is not the problem. Three other things
decide whether this earns anything:

1. **Pictures we are allowed to use.** Every layout leans on photographs and a
   store republishing another brand's lookbook shot is a real liability. Has to be
   solved structurally, not hoped around.
2. **Telling trendy from merely published.** A feed says something was posted, not
   that anyone cared.
3. **Volume is what gets you demoted.** Google's scaled content policy targets
   mass-produced pages made to rank, and a demotion takes the product pages with it.
   The failure mode is writing plenty, not writing little.

## The pipeline

Two gates, both Judy's. Everything above gate 1 runs unattended. Nothing below it
moves without her.

```
  Your feeds + keyword list          two files she edits, no app, no subscription
            |
  Fetch and dedupe                   every item ever seen is remembered
            |
  Score                              corroboration, recency, brand fit, product hook
            |                        <-- Search Console feedback returns here
  Shortlist, 5 to 8 a week           with the reason each one scored
            |
  GATE 1: she picks one or two       nothing is written until she has chosen
            |
  Brief, draft, assemble  <--------  image bank (hers only, no 3rd-party photos)
            |                        gallery, shop tags, meta title, alt text, sources
            |
  GATE 2: she reads it, fixes take   on the review board, images and all
            |
  Posted through her Shopify admin   browser session, no API token
            |
            +---> live post ---> Search Console ---> back into Score
```

The feedback edge is what makes this improve rather than repeat. Nothing before
publishing can tell you what people actually searched for and clicked.

## Scoring: what actually predicts a good post

"Trendiest", "most clickable" and "best for SEO" are three different questions and
only some can be answered before publishing.

| Signal | What it tells you | Cost | Verdict |
|---|---|---|---|
| Cross-feed corroboration | Same story in 3+ feeds inside 48h. Nobody coordinates that | Free | **The one that works** |
| Product hook | Touches a category she sells: tees, hoodies, crops, caps, cargo | Free | Strong, hers to tune |
| Brand-world match | Scores against her keyword file: techno, house, warehouse, flyer, rave, labels | Free | Strong, needs her input |
| Recency decay | Streetwear news is worthless at two weeks | Free | Trivial and necessary |
| Reddit mentions | Whether r/streetwear is actually talking, vs brands announcing | Free tier, needs key | Useful, some setup |
| Google Trends | Search interest over time | Free but unofficial | Fragile, breaks often |
| Keyword difficulty | Whether you could realistically rank | Ahrefs/Semrush ~$100/mo | Not worth it yet |
| Search Console | What people actually searched, clicked, stayed for | Free | **Best signal, only works after publishing** |

**The honest bit:** nothing on this list predicts which post makes someone buy a
hoodie. That is learned, not predicted, and takes six to ten live posts before
Search Console and Shopify analytics have anything to say. Any system claiming
otherwise before publishing is guessing confidently.

## Where it lives

Its own folder beside the theme repo, so blog machinery never clutters store code.
Plain text throughout, readable and deletable without an agent.

```
~/Documents/Github/abcd-blog/
├── feeds.yaml            the feeds she sends, grouped and weighted
├── keywords.yaml         the ABCD world: brands, scenes, words that matter
├── VOICE.md              her samples and rules. nothing drafts without it
├── beat.md               what we cover and, more usefully, what we don't
├── state/
│   ├── seen.json         every item ever fetched, so nothing repeats
│   └── published.json    what went live, when, which products it linked
├── images/               her image bank, shot by her or made by us
└── queue/
    └── 2026-09-02-slug/
        ├── brief.md      what happened, the angle, why it scored
        ├── draft.html    gallery and shop tags already in place
        ├── meta.json     title, excerpt, SEO description, tags, products
        ├── sources.md    every claim traced to the item it came from
        └── images/       chosen and cropped, alt text written
```

## The review board

Regenerated every run. One page: shortlist at the top with why each scored, drafted
posts below with images laid out as they will appear, source links beside each
claim. Same kind of page as the layout mockups. She scrolls, replies with which is
good and what is wrong with the words. No app, no login, nothing to pay for.

Optional: a free Discord webhook drops the daily shortlist into a channel, one line
each. Discord for noticing, review board for deciding. That is what MonitoRSS did,
for nothing.

## The rest of the kit

The software is the least of what an editorial role needs on day one.

**Only Judy can provide**
- **A voice file.** Real samples of how she writes. Everything downstream is built
  on it and it does not get faked. See [[no-copy-without-her-words]] in memory
- **An image bank.** One shooting session of flat-lays, details, rail shots,
  backdrops. Reusable across dozens of posts, legally hers. Single biggest unblock
- **A beat.** What ABCD has an opinion about and what it stays out of. Without it,
  every feed item looks equally relevant

**Handled in the pipeline**
- **No-repeat ledger.** Every story seen and every post published, so we never
  cover the same drop twice or contradict an old post
- **Source discipline.** Every factual claim traced to its feed item, links out
  rather than lifted text, no invented quotes. A store writing about other brands
  cannot be sloppy here
- **SEO plumbing.** Meta title and description, URL handle, excerpt, alt text on
  every image, one internal link to a product and one to an older post. The
  internal linking is most of what builds authority
- **Cadence cap.** Hard ceiling of roughly one post a week, enforced in code, not a
  suggestion. This is the guardrail against demotion
- **Measurement loop.** Search Console and Shopify analytics read monthly, fed back
  into scoring

## Blocked on, in order

1. **The image answer.** Own photography, made graphics in the ABCD language,
   product shots only, or a mix. Decides what a post can even be, so it comes first
2. **Her voice.** Instagram captions are the easiest source and already exist
3. **The feeds.** However they come out of the old setup, OPML or a list of URLs
4. **Cadence.** Recommend one a week. Lower is fine
5. **Discord or not.** Free either way, purely whether she wants the daily nudge

## Prerequisites not yet set up

- **Google Search Console** for abcd.gift. Free, and the measurement loop does not
  work without it. Needs domain verification
- **Reddit API key** if we want that signal. Free tier
- The **footer link** to the blog, agreed but not built. See NOTES.md for why
  `sections/footer.liquid` is the wrong file to touch

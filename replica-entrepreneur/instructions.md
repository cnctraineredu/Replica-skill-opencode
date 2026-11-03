# replica-entrepreneur

**Researches the app being cloned and reads what its real users say in public reviews** (App Store, Google Play, G2, Capterra, Trustpilot, Reddit, Hacker News, its own feature-request board), then ranks what they hate, what is missing and what is unsolved, turns it into a fix plan for the clone and a positioning angle you can sell. **Every quote is real, verbatim and linked.**

Tool: `scripts/reviews.py`

```bash
python3 scripts/reviews.py replica/reviews.csv --out replica/feedback.md
```

---

## The Rules (Non-Negotiable)

- **Never fabricate.** No invented reviews, quotes, ratings, counts, users, or sources. If a source cannot be reached, say so and move on. If there are 14 reviews, say 14.
- **Every quote is verbatim and linked.** Copied exactly from the page, with the URL of the review or thread. `reviews.py` drops any row without a link.
- **Reading, not scraping.** Read review pages the way a person does, in the browser, and copy rows into the sheet. No scraping libraries against stores or review sites whose terms forbid it. Official public feeds and APIs are fine within their terms:
  - Apple's customer reviews RSS feed: `https://itunes.apple.com/us/rss/customerreviews/id=<APP_ID>/sortBy=mostRecent/json`
  - Hacker News Algolia API: `hn.algolia.com/api/v1/search?query=...`
  - Reddit's official API under its terms
- **No fake reviews, ever.** Not for your app, not against theirs. It is illegal in the US (FTC 2024 rule) and many other places.
- **Reviewers are not your testimonials.** Their words are research. Do not put them on your landing page.

---

## Step 1: Collect

Aim for **100+ reviews** across at least **three sources**, recent first:

| Source | Where |
| --- | --- |
| App Store | App page → Ratings and Reviews → See All; or RSS feed |
| Google Play | Listing → See all reviews → sort by newest |
| G2, Capterra, Trustpilot | Product review pages, filter to 1–3 stars too |
| Reddit | Search "X alternative", "switched from X", "X sucks", "X vs" |
| Hacker News | Algolia API or site search, same queries |
| Original's own board | Public roadmap/feature-request board (Canny, etc.) and vote counts |
| Changelog | What it shipped, so you don't "fix" what's already fixed |

**CSV format** (`replica/reviews.csv`): `source,url,date,rating,text` — text copied exactly.

Read 3 and 4 star reviews too. "Love it, but..." is where the best fixes hide.

---

## Step 2: Rank

```bash
python3 scripts/reviews.py replica/reviews.csv --out replica/feedback.md
```

Sorts reviews into themes (`themes.json`, edit for the app's category), weights low ratings and recent reviews higher, marks themes with fewer than 3 reviews or only one source as **thin**, lists every request in users' own words, and surfaces low ratings that matched no theme. Read that last list by hand — often the best part.

---

## Step 3: Three Lists

From `feedback.md`, write three ranked lists. Each item: the problem in one line, how many reviews, how many sources, one or two linked quotes.

1. **What they hate.** Complaints about things the app does.
2. **What is missing.** Features people ask for by name.
3. **What is unsolved.** Whole jobs or groups the app ignores ("not built for teams", "useless for therapists"). These become **positioning**.

Thin themes are listed as thin. Do not present three angry Reddit comments as a trend.

---

## Step 4: The Fix Plan

Pick top 5–8 by evidence × how cheaply you can fix them. For each:
- What to build or change
- Size (S, M, L)
- Which skill does it
- The evidence

Add each one to `replica/features.csv` as a row with `original` set to `no`. Pricing and billing complaints go to `replica-launch`.

---

## Step 5: The Angle

Three positioning options, each grounded in a top theme:

```
For {{who}} who {{hate this about the original, in plain words}},
{{your app}} {{does this instead}}.
Evidence: {{theme}}, {{n}} reviews across {{n}} sources.
```

Recommend one. It drives `replica-brand`'s name and voice and `replica-launch`'s hero.

**Do not put the original's name in your app name, ads, or store listing.** A factual comparison page is a legal question for a lawyer in your country.

---

## Output

- `replica/reviews.csv`
- `replica/feedback.md`
- `replica/fixes.md` (three lists, fix plan, angle)
- New rows in `features.csv`
- Summary stating the sample size

Next step: **`replica-brand`**
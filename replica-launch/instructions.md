# replica-launch

**Writes the launch for an app clone** — the landing page built on the positioning angle, pricing set against the original's public pricing and its users' complaints, and the App Store and Google Play listing linted against the stores' limits and copycat rules.

Reads: `replica/fixes.md` (angle + evidence), `replica/brand.md`, `replica/brand.json`
Writes: `replica/launch/` — `landing.md`, `pricing.md`, `listing.json`, `launch-plan.md`

Tool: `scripts/listing.py`

```bash
python3 scripts/listing.py replica/launch/listing.json
```

---

## The Rules

- **No fake proof.** No invented testimonials, user counts, star ratings, press logos, or "trusted by" rows. An empty proof section is better than a fake one. Real beta users (with permission) are fine.
- **No reviewer quotes as testimonials.** The original's reviews are research.
- **The original's name stays OUT** of your app name, store listing, keywords, and ads. Apple and Google reject it, and it invites a takedown.

---

## Step 1: Landing Page

`landing.md`, section by section, then build it in the project:

1. **Hero**: the angle as a headline (what you do, for whom), one line under it, one button. Screenshot of the real product.
2. **The problem**: the top two "hate" themes, in plain words. Paraphrase, do not quote reviewers.
3. **How it works**: three steps, from the core flow.
4. **Features**: each tied to a fix from `fixes.md`. Lead with the fixes, not the parity features. Parity is the price of entry.
5. **Pricing**: the table from Step 2.
6. **FAQ**: the real objections, including "can I import from {{category}} tools?" if you built an importer.
7. **Final call to action.**

Copy in the brand voice. Run `replica-design`'s contrast check on the page.

---

## Step 2: Pricing

`pricing.md`:

- The original's public pricing page and 2–3 alternatives, in one table, with URL and date read. Prices change, so the date matters.
- What reviewers said about price and billing, from `feedback.md`, with counts.
- The model: free plan or trial, per seat or flat, monthly and annual (annual usually 2 months free).
- **Three tiers at most.** Name them by who they are for.
- Fix the billing complaints in the product: one-click cancel, clear renewal emails, no surprise per-seat jumps.
- The Stripe products and prices to create (the user creates them).

---

## Step 3: Store Listing

Fill `listing.json`, then:

```bash
python3 scripts/listing.py replica/launch/listing.json
```

Checks:
- App Store limits: name 30, subtitle 30, promotional_text 170, keywords 100, description 4000, whats_new 4000
- Google Play limits: title 30, short_description 80, full_description 4000
- Character counts the way a person does
- Flags ranking/price claims in short fields, emoji, wasted keyword characters
- **Any name from `avoid`** anywhere in the listing → error

**Apple Guideline 4.1 (Copycats)** rejects apps that simply copy a popular app or make minor changes to another app's name or UI. Your fixes and your own brand are what get you through review — make them visible in screenshots and first lines of description.

Also prepare:
- Screenshots at current required sizes (check App Store Connect / Play Console)
- Privacy labels and data safety form
- Privacy policy and support URLs
- Age rating
- Review notes with a demo account the reviewer can use

---

## Step 4: Launch Plan

`launch-plan.md`: waitlist or beta list before launch, analytics and error tracking live, where the original's unhappy users talk (Reddit threads, communities from research), a Product Hunt or Hacker News post that leads with the fix, and the first 10 users to talk to by hand.

---

## Output

`replica/launch/` complete, landing page built, `listing.py` passing.

Next step: **`replica-deploy`**
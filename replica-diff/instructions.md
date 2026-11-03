# replica-diff

**Compares an app clone against the original** — a feature parity score from the feature matrix (weighted by must, should, could) with the missing list in build order, plus a screenshot layout diff that ignores colour so a rebrand does not count against you.

**Two tools, both standard-library Python, no installs:**

```bash
# Feature parity
python3 scripts/parity.py replica/features.csv

# Layout diff (ignores colour — rebrand changes every colour on purpose)
python3 scripts/imgdiff.py replica/screens/S07.png replica/clone-screens/S07.png --out diff-S07.png

# JSON output for parity report
python3 scripts/imgdiff.py a.png b.png --json > replica/diffs/S07.json

# Combined report with visual scores
python3 scripts/parity.py replica/features.csv --visual replica/diffs/*.json --markdown > replica/parity.md
```

---

## What Parity Means Here

**Parity means it does the same job, not that it looks the same.**

- The **feature score** is the number that matters: can a user do everything they could do in the original.
- The **layout score** checks structure (is the information in the same places, in the same hierarchy), because that is what makes a switcher feel at home.
- It **deliberately ignores colour**, because `replica-brand` changes every colour on purpose.
- **Do not chase pixel parity** with the original. Its exact look is its trade dress, and it changes before launch.

---

## Step 1: Feature Parity

Make sure `replica/features.csv` is current: every row's `clone` column is `yes`, `partial` (with a note), `no`, or `skip` (with a reason). Then:

```bash
python3 scripts/parity.py replica/features.csv
```

**Weights:** must=3, should=2, could=1. `yes`=1, `partial`=0.5, `no`=0. `skip` and rows where original is `no` (features you added) are left out of the score.

Output: score, must-haves done of total, each area weakest first, missing list in build order. **Must-haves not done = not shippable.**

---

## Step 2: Layout Diff

For each key screen, take two screenshots at the **same viewport** (1440x900 desktop, 390x844 mobile) in the **same state** (same data shape, same tab open, logged in the same way).

- Original's screenshots → `replica/screens/` (from public pages or user's account)
- Clone's screenshots → `replica/clone-screens/`

```bash
python3 scripts/imgdiff.py replica/screens/S07.png replica/clone-screens/S07.png --out replica/diffs/S07.png
```

**Layout mode (default):** turns both into edge maps, cuts into a grid, compares where things are. Scores only cells with something in them. Reports score (90+=matches, 75+=close, 50+=partly), differing regions (original's pixel coords), height difference.

**`--mode pixel`:** exact comparison. Use for your own regressions (clone today vs clone last week), NOT against the original.

---

## Step 3: Behaviour Diff

Walk each flow in both apps and compare what the scores cannot see:
- Clicks to finish the core flow
- What happens on errors
- What is remembered between visits
- What emails arrive

**Fewer clicks than the original is a win.** Write each difference as: flow, original does, clone does, fix or keep.

---

## Step 4: The Report

`replica/parity.md`: overall score, feature score, layout score per screen, missing features in build order, behaviour differences, and a verdict:

| Verdict | Criteria |
|---------|----------|
| **not shippable** | any must-have missing, or any open S1 bug |
| **shippable** | all must-haves done, feature score 80+, no open S1 or S2 |
| **better than the original** | shippable, plus fixes from `replica-entrepreneur` (this is the goal) |

Give honest numbers. A clone at 62% is at 62%.

---

## Output

`replica/parity.md`, the diff images, and the top five things to build next.

Next: **`replica-build`** for the gaps, or **`replica-entrepreneur`** if parity is there.
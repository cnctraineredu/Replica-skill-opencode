# replica-brand

**Names and rebrands an app clone so it is the user's own** — name candidates with trademark, domain, store and handle checks, a new palette checked for contrast, a logo brief, a voice guide, and a sweep tool that finds anything of the original left in the codebase.

**Tool:** `scripts/sweep.py`

```bash
python3 scripts/sweep.py . --avoid "Original Name" --domains original.com --colors "#006bff"
python3 scripts/sweep.py . --config replica/brand.json
```

Writes: `replica/brand.md`, `replica/brand.json` (used by `sweep.py`, `listing.py`, and `replica-deploy`)

---

## Nothing launches under the original's identity. This is the line between "a clone" and "your app".

---

## Step 1: The Name

Read the angle from `replica/fixes.md`. Generate 20 candidates across styles:
- **Descriptive** (Booklink)
- **Compound** (Slotwise)
- **Invented** (Calvo)
- **Metaphor** (Harbor)
- **Verb** (Book)

Cut to **5** with these filters:

1. **Not confusingly similar** to the original in sound, look, or meaning, and not to any other app in the same category. That's the test trademark offices use — so it's the test here. No puns on their name, no "-ly" twin.
2. **Short, spellable after hearing it once**, no awkward meaning in big languages.
3. **Says something about the angle**, or at least does not fight it.

---

## Step 2: The Checks (Run, Not Assumed)

For each of the 5, a row per check. Mark each **to run**, or the result with the date it was run. **Never write "available" for a check nobody ran.**

| Check | Where |
| --- | --- |
| US trademark | tmsearch.uspto.gov (class 9, 42) |
| EU trademark | euipo.europa.eu eSearch, or TMview |
| Canada | ised-isde.canada.ca trademarks database |
| Global | WIPO Global Brand Database |
| Domain | `whois name.com` or registrar search |
| App Store / Play | Search exact name |
| Handles | X, Instagram, TikTok, GitHub |
| Web | Search "name + category" |

**These are screening checks, not legal clearance.** Before spending money on the name, a trademark lawyer should do a proper search.

---

## Step 3: Palette

A new palette, written into the same token roles `replica-design` set up.

Pick a **primary brand hue from a different family** than the original's (if theirs is blue, yours is not a nearby blue). Then:

```bash
python3 ../replica-design/scripts/contrast.py replica/design/tokens.json
```

**Zero AA failures.** Add the original's brand colours to `brand.json` so the sweep catches any that survive.

---

## Step 4: Logo Brief

Not a logo — a brief for whoever makes it (user, designer, image model):

- The idea in one line, tied to the name and angle
- Mark type: wordmark, symbol + wordmark, or monogram
- Must work at 16px (favicon) and as 1024px app icon
- Deliverables: SVG, app icon 1024x1024 (no transparency for iOS), favicon set, social image 1200x630
- **Must not resemble the original's mark**: no shared shape, colour pair, or letterform trick. Put the original's logo next to the drafts and check.

---

## Step 5: Voice

Three words for how it sounds, with what each does not mean ("direct, not blunt"). Five do/don't pairs. Then rewrite the 10 most-seen strings in the clone (sign up, empty states, main button, confirmation, error) in that voice. All fresh, none echoing the original's phrasing.

---

## Step 6: The Sweep

Replace every placeholder name, colour, and string. Then:

```bash
python3 sweep.py . --config replica/brand.json
```

It searches file contents and file names for the original's name (also inside identifiers like `CalendlyEmbed`), domains, and colours, skipping `node_modules`, build output, and the `replica/` planning folder. **Exit 1 = something is left.** Fix until it says clean.

Also check by eye: favicon, page titles, email templates, OG image, app icon.

---

## Output

- `replica/brand.md` (name with checks, palette, logo brief, voice)
- `replica/brand.json`
- Updated tokens
- Rewritten strings
- **Clean sweep**

Next step: **`replica-launch`**
# replica-recon

**Reverse-engineers any app into a recon map** — screen inventory, user flows, component list, inferred data model, and a feature matrix — from public pages, screenshots, app store listings, help docs, and the user's own account.

**First step of the Replica pack.** Everything else builds from what this skill writes. A bad recon map means a bad clone, so take the time here.

---

## Output

Files written to the user's project:

```
replica/recon.md        the recon map (template: references/recon-map.md)
replica/features.csv    the feature matrix (template: references/features.csv)
replica/screens/        reference screenshots of the original (never shipped)
```

---

## The Rules (Clean-Room)

This skill rebuilds **functionality and UX patterns**, clean-room style. It studies what the app does and how a user moves through it. It does **not** take anything the app owns.

1. **Public sources and the user's own account only.** Never log into an account that is not the user's, never ask for a password, never get past a paywall or a login by any trick.
2. **Reading, not scraping.** No crawlers, no bulk downloads, no loops. If a browser tool is connected to the user's own browser, read pages at human speed with the user present.
3. **No source code, no private APIs.** Do not read or save the app's JavaScript bundles, decompile its binary, or log its network calls to copy endpoints. Public API docs are fine to read.
4. **Check the terms.** Some products' terms forbid using an account to build a competing product. If the user's account is under terms like that, say so and work from public sources only.
5. **Screenshots are reference.** They live in `replica/screens/`, are used to compare layouts, and never go into the clone.

---

## Step 1: Scope

Ask three things, or propose answers and get a yes:

1. **Which app, which platform.** Web, iOS, Android, desktop.
2. **Which slice.** "All of Notion" is not a project. "Notion's pages, blocks and sharing" is. Default to the core loop: the one flow users pay for.
3. **Who it is for.** The user's own business, a niche, a product to sell.

---

## Step 2: List the Sources

Build a sources table first, with a URL on every row. In order of value:

| Source | What it gives you |
| --- | --- |
| Help center / docs | Most complete feature list, and the settings |
| Pricing page | Which features matter (they gate them) |
| Changelog | What was added recently, what the team thinks is important |
| App store listing | Screenshots of every key screen, the pitch, ratings |
| Public walkthrough videos | Real flows, click by click |
| Marketing site | Positioning, the core loop in their words |
| The user's own account | The real thing, every state, driven by the user |
| Public API docs | The data model, almost for free |

---

## Step 3: Screen Inventory

One row per screen. IDs are stable: **S01, S02...** Every other file refers to them.

Columns: `ID | screen | route or how you get there | purpose | key components | states seen`

**States matter:** empty, loading, filled, error, permission denied, mobile. An empty state you did not record is an empty state you will not build.

---

## Step 4: User Flows

**F01, F02...** Each one is a goal and the screens it passes through:

```
F01 Guest books a meeting
    S07 booking page -> S08 pick a time -> S09 details form -> S10 confirmed
    edge: no slots this week, time zone differs, slot taken while filling the form
```

Count the clicks on the happy path. It becomes the number to beat.

---

## Step 5: Components

Every repeated UI part: buttons, inputs, date pickers, modals, tables, toasts, nav. Name, variants, states, which screens use it. This becomes `replica-design`'s component list.

---

## Step 6: Inferred Data Model

Entities, fields and relationships, each with its evidence and a confidence:

```
Booking  id, event_type_id, start_at, end_at, guest_name, guest_email,
         status (confirmed | cancelled | rescheduled), answers (json)
         evidence: S09 form fields, S10 confirmation, help article "Cancel a booking"
         confidence: high
```

Mark guesses as guesses. `replica-architect` turns this into a real schema.

---

## Step 7: Feature Matrix

Write `replica/features.csv` (columns: `feature,area,priority,original,clone,notes`). Priority is **must / should / could**. `clone` starts at `no` for every row and gets filled in during the build. `replica-diff` scores it.

---

## Step 8: What Cannot Be Cloned

List it honestly, as `skip` rows with a reason: licensed content (a music catalogue, a stock library), the network and its users, data the app owns, partner deals, hardware, regulated licences (banking, health). "Clone any app" means the features and the flow, not what the app owns.

---

## Step 9: Size It

Screens, flows, entities, and the hard parts (realtime, sync, payments, calendar or email integrations, offline). Give a size: **S** (a weekend), **M** (a few weeks), **L** (a quarter), **XL** (rescope it). No promises of a perfect clone.

---

## Output Summary

Produce `replica/recon.md` and `replica/features.csv`, then a five-line summary:

1. The core loop
2. Screen and flow counts
3. The three hardest parts
4. What is out of scope
5. Next step: **`replica-architect`**
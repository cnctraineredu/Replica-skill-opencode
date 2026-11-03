# replica-architect

**Plans the stack, database schema, and API for an app clone** — from the recon map `replica-recon` wrote.

Reads: `replica/recon.md` and `replica/features.csv`
Writes: `replica/architecture.md` (template: `references/architecture.md`) and SQL in `replica/schema.sql` (or as first migration).

**Prerequisite:** If there is no recon map, stop and run `replica-recon` first. Planning a clone from memory of what an app does is how you miss half of it.

---

## Step 1: The Stack

Use what the user already knows if they have a stack. Otherwise the default — every part is managed, documented, and cheap at zero users:

| Layer | Default | Swap For |
| --- | --- | --- |
| Web app | Next.js (App Router) + TypeScript | Remix, SvelteKit, Rails |
| Styling | Tailwind, tokens from replica-design | CSS modules |
| Mobile | Expo (React Native) | SwiftUI, Kotlin |
| Database | Postgres on Supabase or Neon | PlanetScale, SQLite (Turso) |
| ORM | Drizzle or Prisma | raw SQL |
| Auth | Supabase Auth or Auth.js | Clerk |
| Payments | Stripe Checkout + Billing | Lemon Squeezy, Paddle |
| Email | Resend or Postmark | SES |
| Jobs | Vercel Cron, Inngest or Trigger.dev | a worker on Fly |
| Files | Supabase Storage or Cloudflare R2 | S3 |
| Hosting | Vercel | Netlify, Fly, Render |

**Write each choice with one line of why.** One database. No microservices. The clone does not need the original's architecture — it needs the original's features.

---

## Step 2: The Schema

Turn the inferred data model into SQL. For every table:

- `id uuid primary key default gen_random_uuid()`, `created_at`, `updated_at`
- An owner column (`user_id` or `org_id`) on everything a user owns
- Foreign keys with an `on delete` rule decided, not defaulted
- Indexes on every foreign key and every column you filter or sort by
- Enums or check constraints for status fields
- Times as `timestamptz`, always, stored in UTC
- Money as integer cents plus a currency column
- Access rules: Postgres row level security on Supabase, or one authorisation check per query in the data layer. Write which.

**Example (booking app):**

```sql
create table bookings (
  id uuid primary key default gen_random_uuid(),
  event_type_id uuid not null references event_types(id) on delete cascade,
  host_id uuid not null references users(id) on delete cascade,
  start_at timestamptz not null,
  end_at timestamptz not null,
  guest_name text not null,
  guest_email text not null,
  guest_timezone text not null,
  status text not null default 'confirmed'
    check (status in ('confirmed','cancelled','rescheduled')),
  answers jsonb not null default '{}',
  created_at timestamptz not null default now(),
  constraint no_zero_length check (end_at > start_at)
);
create index on bookings (host_id, start_at);
```

Then the hard constraints the recon found. Two guests booking the same slot is a **database problem** (exclusion constraint or unique index), not a UI problem.

---

## Step 3: The API

One table per flow from the recon map. For every route or server action:

```
method path | what it does | who can call it | input | output | flow
```

Plus webhooks in (Stripe, calendar providers) and out, and background jobs (reminders, sync, cleanup) with their schedule.

**Only official, public APIs with the user's own keys.** Never the original app's private endpoints, even if visible in a browser.

---

## Step 4: The Parts That Bite

Write a line on each that applies:
- Time zones and daylight saving
- Idempotency (webhooks arrive twice)
- Race conditions
- Rate limits
- File size limits
- Search
- Realtime
- Offline
- Email deliverability
- Multi-tenancy
- GDPR deletion

---

## Step 5: Build Order

1. **Vertical slice.** The core loop end to end, ugly: sign up, do the one thing, see the result. Proves the stack.
2. **Must-haves** from `features.csv`, by area.
3. **Should-haves**, then could-haves.
4. **The fixes** `replica-entrepreneur` finds, once it has run.

Each milestone lists its screens (S-IDs), tables, and routes.

---

## Output

`replica/architecture.md`, the SQL in `replica/schema.sql` (or as first migration), and a summary:

1. Stack in one line
2. Table count
3. Route count
4. The three riskiest parts
5. Next step: **`replica-design`**
# replica-deploy

**Ships an app clone live on the user's own domain** — a preflight gate (tests green, parity must-haves done, rebrand sweep clean, listing linted, legal pages up), production database and env vars, the host, DNS records for the domain and for email, Stripe live mode, OAuth redirects, monitoring, and mobile builds to TestFlight and Play.

Reads: everything in `replica/`
Writes: `replica/deploy.md` (the checklist in `references/preflight.md`, filled in)

---

## The Rules

- **Nothing goes live without the user's go.** Show the preflight results and ask.
- **The user buys and signs in.** Never buy a domain, enter a card, type a password, or paste a live key. Write the exact DNS records, env var names, and commands; the user does the account steps.
- **Not until it is rebranded.** The sweep must be clean. No exceptions.

---

## Step 1: Preflight

Run every check and paste the results into `deploy.md`:

```bash
npx playwright test                                                  # replica-test
python3 ../replica-diff/scripts/parity.py replica/features.csv       # must-haves done
python3 ../replica-brand/scripts/sweep.py . --config replica/brand.json  # exit 0: clean
python3 ../replica-launch/scripts/listing.py replica/launch/listing.json # if shipping to stores
npm run build                                                        # production build passes
```

Plus by hand:
- [ ] No open S1 or S2 bugs
- [ ] Privacy policy and terms live, every processor listed
- [ ] Cookie banner if non-essential cookies in EU/UK
- [ ] Account deletion works
- [ ] Favicon, titles, OG image, emails, app icon are yours

**Any failure stops the deploy.** Say which and why.

---

## Step 2: Production Services

- **Separate production database** (never the dev one), backups on, migrations run by the deploy, not by hand.
- **Env vars** set in the host for production, matching `.env.example`. Live keys only here.
- **Stripe**: switch to live mode, recreate products and prices, add the production webhook endpoint and its signing secret, test one real purchase and refund it.
- **OAuth**: add the production domain to every provider's redirect URIs and authorised origins. Google scopes needing verification must be approved, or only test users can sign in.
- **Email**: sending domain verified with the provider.

---

## Step 3: Host and Domain

Default: **Vercel** for Next.js (Netlify, Cloudflare Pages, Fly, Render otherwise). Connect the repo so `main` deploys and PRs get preview URLs.

Domain (after user buys it at any registrar):

| Record | Name | Value |
| --- | --- | --- |
| A | @ | host's apex IP (Vercel: shown in domain settings) |
| CNAME | www | host's target (Vercel: `cname.vercel-dns.com`) |
| TXT | @ or subdomain | host's verification value, if asked |

**Email DNS** from email provider:
- SPF (TXT)
- DKIM (CNAME or TXT)
- DMARC: start at `v=DMARC1; p=none; rua=mailto:you@yourdomain`, then tighten to `quarantine` once reports clean

Without these, confirmation emails go to spam.

Pick one canonical host (apex or www) and redirect the other. HTTPS is automatic on the hosts above; check it.

---

## Step 4: Watch It

- Error tracking (Sentry or host's)
- Uptime checks on home page and core flow's API
- Logs kept
- Analytics (privacy-friendly to avoid cookie banner)
- Alert to user's email or phone

Then do the core flow on the live site yourself, and ask the user to do it on their phone.

---

## Step 5: Mobile (if applicable)

- **Expo**: `eas build` then `eas submit` to TestFlight and Play internal testing
- **Native iOS**: archive in Xcode, upload to App Store Connect
- **Native Android**: Gradle bundle to Play Console

User owns developer accounts ($99/yr Apple, $25 once Google). Beta first, then review with the listing from `replica-launch`.

---

## Output

`replica/deploy.md` with every check and its result, the live URL, the DNS records set, and what to watch in the first week.

The clone is now an app with your name on it.
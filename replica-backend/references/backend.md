# Backend Checklist

## Auth
- [ ] Email sign up + verification
- [ ] Password reset
- [ ] Magic link (if in original)
- [ ] OAuth: Google
- [ ] OAuth: Apple
- [ ] Sessions: http-only secure cookies
- [ ] Sign out everywhere
- [ ] Roles/teams: owner, admin, member
- [ ] Authorisation function: `can(user, action, record)`
- [ ] Account deletion (real delete)

## Database
- [ ] Migrations from architecture.md (checked in)
- [ ] Migration runner script
- [ ] RLS policies on every table (Supabase) OR authorisation function in every query
- [ ] **Tested:** second user gets zero data back
- [ ] Seed script with realistic fake data
- [ ] Backups enabled on host

## Payments (Stripe)
- [ ] Stripe Checkout for sign up
- [ ] Customer Portal for manage/cancel
- [ ] No custom card forms
- [ ] Webhook: verify signature + store event_id
- [ ] Idempotent handlers (Stripe retries)
- [ ] `checkout.session.completed` handler
- [ ] `customer.subscription.updated` handler
- [ ] `customer.subscription.deleted` handler
- [ ] `invoice.payment_failed` handler
- [ ] Subscription status in DB, updated by webhooks
- [ ] Never trust client for subscription state
- [ ] One-click cancel

## Email & Jobs
- [ ] Transactional email: Resend / Postmark (own domain)
- [ ] Fresh templates (no copied copy)
- [ ] Jobs for time-based tasks (reminders, digests, sync, cleanup)
- [ ] Retries + dead-letter log
- [ ] Times in UTC, displayed in user's zone

## Integrations
| Integration | API | OAuth Scopes | Review Process | Rate Limits | Status |
|-------------|-----|--------------|----------------|-------------|--------|
| Google Calendar | | | Google OAuth verification (weeks) | | pending |
| Stripe | | N/A | N/A | | pending |
| Resend/Postmark | | N/A | Domain verification | | pending |

## Security Checklist
- [ ] Secrets only in env vars, `.env*` in `.gitignore`, nothing in client bundles
- [ ] Input validated on server (zod or similar) on every route
- [ ] Authorisation checked on every read and write, tested with second user
- [ ] Rate limits on auth, sign up, and anything sending email/SMS
- [ ] Webhooks verify signatures
- [ ] Uploads: size + type limits, served from separate domain/bucket
- [ ] No user data in URLs or logs
- [ ] Dependencies audited (`npm audit`)
- [ ] Privacy policy lists every processor (Stripe, Resend, host, analytics)

## Environment
- [ ] `.env.example` with all variable names, no values
- [ ] `.env.local` created by user (not committed)
- [ ] Test mode keys in use until replica-deploy
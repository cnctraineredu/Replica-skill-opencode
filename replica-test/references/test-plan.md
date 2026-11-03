# Test plan: {{your app}}

Build: {{commit}}  Date: {{YYYY-MM-DD}}  Env: local, seed data

| case | flow | type | steps | expected | auto | result |
| --- | --- | --- | --- | --- | --- | --- |
| F01-H1 | guest books a meeting | happy | open /u/demo/30min, pick tomorrow 10:00, fill form, submit | confirmation screen, booking on host dashboard, email to both | e2e | |
| F01-E1 | | edge: slot taken while filling form | | clear message, pick another slot, no double booking | e2e | |
| F01-E2 | | edge: guest in another time zone | | times shown in guest zone, stored in UTC | e2e | |
| F01-N1 | | negative: card declined | | error shown, no booking created | manual | |

## Case ID Format

- **F{flow}-H{num}** — Happy path
- **F{flow}-E{num}** — Edge case
- **F{flow}-N{num}** — Negative case

## Edge Case Checklist (apply to every flow)

- [ ] Empty input
- [ ] Very long input
- [ ] Emoji and accents
- [ ] Two tabs at once
- [ ] Double click on submit
- [ ] Back button mid-flow
- [ ] Refresh mid-flow
- [ ] Slow network
- [ ] Offline
- [ ] Expired session
- [ ] Second user's data (must be invisible)
- [ ] Time zones and daylight saving
- [ ] Mobile width (390px)
- [ ] Keyboard only
- [ ] Screen reader labels

## Negative Case Checklist (apply where relevant)

- [ ] Wrong password
- [ ] Card declined (Stripe test: `4000 0000 0000 0002`)
- [ ] Permission denied
- [ ] Deleted record
- [ ] Expired token
- [ ] Rate limited
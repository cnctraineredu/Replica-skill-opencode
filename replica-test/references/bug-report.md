### BUG-{{001}}: {{short title, what is wrong}}

- Severity: {{S1 | S2 | S3 | S4}}
- Flow / case: {{F01 / F01-E1}}
- Screen: {{S08}}
- Build: {{commit}}  Browser / device: {{Chrome 140, 1440px}}

Steps
1. {{exact step}}
2. {{exact step}}

Expected: {{what should happen}}
Actual: {{what happened}}
Evidence: {{replica/bugs/BUG-001.png, console error text, failing test name}}
Suspected cause: {{optional}}
Status: {{open | fixed in abc1234 | won't fix: reason}}

---

## Severity Guide

| Level | Definition | Examples |
|-------|------------|----------|
| **S1** | Data loss, security hole, payments wrong, core flow blocked | Booking created twice, auth bypass, Stripe webhook not processed, PII in logs |
| **S2** | Feature broken, no workaround | "Reschedule" button does nothing, calendar sync fails silently, email not sent |
| **S3** | Broken with workaround, or visibly wrong | Wrong timezone displayed but can manually fix, layout shift on mobile, slow query |
| **S4** | Cosmetic | Off-by-1px alignment, typo in empty state, colour slightly off brand |

---

## Bug Report Checklist

- [ ] Exact reproduction steps (numbered)
- [ ] Expected vs actual behavior
- [ ] Evidence: screenshot, console error, failing test name
- [ ] Severity assigned correctly
- [ ] Linked to flow/case ID
- [ ] Status tracked to fix commit
# Component Specs

For every component in the recon list, one block:

```
ComponentName
  variants  variant1, variant2, variant3
  sizes     sm XXpx, md XXpx, lg XXpx
  states    default, hover, active, focus-visible (2px ring, accent), disabled, loading
  tokens    bg <role>, text <role>, radius <size>, font <size>/<weight>
  a11y      real semantic element, visible focus, loading keeps label for screen readers
  used on   S01, S02, S03
```

## Required States (for every component)

- `default`
- `hover`
- `active`
- `focus-visible` (2px ring, accent colour)
- `disabled`
- `loading`
- `error` (for inputs/forms)
- `empty` (for lists/tables)

## Accessibility Baseline

- Real semantic HTML elements (`<button>`, `<input>`, `<dialog>`, etc.)
- Visible focus styles (never `outline: none` without replacement)
- Loading state keeps accessible label for screen readers
- Keyboard navigation works for all interactive states
- ARIA attributes where native semantics don't cover it

## Example Components

### Button
```
Button
  variants  primary, secondary, ghost, danger
  sizes     sm 32px, md 40px, lg 48px
  states    default, hover, active, focus-visible (2px ring, accent), disabled, loading
  tokens    bg accent, text on-accent, radius md, font sm/600
  a11y      real <button>, visible focus, loading keeps the label for screen readers
  used on   S02, S07, S09
```

### Input
```
Input
  variants  default, error, success
  sizes     sm 32px, md 40px, lg 48px
  states    default, hover, focus-visible (2px ring, accent), disabled, error, loading
  tokens    bg surface, text text, border border-input, radius md, font base/400
  a11y      real <input>, associated <label>, aria-describedby for error/help
  used on   S03, S05, S09
```

### Modal / Dialog
```
Modal
  variants  default, confirm, form
  sizes     sm 400px, md 560px, lg 720px, full
  states    default, open, closing
  tokens    bg surface, border border, radius lg, shadow pop
  a11y      real <dialog>, focus trap, ESC closes, restore focus on close
  used on   S04, S08
```

### Table
```
Table
  variants  default, striped, hoverable
  sizes     -
  states    default, loading, empty, error
  tokens    bg surface, border border, text text, header text-muted
  a11y      <table> with <thead>/<tbody>, scope="col", sortable headers keyboard accessible
  used on   S06, S11
```

### Card
```
Card
  variants  default, bordered, elevated
  sizes     -
  states    default, hover, interactive
  tokens    bg surface, border border, radius md, shadow card
  a11y      article or section, interactive cards use <button> wrapper
  used on   S01, S05, S10
```

### Select / Dropdown
```
Select
  variants  default, searchable, multi
  sizes     sm 32px, md 40px, lg 48px
  states    default, hover, focus-visible, open, disabled, loading
  tokens    bg surface, text text, border border-input, radius md
  a11y      real <select> or combobox pattern, keyboard navigation, aria-expanded
  used on   S03, S07
```

### Toast / Notification
```
Toast
  variants  info, success, warning, danger
  sizes     -
  states    enter, visible, exit
  tokens    bg surface, border border, text text, accent per variant
  a11y      role="alert" or "status", aria-live="polite", auto-dismiss with pause on hover
  used on   S05, S10, S12
```

### Tabs
```
Tabs
  variants  default, underline, pills
  sizes     -
  states    default, active, hover, focus-visible, disabled
  tokens    bg transparent, text text-muted, active accent, border border
  a11y      role="tablist", aria-selected, keyboard arrow navigation, focus management
  used on   S02, S06
```

---

Add your components here following the same format.
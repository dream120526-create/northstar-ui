# Forms: fields, labels, validation, and authentication

> The goal of a form is not "collecting data"; it is letting users get it right the first time, always know where they stand, and fix mistakes when they happen.

## 1. Field anatomy

```
Label 13–14/500 (persistent; never replaced by a placeholder)
┌──────────────────────────────┐
│ Input value 16               │  Height 44–52, radius 8–12
└──────────────────────────────┘
Helper text 13 secondary / Error 13 danger color + icon
```

- Labels sit above the field, left-aligned; never right-align labels (it breaks scanning).
- Label-to-field spacing 6–8; between fields 16–24; between groups 32.
- Mark required only when needed; label optional fields "(optional)" instead of a sea of asterisks.
- Input width = expected content width: short for verification codes, long for addresses; don't stretch everything to full width.
- Placeholders hold examples only ("name@company.com"); the label must remain after typing.
- Prefix/suffix (¥, kg, %): inside the field on each side, secondary color, not editable.

## 2. Label approaches

| Approach | When to use | Watch out |
| --- | --- | --- |
| Fixed top label | Default, all cases | Most robust, recommended |
| Left label + right control | Desktop settings pages, short inline forms | Keep the label column width consistent; switch to top labels on mobile |
| Floating label | Very tight space, brand requirements | Must have placeholder and focused states; never rely on animation alone |
| Section heading + inline label | Structured groups like address or payment | Group heading 13/600; don't repeat field names |

## 3. Validation and errors

- Timing: validate format on blur; validate everything on submit; **never error on every keystroke**.
- Show errors inline below the field, with a danger-colored border + icon; never a toast alone.
- Error copy has three parts: what happened + why + how to fix it.
  - Bad: "Invalid input"
  - Good: "That email format isn't right — for example name@company.com"
- On failed submit: scroll/focus the first invalid field and show a one-line summary at the top of the form.
- No dialog on success; button loading → success state → next step.
- Be cautious with disabled submit buttons: letting users click and see what's missing beats "grayed out with no explanation".

## 4. Special controls

| Control | Rules |
| --- | --- |
| Switch | Applies immediately; state what on/off means, not just the feature name |
| Radio | ≤ 5 options inline; > 5 use a dropdown |
| Checkbox | ≤ 7 options inline; more uses a searchable list |
| Date | Allow manual entry; range selection shows two fields + shortcuts |
| Upload | Show format/size limits; include progress, retry on failure, delete |
| Password | Visibility toggle; show rules at sign-up instead of erroring afterward |
| Verification code | 60s countdown; resend on error; state when it's case-insensitive |
| Rich text | Toolbar with common items only; sanitize pastes; simplify on mobile |

## 5. Form layout

- One column on phone; ≤ 2 columns on desktop, grouped by meaning (name/phone together, address together).
- Don't force 3 short fields onto one row and misalign labels; use `grid` to align baselines.
- Footer action bar is fixed: primary right, cancel left (desktop); on mobile the primary button is full width, sticky at the bottom + safe area.
- Group long forms, one heading per group; when showing "Step 2 of 4", allow going back.
- Intercept navigation with unsaved changes; never discard silently.

## 6. Authentication

```
Brand
Title (Welcome back) + one line of explanation
[Email]
[Password · visibility toggle]
[Forgot password]
[Primary button: Sign in]
—— or ——
[Third-party sign-in]
No account yet? Sign up
```

- One primary button; third-party buttons use brand colors and never match the primary button's weight.
- Errors inline; for security, "email or password is incorrect" can cover both unknown account and wrong password.
- After sign-up, go straight into the product; don't force another sign-in.
- Support password managers: `autocomplete="email"` / `current-password` / `new-password`.
- Keep the primary button visible when the keyboard opens; Enter submits.

## 7. Form acceptance

- [ ] Every field has a persistent label; placeholders are examples only
- [ ] Required/optional is clear; field width matches content
- [ ] Validate on blur/submit only; errors are inline and actionable
- [ ] Submit has loading and prevents duplicates; failures lose no data
- [ ] Single column on phone; keyboard doesn't cover the primary button
- [ ] Supports autofill and password managers
- [ ] Nothing truncates at 200% text size; screen readers announce labels and errors

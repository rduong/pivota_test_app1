# UX Mockup & Design Specification
## Todo List App with User Management (TodoApp)

**Version:** 1.0  
**Date:** 2026-04-15  
**Status:** Validated  
**Sources:** UserStories-TodoApp.md, JOURNEYS-TodoApp.md, PRD-TodoApp.md, FRD-TodoApp.md  

---

## 1. Design Principles

- **Clarity over cleverness** — Every element serves a clear purpose; no decorative complexity
- **Speed of use** — Primary actions reachable in 1–2 clicks; no unnecessary modals or confirmation flows except for destructive actions
- **Plain language** — All labels, errors, and messages use everyday language (PER-03 standard)
- **Visual hierarchy** — Most important actions (Add Todo, Generate Code, Log In) are the most prominent
- **Consistency** — Same patterns for lists, empty states, and error messages across all views

---

## 2. Design System

### 2.1 Color Palette

```
Background:     #FFFFFF  (white)
Surface:        #F8F9FA  (off-white — cards, panels)
Border:         #E9ECEF  (light gray — dividers, borders)
Primary:        #4361EE  (blue — primary buttons, active states)
Primary Hover:  #3A56D4  (darker blue)
Accent Admin:   #7B2FBE  (purple — admin role badge, admin header accent)
Text Primary:   #212529  (near-black — headings, todo titles)
Text Secondary: #6C757D  (gray — meta text, timestamps, descriptions)
Error:          #DC3545  (red — inline validation errors)
Success:        #198754  (green — "Copied!", completion states)
Completed Todo: #ADB5BD  (muted gray — strikethrough text for done items)
```

### 2.2 Typography (Outfit from Google Fonts)

```
Font Family:    'Outfit', sans-serif

Heading 1:      28px / 700 weight — Page titles
Heading 2:      20px / 600 weight — Section headings
Heading 3:      16px / 600 weight — Card titles, form labels
Body:           15px / 400 weight — Body text, form input values
Small:          13px / 400 weight — Timestamps, helper text, badges
Button:         14px / 600 weight — All button labels
```

### 2.3 Spacing Scale (8px base)

```
xs:   4px   — Icon-to-text gaps
sm:   8px   — Internal padding, small gaps
md:  16px   — Component padding
lg:  24px   — Section gaps
xl:  32px   — Page-level padding
2xl: 48px   — Major section separators
```

### 2.4 Border Radius

```
Small:   4px  — Inputs, small chips
Medium:  8px  — Cards, dropdowns
Large:  12px  — Panels, modals
Pill:   999px — Role badges, filter tabs
```

---

## 3. User Flows

### Flow 1: First-Time Admin Setup (JRN-PER01.01)

```
[App opens] → [Login View — first-run notice visible]
    ↓
[Admin types "admin" / "admin123"]
    ↓
[Click "Log In"]
    ↓
[Admin Dashboard]
    ↓
[Click "Generate Invite Code"]
    ↓
[Code appears in invite list with "Copy" button]
    ↓
[Click "Copy" → "Copied!" confirmation]
    ↓
[Send code via external channel]
```

**User Stories Referenced:** US-F8-01, US-F8-02, US-F1-01, US-F1-05, US-F3-01, US-F3-02

---

### Flow 2: New User Registration (JRN-PER02.01, JRN-PER03.01)

```
[Login View]
    ↓
[Click "Register"]
    ↓
[Registration Form]
    ↓
    ├── [Invalid invite code] → [Inline error] → [Fix code] → [Retry]
    ├── [Username taken] → [Inline error] → [New username] → [Retry]
    └── [Valid] → [Account created] → [Auto-login] → [User Dashboard]
```

**User Stories Referenced:** US-F2-01, US-F2-02, US-F2-03, US-F2-04

---

### Flow 3: Daily Todo Management (JRN-PER02.02, JRN-PER02.03)

```
[App opens] → [Auto-login] → [User Dashboard]
    ↓
[Review existing todos]
    ↓
    ├── [Click checkbox] → [Toggle complete/incomplete]
    ├── [Click "Edit"] → [Edit form] → [Save / Cancel]
    ├── [Click "Delete"] → [Confirm] → [Remove]
    └── [Type in Add form] → [Click Add / Enter] → [New item at top]
    ↓
[Click filter tab: All / Active / Completed]
```

**User Stories Referenced:** US-F5-01 through US-F5-06

---

### Flow 4: Admin Views Team Todos (JRN-PER01.02)

```
[Admin Dashboard]
    ↓
[User List — click "View Todos" for a user]
    ↓
[Admin View User — read-only todo list]
    ↓
[Click "← Back"] → [Admin Dashboard]
```

**User Stories Referenced:** US-F4-01, US-F4-02, US-F6-01

---

## 4. Screen Wireframes

---

### Screen 1: Login View

**Layout Description:**
- Centered card on a light background, max-width 400px, vertically centered on viewport
- App name "TodoApp" as H1 in Primary color above the card
- Inside the card: form with 2 inputs and 1 button, stacked vertically
- Link "Don't have an account? Register →" below the button

**Information Hierarchy:**
1. App name (largest, brand)
2. First-run notice banner (only on first run — yellow/warm background, dismissible)
3. Login form (core action)
4. Register link (secondary action)

**Wireframe:**
```
┌─────────────────────────────────────────────┐
│                                             │
│              TodoApp                        │  ← H1, Primary color
│                                             │
│  ┌─────────────────────────────────────┐   │
│  │  ⓘ First time setup:               │   │  ← First-run notice (warm bg)
│  │    Login with admin / admin123      │   │     Hidden after first login
│  └─────────────────────────────────────┘   │
│                                             │
│  ┌─────────────────────────────────────┐   │
│  │  Username                           │   │
│  │  ┌─────────────────────────────┐   │   │
│  │  │ e.g. admin                  │   │   │
│  │  └─────────────────────────────┘   │   │
│  │                                     │   │
│  │  Password                           │   │
│  │  ┌─────────────────────────────┐   │   │
│  │  │ ••••••••                    │   │   │
│  │  └─────────────────────────────┘   │   │
│  │                                     │   │
│  │  [       Log In       ]             │   │  ← Primary button
│  │                                     │   │
│  │  Don't have an account? Register →  │   │  ← Link
│  └─────────────────────────────────────┘   │
│                                             │
└─────────────────────────────────────────────┘
```

**States:**
- **Default:** Empty form, first-run notice visible (if first load)
- **Validation Error:** Red inline message below username or below password (generic "Invalid username or password")
- **Loading:** Log In button disabled with "Logging in…" text during async hash check
- **Success:** Immediate redirect — login view unmounts

**Accessibility:**
- `<label for="username">Username</label>` paired with `<input id="username">`
- Error messages use `role="alert"` and `aria-describedby` on the input
- First-run notice has `role="status"`
- Tab order: username → password → submit button

---

### Screen 2: Registration View

**Layout Description:**
- Same centered card layout as login, max-width 420px
- 4 fields stacked: username, password, confirm password, invite code
- Helper text below the invite code field
- "Create Account" primary button
- "Already have an account? Log in ←" link at the bottom of the card

**Information Hierarchy:**
1. App name
2. "Create your account" subtitle
3. Form fields (each with label + input + optional error)
4. Submit button
5. Login link

**Wireframe:**
```
┌─────────────────────────────────────────────┐
│                                             │
│              TodoApp                        │
│         Create your account                 │  ← H2 subtitle
│                                             │
│  ┌─────────────────────────────────────┐   │
│  │  Username *                         │   │
│  │  ┌─────────────────────────────┐   │   │
│  │  │ 3–30 chars, letters/numbers │   │   │  ← placeholder
│  │  └─────────────────────────────┘   │   │
│  │  ⚠ This username is already taken  │   │  ← inline error (hidden by default)
│  │                                     │   │
│  │  Password *                         │   │
│  │  ┌─────────────────────────────┐   │   │
│  │  │ Minimum 8 characters        │   │   │
│  │  └─────────────────────────────┘   │   │
│  │                                     │   │
│  │  Confirm Password *                 │   │
│  │  ┌─────────────────────────────┐   │   │
│  │  │ Re-enter your password      │   │   │
│  │  └─────────────────────────────┘   │   │
│  │                                     │   │
│  │  Invite Code *                      │   │
│  │  ┌─────────────────────────────┐   │   │
│  │  │ e.g. A3BF92KD               │   │   │
│  │  └─────────────────────────────┘   │   │
│  │  ℹ Sent to you by your admin        │   │  ← helper text
│  │                                     │   │
│  │  [    Create Account    ]           │   │
│  │                                     │   │
│  │  Already have an account? Log in ← │   │
│  └─────────────────────────────────────┘   │
│                                             │
└─────────────────────────────────────────────┘
```

**States:**
- **Default:** Empty form
- **Field Validation Error:** Red text below the specific field (each field has its own error slot)
- **Loading:** Button disabled, text "Creating account…"
- **Success:** Immediate redirect to User Dashboard

**Accessibility:**
- All inputs have `<label>` with `for` matching `id`
- Required indicator `*` explained in a legend or SR-only note
- Error messages linked to inputs via `aria-describedby`
- Helper text linked via `aria-describedby` as well

---

### Screen 3: User Dashboard

**Layout Description:**
- Full-width layout with a sticky header bar
- Header: App name (left), username + role badge + Logout button (right)
- Main content area: Add Todo form at top, filter tabs below, todo list below that
- Max content width: 640px, centered for readability

**Information Hierarchy:**
1. Header (persistent navigation)
2. Add Todo form (primary CTA — always visible)
3. Item count summary + filter tabs
4. Todo list items

**Wireframe:**
```
┌─────────────────────────────────────────────────────┐
│ TodoApp              jordan  [User]  [Log Out]        │  ← Sticky header
├─────────────────────────────────────────────────────┤
│                                                       │
│   My Tasks                                            │  ← H2
│                                                       │
│  ┌───────────────────────────────────────────────┐  │
│  │  What needs to be done?               [Add]   │  │  ← Add form (title input)
│  │  Optional: Add a description…                 │  │  ← Collapsible textarea
│  └───────────────────────────────────────────────┘  │
│                                                       │
│  3 of 7 completed          [All] [Active] [Completed] │  ← Count + filters
│                                                       │
│  ┌───────────────────────────────────────────────┐  │
│  │ ○  Review PR feedback            [Edit][Delete]│  │  ← Active todo
│  ├───────────────────────────────────────────────┤  │
│  │ ○  Update README                 [Edit][Delete]│  │
│  ├───────────────────────────────────────────────┤  │
│  │ ✓  Set up dev environment        [Edit][Delete]│  │  ← Completed (strikethrough)
│  ├───────────────────────────────────────────────┤  │
│  │ ✓  Write unit tests              [Edit][Delete]│  │
│  └───────────────────────────────────────────────┘  │
│                                                       │
└─────────────────────────────────────────────────────┘
```

**Todo Item States:**
- **Active:** Circle checkbox, normal text color, Edit + Delete buttons visible
- **Completed:** Checked checkbox (filled), strikethrough title, muted gray text
- **Edit mode:** Title and description become editable inputs inline; Save/Cancel buttons replace Edit/Delete
- **Delete confirmation:** "Are you sure? [Yes, delete] [Cancel]" inline below the item

**Empty States:**
- All filter + no todos: "No todos yet. Add your first task above." (centered, muted text + icon)
- Active filter + all done: "All tasks are complete! 🎉"
- Completed filter + none done: "No completed tasks yet."

**Accessibility:**
- Checkbox inputs with `<label>` or `aria-label` ("Mark 'Review PR feedback' as complete")
- Edit and Delete buttons have `aria-label` referencing the todo title
- Live region (`aria-live="polite"`) on count summary for dynamic updates

---

### Screen 4: Admin Dashboard

**Layout Description:**
- Same sticky header as User Dashboard, but with purple admin accent on role badge
- Two-panel layout (stacked on mobile, side-by-side on ≥768px):
  - Left/Top panel: Invite Code Manager
  - Right/Bottom panel: User List
- Max content width: 900px

**Information Hierarchy:**
1. Header with admin role indicator
2. Invite Code Manager (admin's primary first-run task)
3. Registered Users List

**Wireframe:**
```
┌─────────────────────────────────────────────────────────────┐
│ TodoApp              admin  [Admin]  [Log Out]                │  ← Purple Admin badge
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  ┌────────────────────────────┐  ┌────────────────────────┐ │
│  │  Invite Codes              │  │  Registered Users      │ │
│  │  ─────────────────────     │  │  ─────────────────     │ │
│  │  [+ Generate Invite Code]  │  │  jordan  User  Apr 15  │ │
│  │                            │  │  [View Todos]          │ │
│  │  ┌──────────────────────┐ │  │                        │ │
│  │  │ A3BF92KD  [Copy] ← New│ │  │  casey   User  Apr 15  │ │
│  │  └──────────────────────┘ │  │  [View Todos]          │ │
│  │                            │  │                        │ │
│  │  Code       Status  User   │  │  admin   Admin Apr 15  │ │
│  │  ────────── ─────── ─────  │  │  (that's you)         │ │
│  │  A3BF92KD   Unused  —      │  └────────────────────────┘ │
│  │  7KD2XMN1   Used    jordan │                               │
│  │  PQ83WR5C   Used    casey  │                               │
│  └────────────────────────────┘                               │
│                                                               │
└─────────────────────────────────────────────────────────────┘
```

**Invite Code Panel States:**
- **Default:** Generate button + code list below
- **After generate:** New code highlighted at top of list; remains until scrolled past
- **Copy success:** Button text changes to "Copied! ✓" for 2 seconds, then reverts
- **Empty state:** "No invite codes yet. Click 'Generate Invite Code' to get started."

**User List States:**
- **Default:** All users listed
- **Empty (only admin exists):** "No users have registered yet. Share an invite code to get started."

**Accessibility:**
- "Generate Invite Code" button has `aria-describedby` pointing to the invite list's `aria-label`
- Copy button has `aria-label="Copy invite code A3BF92KD"`
- Status badges use `role="status"` or are plain text

---

### Screen 5: Admin — View User Todos (Read-Only)

**Layout Description:**
- Same header
- Breadcrumb or back link at top of content: "← Back to Users"
- "Viewing todos for: jordan" heading
- Read-only banner below heading
- Todo list without any interactive controls (no checkboxes, no Edit/Delete buttons)

**Wireframe:**
```
┌─────────────────────────────────────────────────────┐
│ TodoApp              admin  [Admin]  [Log Out]        │
├─────────────────────────────────────────────────────┤
│                                                       │
│  ← Back to Users                                      │  ← Back link
│                                                       │
│  Viewing todos for: jordan                            │  ← H2
│  ┌───────────────────────────────────────────────┐  │
│  │  👁  Admin View — Read Only                    │  │  ← Info banner
│  └───────────────────────────────────────────────┘  │
│                                                       │
│  4 tasks (2 completed)                                │  ← Count (no filter tabs)
│                                                       │
│  ┌───────────────────────────────────────────────┐  │
│  │  ○  Review PR feedback                         │  │  ← No Edit/Delete buttons
│  ├───────────────────────────────────────────────┤  │
│  │  ○  Update README                              │  │
│  ├───────────────────────────────────────────────┤  │
│  │  ✓  Set up dev environment                     │  │  ← Strikethrough
│  ├───────────────────────────────────────────────┤  │
│  │  ✓  Write unit tests                           │  │
│  └───────────────────────────────────────────────┘  │
│                                                       │
└─────────────────────────────────────────────────────┘
```

**States:**
- **Default:** Read-only todo list with completion states shown visually
- **Empty:** "jordan hasn't added any todos yet."

**Accessibility:**
- "Read Only" banner uses `role="note"`
- Todo items are `<li>` elements; no interactive controls rendered
- Checkboxes are NOT present (purely visual state indicators if shown)

---

## 5. Interaction Patterns

### 5.1 Form Submission
- Primary button becomes disabled immediately on click to prevent double-submission
- Async operations show button text change (e.g., "Logging in…")
- Success: immediate state transition (no intermediate success screen)
- Error: inline messages appear; button re-enabled

### 5.2 Todo Completion Toggle
- Single click on checkbox or todo row (left side)
- Immediate visual feedback — no async operation
- Checkbox animates from empty → filled with a brief scale animation

### 5.3 Inline Edit
- Edit button replaces todo display with an inline form
- `Tab` key moves between title and description fields
- `Enter` in title field saves (does not add newline)
- `Escape` key cancels edit
- Save button is disabled if title is empty

### 5.4 Delete Confirmation
- Inline approach (preferred over browser `confirm()`):
  - Delete button text changes to "Are you sure?"
  - Two inline buttons appear: "Yes, delete" (red) and "Cancel"
  - Auto-cancel after 5 seconds of inactivity

### 5.5 Copy to Clipboard
- Uses `navigator.clipboard.writeText()`
- On success: button shows "Copied! ✓" in green for 2 seconds, reverts
- On failure: button shows "Copy manually" and code is auto-selected in input field

### 5.6 Filter Tabs
- Tab/button with `aria-selected="true"` on active filter
- Active tab: Primary background, white text
- Inactive tab: Transparent background, text-secondary color, hover state

---

## 6. Responsive Considerations

### Mobile (375px–767px)
- Cards: full width with 16px horizontal padding on the page
- Admin Dashboard: single column — Invite Codes above User List
- Todo items: Edit and Delete buttons stacked vertically or condensed to icon-only buttons with `aria-label`
- Header: App name only on left; username + logout on right (username may truncate)

### Tablet (768px–1023px)
- Admin Dashboard: 2-column side-by-side layout
- Todo list: Max-width 600px, centered
- Login/Registration cards: 420px centered

### Desktop (1024px+)
- Full layouts as shown in wireframes above
- Max content width: 900px (admin), 640px (user), 400px (auth cards)

---

## 7. Accessibility Notes

### Global
- All pages have a `<main>` landmark element
- Focus is managed on view transitions: first focusable element in new view receives focus
- Color is never the only indicator of state (error messages always have text, not just red color)
- Minimum contrast ratio: 4.5:1 for body text (WCAG 2.1 AA)

### Forms
- `<form>` elements with `novalidate` (custom validation preferred)
- All inputs have programmatically associated labels (`<label for>`)
- Error messages use `aria-describedby` to link to their input
- Required fields marked with `*` and explained with `<span class="sr-only">required</span>` or form legend

### Dynamic Content
- Todo list updates use `aria-live="polite"` on the list container
- Item count uses `aria-live="polite"` on the count element
- Success messages (e.g., "Copied!") use `role="status"`

### Keyboard Navigation
- Full keyboard navigability — no mouse-only interactions
- Tab order follows visual reading order
- `Escape` closes modals / cancels edits
- `Enter` submits focused forms

---

*Document generated by Pivota Spec UX Designer | TodoApp v1.0 | 2026-04-15*

# Functional Requirements Document (FRD)
## Todo List App with User Management (TodoApp)

**Version:** 1.0  
**Date:** 2026-04-15  
**Status:** Validated  
**Source:** PRD-TodoApp.md  

---

## 1. Overview

This document provides detailed functional specifications for all features defined in the TodoApp PRD. Each section maps to a PRD feature (F0–F8) and defines the precise behavior, data structures, validation rules, and UI requirements expected from the implementation.

---

## 2. Data Model

### 2.1 Data Storage Architecture

All data is stored in the browser's `localStorage` using namespaced JSON keys. No data is ever transmitted to any server.

**localStorage Key Namespace:**

| Key | Type | Description |
|---|---|---|
| `todoapp_users` | JSON Array | All registered user accounts |
| `todoapp_invites` | JSON Array | All generated invite codes |
| `todoapp_todos_{username}` | JSON Array | Todo items scoped per user |
| `todoapp_session` | JSON Object | Current active session |

### 2.2 Data Schemas

#### User Object
```json
{
  "username": "string (unique, 3–30 chars, alphanumeric + underscore)",
  "passwordHash": "string (SHA-256 hex or bcrypt-equivalent client-side hash)",
  "role": "admin | user",
  "registeredAt": "ISO 8601 timestamp",
  "inviteCode": "string | null (code used to register; null for seeded admin)"
}
```

#### Invite Code Object
```json
{
  "code": "string (UUID v4 or 8-char alphanumeric random string)",
  "createdAt": "ISO 8601 timestamp",
  "createdBy": "string (admin username)",
  "usedBy": "string | null (username of user who redeemed it; null if unused)",
  "usedAt": "ISO 8601 timestamp | null"
}
```

#### Todo Item Object
```json
{
  "id": "string (UUID v4 — unique per todo)",
  "title": "string (required, 1–200 chars)",
  "description": "string | null (optional, max 1000 chars)",
  "completed": "boolean (default: false)",
  "createdAt": "ISO 8601 timestamp",
  "updatedAt": "ISO 8601 timestamp"
}
```

#### Session Object
```json
{
  "username": "string",
  "role": "admin | user",
  "token": "string (random UUID, regenerated on each login)",
  "loginAt": "ISO 8601 timestamp"
}
```

---

## 3. Functional Requirements by Feature

### FR-F0: Application Shell & Navigation

**FR-F0.1 — Single-Page Architecture**  
The application MUST be implemented as a single HTML document. All views (login, register, admin dashboard, user dashboard) MUST be rendered as toggled sections within the same document. No browser navigation events (back/forward) or URL hash changes are required, but may be implemented optionally.

**FR-F0.2 — View States**  
The application MUST support the following discrete view states, with exactly one active at any time:
- `LOGIN` — Login form (default state on app load if no session)
- `REGISTER` — Registration form (navigated to from login page)
- `ADMIN_DASHBOARD` — Admin home: invite code manager + user list
- `USER_DASHBOARD` — User home: personal todo list
- `ADMIN_VIEW_USER` — Admin viewing a specific user's todos (read-only)

**FR-F0.3 — Client-Side Router**  
View transitions MUST be driven by a simple state variable (e.g., `currentView`). The router reads application state from localStorage on page load and routes to the appropriate view.

**FR-F0.4 — Typography**  
The application MUST load the Outfit font family from Google Fonts CDN. Outfit MUST be applied as the primary font for all visible text. A sans-serif fallback MUST be specified in the CSS font stack.

**FR-F0.5 — Responsive Layout**  
The layout MUST be functional and usable on viewports ≥375px wide. Content MUST not overflow horizontally. Touch targets MUST be at least 44×44px on mobile.

---

### FR-F1: User Authentication & Session Management

**FR-F1.1 — Login Form**  
The login view MUST include:
- A text input for username (required, max 30 chars)
- A password input (type="password") (required)
- A submit button labeled "Log In" or equivalent
- A link to navigate to the Registration view

**FR-F1.2 — Login Validation**  
On login submission:
1. Both fields MUST be non-empty; inline validation errors shown if empty
2. The username MUST exist in `todoapp_users` localStorage; show "Invalid username or password" if not found (do not reveal which field is wrong)
3. The submitted password MUST hash-match the stored `passwordHash`; show "Invalid username or password" if mismatch
4. On success: create a new session object, write to `todoapp_session`, route to role-appropriate dashboard

**FR-F1.3 — Session Persistence**  
On every page load, the app MUST read `todoapp_session` from localStorage. If a valid session exists, the app MUST route directly to the appropriate dashboard (Admin or User) without showing the login screen.

**FR-F1.4 — Logout**  
A "Log Out" button MUST be visible in the navigation/header of all authenticated views. On click:
1. Remove `todoapp_session` from localStorage
2. Route to `LOGIN` view
3. Clear any in-memory state

**FR-F1.5 — Role-Based Routing**  
After successful login:
- Users with `role: "admin"` MUST be routed to `ADMIN_DASHBOARD`
- Users with `role: "user"` MUST be routed to `USER_DASHBOARD`
- Direct access to admin views by a non-admin user MUST be prevented by re-checking role in the router

---

### FR-F2: Invite Code Registration

**FR-F2.1 — Registration Form**  
The registration view MUST include:
- A text input for desired username (required, 3–30 chars, alphanumeric + underscore only)
- A password input (required, min 8 chars)
- A password confirmation input (required, must match password field)
- A text input for invite code (required)
- A submit button labeled "Create Account" or equivalent
- A link back to the Login view

**FR-F2.2 — Registration Validation**  
On registration submission, the following validations MUST be performed in order:
1. All fields non-empty — inline error if any field is empty
2. Username format valid (3–30 chars, alphanumeric + underscore) — show format error
3. Username not already taken — show "Username already taken"
4. Password meets minimum length (8+ chars) — show length error
5. Password and confirmation match — show mismatch error
6. Invite code exists in `todoapp_invites` — show "Invalid invite code" if not found
7. Invite code not already used (`usedBy === null`) — show "Invite code already used"

**FR-F2.3 — Account Creation**  
On successful validation:
1. Hash the password using a deterministic client-side method (e.g., a simple hash or btoa-based encoding — acceptable for a client-side-only app)
2. Create a new User object and append to `todoapp_users`
3. Update the invite code object: set `usedBy` to new username, set `usedAt` to current ISO timestamp
4. Create a new session for the registered user
5. Write session to `todoapp_session`
6. Route to `USER_DASHBOARD`

**FR-F2.4 — Error Display**  
All registration errors MUST be displayed inline adjacent to the relevant field, not as a generic alert. The form MUST NOT clear successfully filled fields on error.

---

### FR-F3: Admin — Invite Code Management

**FR-F3.1 — Invite Code Generation**  
The admin dashboard MUST include an "Generate Invite Code" button (or equivalent). On click:
1. Generate a unique code: 8 alphanumeric characters (uppercase) or UUID v4 format
2. Create an Invite Code object with `usedBy: null`, `usedAt: null`, `createdAt: now`, `createdBy: currentAdmin`
3. Append to `todoapp_invites` in localStorage
4. Display the new code prominently with a copy-to-clipboard affordance

**FR-F3.2 — Invite Code Copy**  
The generated code MUST be displayed in a text field or card with a "Copy" button. On "Copy" click:
1. Write the code value to the clipboard using `navigator.clipboard.writeText()`
2. Show brief confirmation (e.g., "Copied!" tooltip or button text change for 2 seconds)

**FR-F3.3 — Invite Code List**  
The admin dashboard MUST display all previously generated invite codes in a list or table showing:
- The code value
- Status: "Unused" or "Used"
- If used: the username that redeemed it and the redemption timestamp
- Creation timestamp

**FR-F3.4 — Empty State**  
If no invite codes have been generated, the list area MUST display an appropriate empty state message (e.g., "No invite codes yet. Generate one to invite team members.").

---

### FR-F4: Admin — User Management

**FR-F4.1 — User List**  
The admin dashboard MUST include a section listing all registered users. Each user entry MUST display:
- Username
- Role (Admin / User)
- Registration date
- A "View Todos" action link/button

**FR-F4.2 — View User Todos**  
Clicking "View Todos" for any user navigates to `ADMIN_VIEW_USER` state. This view MUST:
- Display the target user's username in the view header (e.g., "Viewing todos for: jordan")
- Load and display that user's `todoapp_todos_{username}` list
- Render the todos in read-only mode (no add/edit/delete controls)

**FR-F4.3 — Read-Only Enforcement**  
In `ADMIN_VIEW_USER` state, no todo mutation controls (add, edit, delete, complete toggle) MUST be rendered. The view is strictly observational.

**FR-F4.4 — Back Navigation**  
The `ADMIN_VIEW_USER` view MUST include a "Back" link or button that returns to `ADMIN_DASHBOARD`.

**FR-F4.5 — Empty User Todo State**  
If the selected user has no todos, display an appropriate empty state (e.g., "Jordan hasn't added any todos yet.").

---

### FR-F5: User — Todo Management (CRUD)

**FR-F5.1 — Add Todo**  
The user dashboard MUST include an "Add Todo" form or input. Requirements:
- Title input (required, 1–200 chars)
- Optional description textarea (max 1000 chars)
- Submit button labeled "Add" or "Add Todo"
- On submit: validate title non-empty, create Todo object, prepend to user's `todoapp_todos_{username}`, re-render list
- Clear the form after successful add

**FR-F5.2 — Todo List Display**  
All todos MUST be displayed in a list. Each item MUST show:
- A checkbox (or equivalent toggle) for completion status
- The todo title
- The description (if present), collapsed or shown below title
- Edit and Delete action buttons
- Visual distinction for completed items (e.g., strikethrough on title, muted color)

**FR-F5.3 — Complete / Incomplete Toggle**  
Clicking the checkbox/toggle on a todo MUST:
1. Toggle `completed` boolean on the item
2. Update `updatedAt` timestamp
3. Write updated array back to localStorage
4. Re-render the item with updated visual state

**FR-F5.4 — Edit Todo**  
Clicking "Edit" on a todo MUST:
1. Open an inline edit form or modal pre-populated with the current title and description
2. Allow editing of title (required) and description (optional)
3. On save: validate title non-empty, update item, set `updatedAt`, write to localStorage, close edit mode
4. On cancel: discard changes, close edit mode, restore original display

**FR-F5.5 — Delete Todo**  
Clicking "Delete" on a todo MUST:
1. Show a confirmation prompt (inline confirmation button or browser `confirm()` dialog)
2. On confirm: remove item from array, write to localStorage, re-render list
3. On cancel: no action taken

**FR-F5.6 — Filter Tabs**  
The user dashboard MUST include filter controls:
- "All" — shows all todos
- "Active" — shows only `completed: false` todos
- "Completed" — shows only `completed: true` todos
- Active filter tab MUST be visually highlighted
- Filter state MUST update list display immediately on selection

**FR-F5.7 — Item Count**  
The user dashboard MUST display a count summary, e.g., "X of Y completed" or "X active items". This MUST update dynamically with any todo mutation.

**FR-F5.8 — Empty State**  
If the filtered list is empty, display an appropriate empty state message:
- "All" + no todos: "No todos yet. Add your first task above."
- "Active" + none active: "All tasks are complete! 🎉"
- "Completed" + none completed: "No completed tasks yet."

**FR-F5.9 — Sort Order**  
Todos MUST be displayed sorted by `createdAt` descending (newest first) within each filter view. Completed items MAY be visually separated (shown at bottom) as a secondary sort.

---

### FR-F6: Admin — Todo Visibility (Read-Only)

Covered by FR-F4.2, FR-F4.3, FR-F4.4, and FR-F4.5. See Section 3 FR-F4 for full specification.

---

### FR-F7: Data Persistence Layer

**FR-F7.1 — Write Operations**  
Every mutation to application data (add/edit/delete todo, register user, generate invite, login/logout) MUST immediately write the updated data to localStorage. No in-memory-only state that is not reflected in localStorage.

**FR-F7.2 — Read Operations**  
On page load and on every view transition, the application MUST read required data from localStorage to initialize the in-memory state. The app MUST NOT rely on stale in-memory state after a page reload.

**FR-F7.3 — Data Integrity**  
If any localStorage key is missing or contains invalid JSON on parse, the application MUST:
1. Treat the key as empty/default (not throw an exception)
2. Log a console warning for diagnostic purposes
3. Continue operating with a clean default state for that data type

**FR-F7.4 — Key Namespace**  
All localStorage keys MUST be prefixed with `todoapp_` to avoid collisions with other apps on the same origin.

---

### FR-F8: Default Admin Bootstrapping

**FR-F8.1 — First-Run Detection**  
On application load, before rendering any view, the app MUST check whether `todoapp_users` exists and contains at least one entry. If the array is empty or the key is missing, the app is in "first-run" state.

**FR-F8.2 — Seed Admin Account**  
In first-run state, the app MUST automatically create the following admin account and write it to `todoapp_users`:
- `username`: `admin`
- `password`: `admin123` (stored as hash)
- `role`: `admin`
- `registeredAt`: current ISO timestamp
- `inviteCode`: `null`

**FR-F8.3 — First-Run Notice**  
When the app is in first-run state (or on the first render of the login view after seeding), it MUST display a visible notice/banner showing:
- "First time setup: Login with admin / admin123"
- Or equivalent clear, friendly message
- The notice SHOULD be dismissible or auto-hide after successful login

**FR-F8.4 — Idempotency**  
The seeding logic MUST be idempotent. If `todoapp_users` already contains data, no seed account MUST be created or overwrite existing data.

---

## 4. UI Component Specifications

### 4.1 Navigation Header
- Present on all authenticated views
- Contains: App name/logo, current username + role badge, "Log Out" button
- Visually distinct for admin vs. user sessions (e.g., color accent or role label)

### 4.2 Form Components
- All form inputs MUST have associated `<label>` elements with matching `for`/`id` attributes
- Required fields MUST be visually marked (asterisk or equivalent)
- Inline validation error messages MUST appear below the relevant field in red/error color
- Submit buttons MUST be disabled or show loading state while processing to prevent double-submit

### 4.3 Empty States
- All list views (todos, users, invite codes) MUST have a designed empty state
- Empty states MUST be visually distinct from loading states
- Empty states MUST include a brief action prompt where applicable

### 4.4 Confirmation Dialogs
- Destructive actions (delete todo) MUST require confirmation
- Confirmation may use: inline "Are you sure? [Yes] [No]" or browser `confirm()` — both acceptable

---

## 5. Error Handling Specifications

| Scenario | User-Facing Message |
|---|---|
| Login — username not found | "Invalid username or password." |
| Login — password mismatch | "Invalid username or password." |
| Registration — username taken | "This username is already taken. Please choose another." |
| Registration — invalid invite code | "Invite code not found. Please check the code and try again." |
| Registration — used invite code | "This invite code has already been used." |
| Registration — passwords don't match | "Passwords do not match." |
| Registration — password too short | "Password must be at least 8 characters." |
| Todo add — empty title | "Please enter a title for your todo." |
| Todo edit — empty title | "Todo title cannot be empty." |
| localStorage parse failure | (console warning only; user sees empty state — no visible error) |
| Clipboard copy failure | "Could not copy — please copy the code manually." |

---

## 6. Acceptance Criteria Summary

| Feature | Key Acceptance Criteria |
|---|---|
| F0 | App loads in single HTML file; all views toggle without page reload; Outfit font applied |
| F1 | Login with valid credentials routes to correct dashboard; invalid login shows error; logout clears session |
| F2 | Valid invite code + unique username creates account; invalid/used code shows appropriate error |
| F3 | Admin generates code → code appears in list as Unused; code can be copied; code used by user → shows as Used |
| F4 | All users visible in admin panel; "View Todos" shows correct user's items in read-only mode |
| F5 | Add/edit/delete/complete todos persist across page refresh; filters work correctly |
| F6 | Admin can view any user's todos; no edit/delete controls present in admin view mode |
| F7 | All data survives page refresh; missing localStorage key does not crash app |
| F8 | First load creates admin/admin123 account; first-run notice displayed; subsequent loads do not re-seed |

---

*Document generated by Pivota Spec FRD Generator | TodoApp v1.0 | 2026-04-15*

# User Stories
## Todo List App with User Management (TodoApp)

**Version:** 1.0  
**Date:** 2026-04-15  
**Status:** Validated  
**Sources:** PRD-TodoApp.md, FRD-TodoApp.md, PERSONAS-TodoApp.md  

---

## 1. Overview

This document captures user stories for all PRD features (F0–F8). Stories are written from the perspective of the personas defined in PERSONAS-TodoApp.md. Each story includes acceptance criteria (AC) and references the relevant PRD feature ID.

**Persona Reference:**
- **Alex** = PER-01 (Team Admin)
- **Jordan** = PER-02 (Everyday User)
- **Casey** = PER-03 (Non-Technical Invitee)

---

## 2. Epic: Application Shell & Navigation (F0)

### US-F0-01: Single-Page Experience
**As a** Jordan (everyday user),  
**I want** the app to switch between views (login, my todo list, etc.) without the page reloading,  
**So that** my experience feels fast and I never lose my place.

**Acceptance Criteria:**
- AC1: All view transitions happen without a browser navigation event
- AC2: The browser back button does not cause unexpected behavior (or navigates predictably)
- AC3: The active view is determined by in-memory app state, not URL changes

**PRD Feature:** F0

---

### US-F0-02: Outfit Typography
**As a** Casey (non-technical invitee),  
**I want** the app to use a clean, modern font,  
**So that** it feels polished and easy to read.

**Acceptance Criteria:**
- AC1: The Outfit font family is loaded from Google Fonts CDN
- AC2: All visible text renders in Outfit; fallback to system sans-serif if CDN unavailable
- AC3: Font renders at appropriate sizes (minimum 14px body text)

**PRD Feature:** F0

---

### US-F0-03: Responsive Layout
**As a** Jordan (everyday user),  
**I want** the app to work on my phone as well as my laptop,  
**So that** I can check and update my todos from any device.

**Acceptance Criteria:**
- AC1: App is fully functional at 375px viewport width
- AC2: No horizontal overflow or broken layouts at 375px, 768px, or 1280px
- AC3: All interactive touch targets are at least 44×44px

**PRD Feature:** F0

---

## 3. Epic: User Authentication & Session Management (F1)

### US-F1-01: Login with Valid Credentials
**As a** Jordan (everyday user),  
**I want** to log in with my username and password,  
**So that** I can access my personal todo list.

**Acceptance Criteria:**
- AC1: Login form shows username input, password input, and submit button
- AC2: On successful login, I am routed to my user dashboard
- AC3: My username is visible in the app header after login

**PRD Feature:** F1

---

### US-F1-02: Login Error for Invalid Credentials
**As a** Casey (non-technical invitee),  
**I want** to see a helpful error message if I type my credentials wrong,  
**So that** I know what to fix without being confused.

**Acceptance Criteria:**
- AC1: If username doesn't exist, show "Invalid username or password" (do not reveal which)
- AC2: If password is wrong, show same generic message
- AC3: Error is displayed inline in the form, not as a browser alert
- AC4: Form fields retain their values after an error (don't clear the username)

**PRD Feature:** F1

---

### US-F1-03: Auto-Login on Return Visit
**As a** Jordan (everyday user),  
**I want** the app to remember my login when I close and reopen my browser,  
**So that** I don't have to enter my credentials every time.

**Acceptance Criteria:**
- AC1: On page load, if a valid session exists in localStorage, route to the appropriate dashboard
- AC2: Login page is NOT shown if a valid session exists
- AC3: Session persists across tab close, browser close, and OS restart

**PRD Feature:** F1

---

### US-F1-04: Logout
**As a** Alex (admin),  
**I want** to log out of the app,  
**So that** my session is cleared and the next person who opens the browser sees the login screen.

**Acceptance Criteria:**
- AC1: A "Log Out" button is visible in the header on all authenticated views
- AC2: Clicking logout removes the session from localStorage
- AC3: App immediately routes to the login view
- AC4: Navigating back (browser back) after logout shows login view, not the dashboard

**PRD Feature:** F1

---

### US-F1-05: Role-Based Routing After Login
**As a** Alex (admin),  
**I want** to be taken to the admin dashboard automatically when I log in,  
**So that** I don't have to navigate there manually.

**Acceptance Criteria:**
- AC1: Admin users (`role: "admin"`) are routed to ADMIN_DASHBOARD after login
- AC2: Regular users (`role: "user"`) are routed to USER_DASHBOARD after login
- AC3: A non-admin user who somehow navigates to an admin URL/state is redirected to USER_DASHBOARD

**PRD Feature:** F1

---

## 4. Epic: Invite Code Registration (F2)

### US-F2-01: Register with a Valid Invite Code
**As a** Jordan (everyday user),  
**I want** to register a new account using an invite code,  
**So that** I can get access to the app.

**Acceptance Criteria:**
- AC1: Registration form has fields for username, password, password confirmation, and invite code
- AC2: On successful registration, a new user account is created in localStorage
- AC3: The used invite code is marked as used with my username and timestamp
- AC4: I am automatically logged in and routed to the user dashboard
- AC5: I do not need to log in separately after registering

**PRD Feature:** F2

---

### US-F2-02: Registration Fails with Invalid Invite Code
**As a** Casey (non-technical invitee),  
**I want** a clear error message if I type the invite code wrong,  
**So that** I know what to fix and can try again.

**Acceptance Criteria:**
- AC1: If invite code is not found in the system, show "Invite code not found. Please check the code and try again."
- AC2: Error is shown inline below the invite code field
- AC3: All other successfully filled fields (username, password) are NOT cleared
- AC4: I can correct the code and resubmit without refreshing

**PRD Feature:** F2

---

### US-F2-03: Registration Fails with Already-Used Invite Code
**As a** Casey (non-technical invitee),  
**I want** to know if my invite code has already been used,  
**So that** I can ask the admin for a new one instead of trying the same code repeatedly.

**Acceptance Criteria:**
- AC1: If invite code exists but `usedBy` is not null, show "This invite code has already been used."
- AC2: The message does not reveal who used the code
- AC3: Error appears inline below the invite code field

**PRD Feature:** F2

---

### US-F2-04: Registration Validation for Username and Password
**As a** Jordan (everyday user),  
**I want** clear validation messages for username and password requirements,  
**So that** I know the rules and can fill the form correctly.

**Acceptance Criteria:**
- AC1: Username must be 3–30 chars, alphanumeric + underscore; show format error otherwise
- AC2: If username is already taken, show "This username is already taken. Please choose another."
- AC3: Password must be at least 8 characters; show length error otherwise
- AC4: If password and confirmation don't match, show "Passwords do not match."
- AC5: Errors appear inline below the relevant field
- AC6: Only one error is shown per field at a time

**PRD Feature:** F2

---

## 5. Epic: Admin — Invite Code Management (F3)

### US-F3-01: Generate a New Invite Code
**As a** Alex (admin),  
**I want** to generate a new invite code with one click,  
**So that** I can quickly share it with someone I want to invite.

**Acceptance Criteria:**
- AC1: Admin dashboard has a "Generate Invite Code" button
- AC2: Clicking generates a unique 8-char alphanumeric code
- AC3: The new code is immediately displayed in a text field or card
- AC4: A "Copy" button copies the code to the clipboard
- AC5: The code is simultaneously added to the invite code list as "Unused"

**PRD Feature:** F3

---

### US-F3-02: Copy Invite Code to Clipboard
**As a** Alex (admin),  
**I want** to copy an invite code to my clipboard in one click,  
**So that** I can paste it into a message without retyping it.

**Acceptance Criteria:**
- AC1: Each invite code (new and existing unused ones) has a "Copy" button
- AC2: Clicking "Copy" writes the code to clipboard via Clipboard API
- AC3: Button shows brief "Copied!" confirmation for 2 seconds
- AC4: If clipboard API fails, display "Could not copy — please copy manually"

**PRD Feature:** F3

---

### US-F3-03: View All Invite Codes with Status
**As a** Alex (admin),  
**I want** to see a list of all invite codes I've generated and whether each has been used,  
**So that** I can track who has registered and which codes are still available.

**Acceptance Criteria:**
- AC1: Invite code list shows all codes generated by the admin
- AC2: Each code shows: code value, status (Unused / Used), creation timestamp
- AC3: Used codes show the username that redeemed them and the redemption timestamp
- AC4: List is sorted with newest codes at top
- AC5: If no codes exist, show "No invite codes yet. Generate one to invite team members."

**PRD Feature:** F3

---

## 6. Epic: Admin — User Management (F4)

### US-F4-01: View All Registered Users
**As a** Alex (admin),  
**I want** to see a list of all users who have registered,  
**So that** I can confirm who is in the system and check their registration dates.

**Acceptance Criteria:**
- AC1: Admin dashboard includes a section listing all registered users
- AC2: Each user entry shows: username, role, registration date
- AC3: Users are listed in alphabetical or registration-date order
- AC4: Admin's own account is visible in the list

**PRD Feature:** F4

---

### US-F4-02: Navigate to a User's Todo List
**As a** Alex (admin),  
**I want** to click on a user and see their todo list,  
**So that** I can check their task progress without asking them.

**Acceptance Criteria:**
- AC1: Each user in the list has a "View Todos" button or link
- AC2: Clicking navigates to a read-only view of that user's todos
- AC3: The view header clearly identifies whose todos are being shown (e.g., "Viewing todos for: jordan")
- AC4: A "Back" button returns to the admin dashboard

**PRD Feature:** F4

---

## 7. Epic: User — Todo Management (F5)

### US-F5-01: Add a New Todo
**As a** Jordan (everyday user),  
**I want** to quickly add a new task to my list,  
**So that** I can capture things I need to do before I forget them.

**Acceptance Criteria:**
- AC1: User dashboard has an "Add Todo" form with a title input (required) and optional description
- AC2: Pressing Enter or clicking "Add" submits the form
- AC3: New todo appears at the top of the list immediately
- AC4: Todo is saved to localStorage (`todoapp_todos_{username}`)
- AC5: Form clears after successful submission

**PRD Feature:** F5

---

### US-F5-02: Mark a Todo as Complete
**As a** Jordan (everyday user),  
**I want** to check off a completed task,  
**So that** I can see at a glance what I've accomplished.

**Acceptance Criteria:**
- AC1: Each todo has a checkbox or toggle control
- AC2: Clicking the checkbox toggles the `completed` state
- AC3: Completed todos are visually distinguished (strikethrough title, muted color)
- AC4: The change persists in localStorage immediately
- AC5: The item count summary updates to reflect the new completion state

**PRD Feature:** F5

---

### US-F5-03: Edit a Todo
**As a** Jordan (everyday user),  
**I want** to edit the title or description of an existing todo,  
**So that** I can correct typos or update the task details.

**Acceptance Criteria:**
- AC1: Each todo has an "Edit" button
- AC2: Clicking "Edit" opens an inline edit form or modal pre-filled with current values
- AC3: Saving the edit updates the todo in the list and in localStorage
- AC4: `updatedAt` timestamp is updated on save
- AC5: Clicking "Cancel" discards changes and returns to normal view
- AC6: Empty title on save shows an inline error; save is blocked

**PRD Feature:** F5

---

### US-F5-04: Delete a Todo
**As a** Jordan (everyday user),  
**I want** to delete a task I no longer need,  
**So that** my list stays clean and relevant.

**Acceptance Criteria:**
- AC1: Each todo has a "Delete" button
- AC2: Clicking "Delete" shows a confirmation prompt ("Are you sure?")
- AC3: Confirming removes the todo from the list and from localStorage
- AC4: Cancelling the confirmation takes no action
- AC5: The item count summary updates after deletion

**PRD Feature:** F5

---

### US-F5-05: Filter Todos by Status
**As a** Jordan (everyday user),  
**I want** to filter my todos to show only active or completed tasks,  
**So that** I can focus on what still needs to be done without the clutter of completed items.

**Acceptance Criteria:**
- AC1: Filter controls with "All", "Active", and "Completed" options are visible
- AC2: Clicking each filter immediately updates the list to show only matching items
- AC3: The active filter tab/button is visually highlighted
- AC4: Item count summary reflects the count for the current filter
- AC5: Each filter shows an appropriate empty state if no items match

**PRD Feature:** F5

---

### US-F5-06: View Item Count Summary
**As a** Jordan (everyday user),  
**I want** to see how many tasks I have and how many I've completed,  
**So that** I can get a quick sense of my progress.

**Acceptance Criteria:**
- AC1: A count summary is displayed (e.g., "3 of 7 completed" or "4 active items")
- AC2: The count updates immediately after any add, delete, or completion toggle
- AC3: Count reflects the currently active filter

**PRD Feature:** F5

---

## 8. Epic: Admin — Todo Visibility — Read-Only (F6)

### US-F6-01: View a User's Todos in Read-Only Mode
**As a** Alex (admin),  
**I want** to view a team member's todo list without any ability to edit or delete their items,  
**So that** I can check their progress without risking accidental data changes.

**Acceptance Criteria:**
- AC1: Admin todo view shows all todos for the selected user
- AC2: No "Edit", "Delete", or completion-toggle controls are rendered
- AC3: Completed items are still visually distinguished (strikethrough, etc.)
- AC4: A clear label or banner identifies the view as read-only (e.g., "Viewing as Admin — Read Only")
- AC5: Empty state shown if user has no todos

**PRD Feature:** F6

---

## 9. Epic: Data Persistence (F7)

### US-F7-01: Data Survives Page Refresh
**As a** Casey (non-technical invitee),  
**I want** my todos to still be there after I close and reopen my browser tab,  
**So that** I can rely on this tool for real task tracking.

**Acceptance Criteria:**
- AC1: All todos added in a session are readable after a full page refresh
- AC2: Completion status, titles, and descriptions are preserved exactly
- AC3: No blank or corrupt data appears after refresh
- AC4: Auto-login resumes my session so I go directly to my dashboard

**PRD Feature:** F7

---

### US-F7-02: Graceful Recovery from Missing Data
**As a** Casey (non-technical invitee),  
**I want** the app to not crash or show a broken screen if something goes wrong with my stored data,  
**So that** I can still use the app even if I've cleared my browser.

**Acceptance Criteria:**
- AC1: If localStorage data is missing or unparseable, the app shows an appropriate empty state (not a blank/broken screen)
- AC2: No unhandled JavaScript errors are thrown to the console in this scenario
- AC3: A missing session results in showing the login view, not an error page

**PRD Feature:** F7

---

## 10. Epic: Default Admin Bootstrapping (F8)

### US-F8-01: First-Run Setup Happens Automatically
**As a** Alex (admin),  
**I want** the app to create a default admin account the first time it's opened,  
**So that** I can log in immediately without any setup process.

**Acceptance Criteria:**
- AC1: On first load (empty localStorage), a default admin account exists with username `admin` and password `admin123`
- AC2: I can log in with these credentials immediately
- AC3: A visible notice on the login screen shows the default credentials
- AC4: After I log in and set up the app, subsequent page loads do not recreate or modify the admin account

**PRD Feature:** F8

---

### US-F8-02: First-Run Notice on Login Screen
**As a** Alex (admin),  
**I want** to see the default login credentials on the screen the first time I open the app,  
**So that** I don't have to look up documentation just to log in.

**Acceptance Criteria:**
- AC1: A notice/banner is displayed on the login screen showing default credentials (admin / admin123)
- AC2: The notice is prominently visible — not hidden in a footnote
- AC3: The notice is only shown when the app has not been previously set up (first-run state)
- AC4: The notice disappears or is dismissible after the first successful admin login

**PRD Feature:** F8

---

## 11. Story Summary Table

| Story ID | Epic | Persona | Priority |
|---|---|---|---|
| US-F0-01 | App Shell | Jordan | P0 |
| US-F0-02 | App Shell | Casey | P0 |
| US-F0-03 | App Shell | Jordan | P0 |
| US-F1-01 | Authentication | Jordan | P0 |
| US-F1-02 | Authentication | Casey | P0 |
| US-F1-03 | Authentication | Jordan | P0 |
| US-F1-04 | Authentication | Alex | P0 |
| US-F1-05 | Authentication | Alex | P0 |
| US-F2-01 | Registration | Jordan | P0 |
| US-F2-02 | Registration | Casey | P0 |
| US-F2-03 | Registration | Casey | P0 |
| US-F2-04 | Registration | Jordan | P0 |
| US-F3-01 | Invite Codes | Alex | P0 |
| US-F3-02 | Invite Codes | Alex | P0 |
| US-F3-03 | Invite Codes | Alex | P0 |
| US-F4-01 | User Management | Alex | P1 |
| US-F4-02 | User Management | Alex | P1 |
| US-F5-01 | Todo CRUD | Jordan | P0 |
| US-F5-02 | Todo CRUD | Jordan | P0 |
| US-F5-03 | Todo CRUD | Jordan | P0 |
| US-F5-04 | Todo CRUD | Jordan | P0 |
| US-F5-05 | Todo CRUD | Jordan | P0 |
| US-F5-06 | Todo CRUD | Jordan | P0 |
| US-F6-01 | Admin Visibility | Alex | P1 |
| US-F7-01 | Persistence | Casey | P0 |
| US-F7-02 | Persistence | Casey | P0 |
| US-F8-01 | Bootstrapping | Alex | P0 |
| US-F8-02 | Bootstrapping | Alex | P0 |

**Total Stories:** 28  
**P0 Stories:** 24  
**P1 Stories:** 4  

---

*Document generated by Pivota Spec UserStories Generator | TodoApp v1.0 | 2026-04-15*

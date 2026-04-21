# Roadmap: Todo List App with User Management

## Overview

This project delivers a pure-frontend SPA (HTML/CSS/JS) for team task management with invite-based user access and role-based visibility. The journey moves from establishing the app shell and data foundation, through authentication and invite flows, to todo CRUD for users and full admin visibility — yielding a complete, self-contained todo app stored entirely in localStorage.

## Phases

**Phase Numbering:**
- Integer phases (1, 2, 3): Planned milestone work
- Decimal phases (2.1, 2.2): Urgent insertions (marked with INSERTED)

Decimal phases appear between their surrounding integers in numeric order.

- [x] **Phase 1: Foundation** - App shell, data persistence layer, and admin bootstrapping
- [ ] **Phase 2: Authentication** - Login, session management, logout, and role-based routing
- [ ] **Phase 3: Invite System** - Invite code generation (admin) and registration via invite code (user)
- [ ] **Phase 4: Todo Management** - Full user-facing todo CRUD with filters and persistence
- [ ] **Phase 5: Admin Visibility** - Admin user list and read-only todo view for any user

## Phase Details

### Phase 1: Foundation
**Status**: pr creation failed
**Goal**: The application shell is in place — the SPA loads, navigates between views, persists data to localStorage, and bootstraps the default admin account on first run
**Depends on**: Nothing (first phase)
**Requirements**: F0, F7, F8
**Success Criteria** (what must be TRUE):
  1. Opening the app in a browser shows a styled LOGIN view using the Outfit font, with no console errors
  2. The app displays correctly on a 375px-wide viewport (mobile-minimum responsive)
  3. On first load with no existing data, a default admin account (admin / admin123) is present and usable
  4. A first-run notice or indicator is shown to signal that the app has been initialized
  5. All localStorage keys are namespaced; a corrupt/invalid localStorage entry does not crash the app
**Plans**: 3 plans

Plans:
- [x] 01-01-PLAN.md — HTML scaffold, Outfit font, CSS design system with custom properties
- [x] 01-02-PLAN.md — Config, Storage, hashPassword, generateUUID, Bootstrap (data layer + admin seed)
- [x] 01-03-PLAN.md — Router state machine, UI render functions, async init() entry point + human verify

### Phase 2: Authentication
**Goal**: Users can securely log in, maintain their session across refreshes, and be routed to the correct dashboard by role; users can log out from any view
**Depends on**: Phase 1
**Requirements**: F1
**Success Criteria** (what must be TRUE):
  1. User submits valid credentials on the LOGIN view and is taken to their role-appropriate dashboard (admin → ADMIN_DASHBOARD, user → USER_DASHBOARD)
  2. After logging in, refreshing the browser keeps the user logged in on the correct dashboard
  3. A user with invalid credentials sees an error message and cannot proceed
  4. Clicking logout from any dashboard clears the session and returns the user to the LOGIN view
**Plans**: 2 plans

Plans:
- [ ] 02-01-PLAN.md — Auth namespace, escapeHtml utility, and dashboard CSS
- [ ] 02-02-PLAN.md — Dashboard render functions, login submit handler, logout binding + human verify

### Phase 3: Invite System
**Goal**: The admin can generate and share invite codes; a new user can register an account using a valid invite code and immediately access the app
**Depends on**: Phase 2
**Requirements**: F2, F3
**Success Criteria** (what must be TRUE):
  1. Admin can generate a new unique invite code from the admin dashboard and copy it to the clipboard
  2. Admin sees a list of all invite codes showing which are used and which are still available
  3. A visitor can navigate to the REGISTER view, enter a valid invite code plus credentials, and create an account
  4. Attempting to register with an invalid or already-used invite code shows an error and does not create an account
  5. A newly registered user is immediately logged in and taken to their USER_DASHBOARD
**Plans**: 3 plans

Plans:
- [ ] 03-01-PLAN.md — InviteCodes namespace (create/find/markUsed/list) + Users.register() with 7-step validation chain
- [ ] 03-02-PLAN.md — Replace renderRegister() + renderAdminDashboard() stubs; wire all Phase 3 bindEvents() handlers
- [ ] 03-03-PLAN.md — Human verify: all 5 Phase 3 success criteria confirmed in browser

### Phase 4: Todo Management
**Goal**: Logged-in users can fully manage their todo list — create, edit, complete, and delete items — with the list persisted across sessions and filterable by status
**Depends on**: Phase 2
**Requirements**: F5
**Success Criteria** (what must be TRUE):
  1. User can add a new todo item that immediately appears in their list sorted newest-first
  2. User can edit the text of an existing todo item in place
  3. User can mark a todo as complete/incomplete and it moves between Active and Completed filter tabs
  4. User can delete a todo item and it is permanently removed
  5. All filter tabs (All / Active / Completed) show the correct items and display an empty-state message when there are no matching todos; item count updates correctly
  6. After refreshing the browser, all todos are still present exactly as left
**Plans**: TBD

### Phase 5: Admin Visibility
**Goal**: The admin can see every registered user and inspect any user's todo list in a read-only view, with easy navigation back to the admin dashboard
**Depends on**: Phase 3, Phase 4
**Requirements**: F4, F6
**Success Criteria** (what must be TRUE):
  1. Admin dashboard shows a list of all registered user accounts
  2. Admin can click on any user to open ADMIN_VIEW_USER and see that user's todos exactly as the user sees them
  3. No edit, delete, or status-change controls are visible or functional in the admin's read-only view of a user's todos
  4. Admin can navigate back from ADMIN_VIEW_USER to ADMIN_DASHBOARD without losing state
  5. A regular (non-admin) user navigating directly to the admin view is blocked and redirected to their USER_DASHBOARD
**Plans**: TBD

## Progress

**Execution Order:**
Phases execute in numeric order: 1 → 2 → 3 → 4 → 5

| Phase | Plans Complete | Status | Completed |
|-------|----------------|--------|-----------|
| 1. Foundation | 3/3 | Complete | 2026-04-16 |
| 2. Authentication | 0/2 | Not started | - |
| 3. Invite System | 0/3 | Not started | - |
| 4. Todo Management | 0/TBD | Not started | - |
| 5. Admin Visibility | 0/TBD | Not started | - |
# Story Map
## Todo List App with User Management (TodoApp)

**Version:** 1.0  
**Date:** 2026-04-15  
**Status:** Validated  
**Sources:** PERSONAS-TodoApp.md, JTBD-TodoApp.md, JOURNEYS-TodoApp.md, UserStories-TodoApp.md, PRD-TodoApp.md  

---

## 1. Overview

This document maps personas and journey steps to epics, user stories, and releases. It provides a unified view of the product narrative from the user's perspective, organized by the "backbone" (activities) and "walking skeleton" (minimal viable path through the product).

**Persona Reference:**
- Alex = PER-01 (Team Admin)
- Jordan = PER-02 (Everyday User)
- Casey = PER-03 (Non-Technical Invitee)

---

## 2. Product Backbone (Activity Spine)

The product backbone represents the high-level activities users perform, reading left to right in temporal/workflow order:

```
[Set Up App]  →  [Get Access]  →  [Manage Tasks]  →  [Oversee Team]
   (Admin)         (User)            (User)            (Admin)
```

---

## 3. Story Map by Epic

### Epic 1: App Setup & Session (F0, F1, F7, F8)

**Activity:** Set Up App / Authenticate

| Journey Step | User Story | Persona | NaC (Natural Acceptance Criteria) | Release |
|---|---|---|---|---|
| Open app for first time | US-F8-01: First-Run Setup Happens Automatically | Alex | Seeded admin exists on first load; login works immediately | R1 — MVP |
| See default credentials | US-F8-02: First-Run Notice on Login Screen | Alex | Banner shows "admin / admin123" before first login | R1 — MVP |
| Log in | US-F1-01: Login with Valid Credentials | Alex, Jordan | Login routes to correct dashboard by role | R1 — MVP |
| See login error | US-F1-02: Login Error for Invalid Credentials | Casey | Generic error message; no field clearing | R1 — MVP |
| Return to app next day | US-F1-03: Auto-Login on Return Visit | Jordan, Casey | Dashboard loads without login prompt | R1 — MVP |
| Log out | US-F1-04: Logout | Alex, Jordan | Session cleared; login view shown | R1 — MVP |
| Role routing after login | US-F1-05: Role-Based Routing After Login | Alex, Jordan | Admin → admin dashboard; user → user dashboard | R1 — MVP |
| Data survives refresh | US-F7-01: Data Survives Page Refresh | Jordan, Casey | All todos intact after page reload | R1 — MVP |
| Graceful recovery | US-F7-02: Graceful Recovery from Missing Data | Casey | Empty state on missing data; no crash | R1 — MVP |
| Responsive layout | US-F0-03: Responsive Layout | Jordan, Casey | Functional at 375px; no overflow | R1 — MVP |
| Typography | US-F0-02: Outfit Typography | All | Outfit font loaded; fallback to sans-serif | R1 — MVP |
| SPA navigation | US-F0-01: Single-Page Experience | All | No page reloads on view transitions | R1 — MVP |

---

### Epic 2: Invite-Based Registration (F2, F3)

**Activity:** Get Access (User) / Control Access (Admin)

| Journey Step | User Story | Persona | NaC | Release |
|---|---|---|---|---|
| Generate invite code | US-F3-01: Generate a New Invite Code | Alex | Code generated in 1 click; displayed with copy button | R1 — MVP |
| Copy invite code | US-F3-02: Copy Invite Code to Clipboard | Alex | "Copied!" confirmation for 2 seconds | R1 — MVP |
| View all invite codes | US-F3-03: View All Invite Codes with Status | Alex | All codes visible with Unused/Used status | R1 — MVP |
| Register with valid code | US-F2-01: Register with a Valid Invite Code | Jordan, Casey | Account created; redirect to dashboard; code marked used | R1 — MVP |
| Register fails (bad code) | US-F2-02: Registration Fails with Invalid Invite Code | Casey | Inline error; other fields retained | R1 — MVP |
| Register fails (used code) | US-F2-03: Registration Fails with Already-Used Invite Code | Casey | "Code already used" error shown | R1 — MVP |
| Register validation errors | US-F2-04: Registration Validation for Username and Password | Jordan | Per-field inline errors for all constraint violations | R1 — MVP |

---

### Epic 3: Todo Task Management (F5)

**Activity:** Manage Tasks

| Journey Step | User Story | Persona | NaC | Release |
|---|---|---|---|---|
| Add first todo | US-F5-01: Add a New Todo | Jordan, Casey | Todo appears at top; form clears | R1 — MVP |
| Mark todo complete | US-F5-02: Mark a Todo as Complete | Jordan, Casey | Toggle changes visual + persists; count updates | R1 — MVP |
| Edit a todo | US-F5-03: Edit a Todo | Jordan | Pre-filled form; save updates; cancel discards | R1 — MVP |
| Delete a todo | US-F5-04: Delete a Todo | Jordan | Confirmation required; item removed on confirm | R1 — MVP |
| Filter todos | US-F5-05: Filter Todos by Status | Jordan | All/Active/Completed filters work instantly | R1 — MVP |
| View item count | US-F5-06: View Item Count Summary | Jordan, Casey | Count shows completed vs total; updates dynamically | R1 — MVP |

---

### Epic 4: Admin Team Oversight (F4, F6)

**Activity:** Oversee Team

| Journey Step | User Story | Persona | NaC | Release |
|---|---|---|---|---|
| View all users | US-F4-01: View All Registered Users | Alex | All users listed with username, role, date | R2 — Post-MVP |
| Navigate to user todos | US-F4-02: Navigate to a User's Todo List | Alex | Click-through to user's list; back navigation works | R2 — Post-MVP |
| Read-only admin view | US-F6-01: View a User's Todos in Read-Only Mode | Alex | No edit/delete controls; "Read Only" label shown | R2 — Post-MVP |

---

## 4. Release Plan

### Release 1 — MVP (All P0 Features)

**Goal:** A fully working todo app with invite-based registration and admin access control.

**Included Epics:** App Setup & Session (full), Invite-Based Registration (full), Todo Task Management (full)

**Persona Coverage:**
- Alex can set up the app, generate invite codes, and log in/out ✅
- Jordan can register, manage todos (add/edit/delete/complete/filter) ✅
- Casey can register (including error recovery) and manage todos ✅

**Stories in R1:**
All stories from Epics 1, 2, and 3 (25 stories):
US-F0-01, US-F0-02, US-F0-03, US-F1-01 through US-F1-05, US-F2-01 through US-F2-04, US-F3-01 through US-F3-03, US-F5-01 through US-F5-06, US-F7-01, US-F7-02, US-F8-01, US-F8-02

**End-to-End Journey Coverage for R1:**
- JRN-PER01.01 (Setup + Invite Codes): ✅ Complete in R1
- JRN-PER02.01 (Registration + First Todo): ✅ Complete in R1
- JRN-PER02.02 (Daily Task Management): ✅ Complete in R1
- JRN-PER02.03 (Edit and Delete Todos): ✅ Complete in R1
- JRN-PER03.01 (Registration with Error Recovery): ✅ Complete in R1
- JRN-PER03.02 (Daily Routine Todo Use): ✅ Complete in R1

---

### Release 2 — Admin Oversight (P1 Features)

**Goal:** Admin can view all users and inspect any user's task list.

**Included Epics:** Admin Team Oversight

**Persona Coverage:**
- Alex can now complete JRN-PER01.02 (Check Team Progress) ✅
- Alex can now complete JRN-PER01.03 (Verify Registration Status) ✅

**Stories in R2:**
US-F4-01, US-F4-02, US-F6-01

**End-to-End Journey Coverage added in R2:**
- JRN-PER01.02 (Check Team Progress Mid-Sprint): ✅ Complete in R2
- JRN-PER01.03 (Verify Registration Status): ✅ Complete in R2

---

## 5. Natural Acceptance Criteria (NaC) Summary by Persona

NaC statements are derived from JTBD outcomes and describe the minimum behaviors that make each persona feel the product "works."

### Alex (PER-01) — NaC
- Can generate all invite codes and share them in under 5 minutes from first opening the app
- Can see all registered users and their task lists from the admin dashboard
- Default credentials are visible on first load — no documentation needed
- Admin view of user todos never allows accidental edits

### Jordan (PER-02) — NaC
- Can register and add the first task in under 2 minutes
- Todo list is fully intact on return visits (auto-login + persistence)
- Add, complete, edit, delete operations take 1–2 clicks with immediate feedback
- Filter tabs show exactly the right items with an accurate count

### Casey (PER-03) — NaC
- Registration form provides enough guidance to complete it without help
- Error messages use plain language and don't clear filled fields
- Tasks are present every morning without re-entering credentials
- App never shows a blank or broken screen

---

## 6. Story Map Visual Summary

```
ACTIVITY →      Set Up App        Get Access       Manage Tasks      Oversee Team
                (Admin)           (User/Admin)      (User)            (Admin)
─────────────────────────────────────────────────────────────────────────────────
BACKBONE:       Bootstrap &       Register &        Todo CRUD         User Visibility
                Authenticate      Invite Codes      & Filters         & Team Oversight
─────────────────────────────────────────────────────────────────────────────────
R1 - MVP:       US-F8-01/02       US-F3-01/02/03    US-F5-01/02       —
                US-F1-01/02/03    US-F2-01/02/03    US-F5-03/04
                US-F1-04/05       US-F2-04          US-F5-05/06
                US-F0-01/02/03
                US-F7-01/02
─────────────────────────────────────────────────────────────────────────────────
R2 - Post-MVP:  —                 —                 —                 US-F4-01/02
                                                                      US-F6-01
─────────────────────────────────────────────────────────────────────────────────
```

---

*Document generated by Pivota Spec StoryMap Generator | TodoApp v1.0 | 2026-04-15*

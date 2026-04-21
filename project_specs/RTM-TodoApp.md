# Requirements Traceability Matrix (RTM)
## Todo List App with User Management (TodoApp)

**Version:** 1.0  
**Date:** 2026-04-15  
**Status:** Final  
**Sources:** PRD-TodoApp.md, FRD-TodoApp.md, TechArch-TodoApp.md, UserStories-TodoApp.md  

---

## 1. Overview

This RTM provides bidirectional traceability across all TodoApp specification documents. Every PRD feature traces forward to FRD requirements, TechArch specs, and User Stories. The matrix enables:

- **Forward traceability:** Verify every business requirement has functional specs and stories
- **Backward traceability:** Verify every story and spec traces to a business requirement
- **Test coverage:** Identify which user stories provide test coverage for each requirement
- **Impact analysis:** Understand blast radius of changes to any requirement

---

## 2. Master Traceability Matrix

| PRD Feature | PRD Priority | FRD Requirements | TechArch Sections | User Stories | Test Coverage |
|---|---|---|---|---|---|
| **F0: App Shell & Navigation** | P0 | FR-F0.1, FR-F0.2, FR-F0.3, FR-F0.4, FR-F0.5 | §1.1 Architecture Philosophy, §1.3 File Structure, §3.1 View State Machine, §3.3 CSS Architecture | US-F0-01, US-F0-02, US-F0-03 | ✅ 3 stories |
| **F1: Authentication & Session** | P0 | FR-F1.1, FR-F1.2, FR-F1.3, FR-F1.4, FR-F1.5 | §3.1 View State Machine (login transitions), §2.4 Password Hashing | US-F1-01, US-F1-02, US-F1-03, US-F1-04, US-F1-05 | ✅ 5 stories |
| **F2: Invite Code Registration** | P0 | FR-F2.1, FR-F2.2, FR-F2.3, FR-F2.4 | §2.2 InviteCode interface, §2.5 UUID Generation | US-F2-01, US-F2-02, US-F2-03, US-F2-04 | ✅ 4 stories |
| **F3: Admin — Invite Code Mgmt** | P0 | FR-F3.1, FR-F3.2, FR-F3.3, FR-F3.4 | §1.4 Module Org (InviteCodes module), §2.2 InviteCode interface, §2.5 UUID Generation | US-F3-01, US-F3-02, US-F3-03 | ✅ 3 stories |
| **F4: Admin — User Management** | P1 | FR-F4.1, FR-F4.2, FR-F4.3, FR-F4.4, FR-F4.5 | §3.1 View State Machine (ADMIN_VIEW_USER), §3.2 UI Rendering Functions | US-F4-01, US-F4-02 | ✅ 2 stories |
| **F5: User — Todo Management** | P0 | FR-F5.1, FR-F5.2, FR-F5.3, FR-F5.4, FR-F5.5, FR-F5.6, FR-F5.7, FR-F5.8, FR-F5.9 | §1.4 Module Org (Todos module), §2.2 Todo interface, §2.1 localStorage key `todoapp_todos_{username}` | US-F5-01, US-F5-02, US-F5-03, US-F5-04, US-F5-05, US-F5-06 | ✅ 6 stories |
| **F6: Admin — Todo Visibility** | P1 | FR-F4.2, FR-F4.3, FR-F4.4, FR-F4.5 (covered by F4) | §3.4 Security (read-only enforcement) | US-F6-01 | ✅ 1 story |
| **F7: Data Persistence Layer** | P0 | FR-F7.1, FR-F7.2, FR-F7.3, FR-F7.4 | §1.1 Architecture Philosophy, §2.1 localStorage Schema, §2.3 Data Access Patterns | US-F7-01, US-F7-02 | ✅ 2 stories |
| **F8: Default Admin Bootstrap** | P0 | FR-F8.1, FR-F8.2, FR-F8.3, FR-F8.4 | §2.3 Data Access Patterns (seed logic) | US-F8-01, US-F8-02 | ✅ 2 stories |

**Totals:** 9 Features → 38 FRD Requirements → 28 User Stories

---

## 3. FRD Requirements Traceability

### F0 — Application Shell & Navigation

| FRD Req ID | Requirement Summary | Traced To (Stories) | Test Notes |
|---|---|---|---|
| FR-F0.1 | Single HTML document; all views toggled | US-F0-01 | Verify no page reload on navigation |
| FR-F0.2 | 5 discrete view states; exactly one active | US-F0-01 | Verify each state renders correctly |
| FR-F0.3 | Router driven by `currentView` state variable | US-F0-01 | Verify router reads localStorage on load |
| FR-F0.4 | Outfit font from Google Fonts CDN; sans-serif fallback | US-F0-02 | Inspect computed font in browser DevTools |
| FR-F0.5 | Functional layout ≥375px; 44×44px touch targets | US-F0-03 | Test at 375px viewport; measure targets |

### F1 — Authentication & Session Management

| FRD Req ID | Requirement Summary | Traced To (Stories) | Test Notes |
|---|---|---|---|
| FR-F1.1 | Login form: username + password + submit + register link | US-F1-01 | Render test; all elements present |
| FR-F1.2 | Login validation: empty, not found, hash mismatch → error | US-F1-01, US-F1-02 | Test all 3 failure modes + success |
| FR-F1.3 | Session persistence: auto-route on page load | US-F1-03 | Set session in localStorage; reload; verify |
| FR-F1.4 | Logout: clear session, route to LOGIN | US-F1-04 | Click logout; verify localStorage cleared |
| FR-F1.5 | Role routing: admin → ADMIN_DASHBOARD; user → USER_DASHBOARD | US-F1-05 | Login with each role; verify route |

### F2 — Invite Code Registration

| FRD Req ID | Requirement Summary | Traced To (Stories) | Test Notes |
|---|---|---|---|
| FR-F2.1 | Registration form: username, password, confirm, invite code | US-F2-01 | All fields present and labeled |
| FR-F2.2 | Validation sequence: 7 ordered checks | US-F2-02, US-F2-03, US-F2-04 | Test each failure case individually |
| FR-F2.3 | Account creation: hash password, create user, mark invite used, auto-login | US-F2-01 | Verify localStorage after registration |
| FR-F2.4 | Inline error display; no field clearing on error | US-F2-02, US-F2-03, US-F2-04 | Submit with error; verify fields retained |

### F3 — Admin Invite Code Management

| FRD Req ID | Requirement Summary | Traced To (Stories) | Test Notes |
|---|---|---|---|
| FR-F3.1 | Generate button → unique 8-char code → localStorage append → display | US-F3-01 | Verify code in localStorage after click |
| FR-F3.2 | Copy button → clipboard write → "Copied!" 2-second confirm | US-F3-02 | Mock clipboard; verify success/failure states |
| FR-F3.3 | Invite code list: code, status, redeemer, timestamps | US-F3-03 | Verify all columns render correctly |
| FR-F3.4 | Empty state when no codes exist | US-F3-03 | Clear `todoapp_invites`; verify empty state |

### F4 — Admin User Management

| FRD Req ID | Requirement Summary | Traced To (Stories) | Test Notes |
|---|---|---|---|
| FR-F4.1 | User list: username, role, date, "View Todos" button | US-F4-01 | Verify all columns; all users listed |
| FR-F4.2 | View Todos → ADMIN_VIEW_USER with correct user's todos | US-F4-02 | Click "View Todos"; verify username in header |
| FR-F4.3 | Read-only enforcement: no add/edit/delete/toggle in admin view | US-F6-01 | Inspect DOM; verify no mutation controls |
| FR-F4.4 | Back navigation from ADMIN_VIEW_USER → ADMIN_DASHBOARD | US-F4-02 | Click Back; verify route |
| FR-F4.5 | Empty state for user with no todos | US-F6-01 | View user with empty todos; verify message |

### F5 — User Todo Management (CRUD)

| FRD Req ID | Requirement Summary | Traced To (Stories) | Test Notes |
|---|---|---|---|
| FR-F5.1 | Add form: title (required), description (optional), submit | US-F5-01 | Submit valid; verify item in list + localStorage |
| FR-F5.2 | List display: checkbox, title, description, edit/delete, completion style | US-F5-01, US-F5-02 | Verify all elements rendered per item |
| FR-F5.3 | Complete toggle: updates completed, updatedAt, localStorage | US-F5-02 | Toggle; verify localStorage `completed` field |
| FR-F5.4 | Edit: inline form pre-filled; save updates; cancel discards | US-F5-03 | Edit and save; edit and cancel; verify both |
| FR-F5.5 | Delete: confirmation required; removes on confirm | US-F5-04 | Confirm delete; cancel delete; verify both |
| FR-F5.6 | Filter tabs: All / Active / Completed; active highlighted | US-F5-05 | Switch each tab; verify list updates |
| FR-F5.7 | Item count: dynamic; updates on mutation | US-F5-06 | Add/complete/delete; verify count changes |
| FR-F5.8 | Empty states per filter combination | US-F5-05 | Trigger each empty state scenario |
| FR-F5.9 | Sort: newest first; completed at bottom (optional) | US-F5-01 | Add multiple; verify order |

### F7 — Data Persistence Layer

| FRD Req ID | Requirement Summary | Traced To (Stories) | Test Notes |
|---|---|---|---|
| FR-F7.1 | All mutations write to localStorage immediately | US-F7-01 | After each mutation, inspect localStorage |
| FR-F7.2 | Read from localStorage on load; no stale in-memory state | US-F7-01 | Reload after mutation; verify data present |
| FR-F7.3 | Missing/corrupt key → default state; console warn; no exception | US-F7-02 | Delete key; reload; verify graceful behavior |
| FR-F7.4 | All keys prefixed `todoapp_` | US-F7-01 | Inspect localStorage; verify key prefix |

### F8 — Default Admin Bootstrapping

| FRD Req ID | Requirement Summary | Traced To (Stories) | Test Notes |
|---|---|---|---|
| FR-F8.1 | First-run detection: check `todoapp_users` on load | US-F8-01 | Clear localStorage; reload; verify detection |
| FR-F8.2 | Seed admin account: admin/admin123 as hash | US-F8-01 | First load; verify `todoapp_users` has admin |
| FR-F8.3 | First-run notice visible on login screen | US-F8-02 | First load; verify banner present |
| FR-F8.4 | Idempotency: no re-seed if users exist | US-F8-01 | Load with existing users; verify no duplicate |

---

## 4. User Story Traceability (Backward)

| Story ID | Story Summary | Traced From (PRD Feature) | FRD Requirements |
|---|---|---|---|
| US-F0-01 | Single-Page Experience | F0 | FR-F0.1, FR-F0.2, FR-F0.3 |
| US-F0-02 | Outfit Typography | F0 | FR-F0.4 |
| US-F0-03 | Responsive Layout | F0 | FR-F0.5 |
| US-F1-01 | Login with Valid Credentials | F1 | FR-F1.1, FR-F1.2 |
| US-F1-02 | Login Error for Invalid Credentials | F1 | FR-F1.2 |
| US-F1-03 | Auto-Login on Return Visit | F1 | FR-F1.3 |
| US-F1-04 | Logout | F1 | FR-F1.4 |
| US-F1-05 | Role-Based Routing After Login | F1 | FR-F1.5 |
| US-F2-01 | Register with Valid Invite Code | F2 | FR-F2.1, FR-F2.2, FR-F2.3 |
| US-F2-02 | Registration Fails — Invalid Invite Code | F2 | FR-F2.2, FR-F2.4 |
| US-F2-03 | Registration Fails — Used Invite Code | F2 | FR-F2.2, FR-F2.4 |
| US-F2-04 | Registration Validation — Username & Password | F2 | FR-F2.2, FR-F2.4 |
| US-F3-01 | Generate a New Invite Code | F3 | FR-F3.1 |
| US-F3-02 | Copy Invite Code to Clipboard | F3 | FR-F3.2 |
| US-F3-03 | View All Invite Codes with Status | F3 | FR-F3.3, FR-F3.4 |
| US-F4-01 | View All Registered Users | F4 | FR-F4.1 |
| US-F4-02 | Navigate to a User's Todo List | F4 | FR-F4.2, FR-F4.4 |
| US-F5-01 | Add a New Todo | F5 | FR-F5.1, FR-F5.2, FR-F5.9 |
| US-F5-02 | Mark a Todo as Complete | F5 | FR-F5.2, FR-F5.3 |
| US-F5-03 | Edit a Todo | F5 | FR-F5.4 |
| US-F5-04 | Delete a Todo | F5 | FR-F5.5 |
| US-F5-05 | Filter Todos by Status | F5 | FR-F5.6, FR-F5.8 |
| US-F5-06 | View Item Count Summary | F5 | FR-F5.7 |
| US-F6-01 | Admin View Todos Read-Only | F6 | FR-F4.2, FR-F4.3, FR-F4.5 |
| US-F7-01 | Data Survives Page Refresh | F7 | FR-F7.1, FR-F7.2, FR-F7.4 |
| US-F7-02 | Graceful Recovery from Missing Data | F7 | FR-F7.3 |
| US-F8-01 | First-Run Setup Happens Automatically | F8 | FR-F8.1, FR-F8.2, FR-F8.4 |
| US-F8-02 | First-Run Notice on Login Screen | F8 | FR-F8.3 |

---

## 5. TechArch Coverage Matrix

| TechArch Section | Covers PRD Features | Key Technical Decisions |
|---|---|---|
| §1.1 Architecture Philosophy | F0, F7 | Zero-dependency static SPA; file:// protocol support |
| §1.2 Architectural Pattern | F0, F1, F5 | State-Driven View Pattern (simplified MVC) |
| §1.3 File Structure | F0 | Single `index.html` or minimal multi-file option |
| §1.4 Module Organization | F1, F2, F3, F5, F7 | 8 namespaced modules: Config, Storage, Auth, Users, InviteCodes, Todos, Router, UI |
| §2.1 localStorage Schema | F7 | 4 keys: users, invites, todos_{username}, session |
| §2.2 Type Interfaces | F1, F2, F3, F5, F7 | User, InviteCode, Todo, Session object shapes |
| §2.3 Data Access Patterns | F7, F8 | Read/write helpers; seed logic pattern |
| §2.4 Password Hashing | F1, F2, F8 | Client-side SHA-256 hash; known limitation documented |
| §2.5 UUID Generation | F2, F3, F5 | `crypto.randomUUID()` for IDs and invite codes |
| §3.1 View State Machine | F0, F1, F4, F6 | 5 states; state transition table |
| §3.2 UI Module Structure | F0, F1, F4, F5, F6 | Per-view render functions + event binding |
| §3.3 CSS Architecture | F0 | 11-section CSS; CSS Custom Properties |
| §3.4 Security Considerations | F1, F4, F6 | Role guard in router; known limitations documented |
| §4.1 Target Browsers | F0 | Chrome/Firefox/Safari/Edge 90+ |
| §4.2 APIs Used | F0, F1, F3, F7 | localStorage, crypto.randomUUID, clipboard API |
| §4.3 Performance Targets | F0, F5 | <2s load, <16ms re-render, <500 lines JS |

---

## 6. Test Case Coverage Summary

| Test Category | Story IDs | Priority | Notes |
|---|---|---|---|
| **Smoke Tests (Must Pass for Ship)** | US-F1-01, US-F2-01, US-F3-01, US-F5-01, US-F8-01 | P0 | Core happy paths |
| **Auth Tests** | US-F1-01 through US-F1-05 | P0 | All login/logout/session flows |
| **Registration Tests** | US-F2-01 through US-F2-04 | P0 | All validation error cases |
| **Invite Code Tests** | US-F3-01 through US-F3-03 | P0 | Generate, copy, list |
| **Todo CRUD Tests** | US-F5-01 through US-F5-06 | P0 | Full add/edit/delete/filter/count cycle |
| **Persistence Tests** | US-F7-01, US-F7-02 | P0 | Refresh + corrupt data scenarios |
| **Bootstrapping Tests** | US-F8-01, US-F8-02 | P0 | First-run and idempotency |
| **Admin Oversight Tests** | US-F4-01, US-F4-02, US-F6-01 | P1 | Post-MVP; user list + read-only view |
| **Accessibility Tests** | US-F0-01, US-F0-02, US-F0-03 | P0 | WCAG 2.1 AA on primary flows |
| **Responsive Tests** | US-F0-03 | P0 | 375px, 768px, 1280px breakpoints |

---

## 7. Requirements Coverage Summary

| Metric | Count | Coverage |
|---|---|---|
| PRD Features | 9 | 100% traced to FRD |
| FRD Requirements | 38 | 100% traced to User Stories |
| User Stories | 28 | 100% traced to FRD |
| P0 Features Covered | 7 | 100% |
| P1 Features Covered | 2 | 100% |
| Features with TechArch Coverage | 9 | 100% |
| Stories with Acceptance Criteria | 28 | 100% |

---

## 8. Document Cross-Reference Index

| Document | Filename | Version | Status | Referenced By |
|---|---|---|---|---|
| Product Requirements Document | `PRD-TodoApp.md` | 1.0 | Validated | All documents |
| Personas | `PERSONAS-TodoApp.md` | 1.0 | Validated | FRD, JTBD, UserStories, Journeys, StoryMap |
| Functional Requirements Document | `FRD-TodoApp.md` | 1.0 | Validated | TechArch, UserStories, RTM |
| Jobs-To-Be-Done | `JTBD-TodoApp.md` | 1.0 | Validated | Journeys, StoryMap |
| Technical Architecture | `TechArch-TodoApp.md` | 1.0 | Validated | RTM |
| User Stories | `UserStories-TodoApp.md` | 1.0 | Validated | StoryMap, UX-Mockup, RTM |
| Customer Journeys | `JOURNEYS-TodoApp.md` | 1.0 | Validated | StoryMap, UX-Mockup |
| Story Map | `STORY-MAP-TodoApp.md` | 1.0 | Validated | — |
| UX Mockup | `UX-Mockup-TodoApp.md` | 1.0 | Validated | — |
| Requirements Traceability Matrix | `RTM-TodoApp.md` | 1.0 | Final | — |

---

## 9. Approval Section

| Role | Name | Signature | Date |
|---|---|---|---|
| Product Owner | _______________ | _______________ | _______ |
| Tech Lead | _______________ | _______________ | _______ |
| QA Lead | _______________ | _______________ | _______ |
| Project Sponsor | _______________ | _______________ | _______ |

---

*Document generated by Pivota Spec RTM Generator | TodoApp v1.0 | 2026-04-15*

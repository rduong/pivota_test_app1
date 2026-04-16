# Requirements: Todo List App with User Management

**Defined:** 2026-04-16
**Core Value:** Users can organize their tasks and admins can control who has access — a todo app with a proper invite-based user management layer.

## v1 Requirements

Requirements for initial release. Each maps to roadmap phases.

### Application Shell & Navigation

- [ ] **F0**: Single HTML document with 5 view states (LOGIN, REGISTER, ADMIN_DASHBOARD, USER_DASHBOARD, ADMIN_VIEW_USER), client-side router, Outfit font via Google Fonts, responsive layout at ≥375px

### Authentication & Session Management

- [ ] **F1**: User can log in with username/password, session persists across browser refresh via localStorage, user is routed by role on login, user can log out from any view

### Invite Code Registration

- [ ] **F2**: User can register a new account by entering a valid invite code and choosing credentials; account is created in localStorage and session is established

### Admin — Invite Code Management

- [ ] **F3**: Admin can generate unique invite codes, copy them to clipboard, and view the full list of all codes with their used/available status

### Admin — User Management & Todo Visibility

- [ ] **F4**: Admin can view a list of all registered users and open any user's todo list in a read-only view, with back navigation to the admin dashboard

### User — Todo Management (CRUD)

- [ ] **F5**: User can add, edit, delete, and mark todos as complete; todos are shown in All/Active/Completed filter tabs with item count, empty states, and sorted by newest first

### Admin — Todo Read-Only View (covered by F4)

- [ ] **F6**: Admin's view of any user's todos is fully read-only (no edit, delete, or status-change controls exposed)

### Data Persistence Layer

- [ ] **F7**: All data mutations write to localStorage immediately on change; data is loaded on app start; parse failures are handled gracefully; storage keys are namespaced to avoid collisions

### Default Admin Bootstrapping

- [ ] **F8**: On first run, a default admin account (admin / admin123) is seeded into localStorage; a first-run notice is shown; bootstrapping is idempotent (does not re-seed if data already exists)

## v2 Requirements

Deferred to future release. Tracked but not in current roadmap.

### Future Enhancements

- **v2-01**: Real-time collaboration (multi-user sync without page refresh)
- **v2-02**: Server-side persistence / backend integration
- **v2-03**: Password recovery / account self-service
- **v2-04**: Bulk todo operations (select all, clear completed)
- **v2-05**: Todo due dates and sorting by date

## Out of Scope

Explicitly excluded to prevent scope creep.

| Feature | Reason |
|---------|--------|
| Real-time collaboration | localStorage-only; no backend sync |
| Mobile app (native) | Web SPA only; responsive web acceptable |
| OAuth / third-party login | Invite code registration only |
| External database / backend | No server required; fully static |

## Traceability

Which phases cover which requirements. Updated during roadmap creation.

| Requirement | Phase | Status |
|-------------|-------|--------|
| F0 | Phase 1 | Pending |
| F7 | Phase 1 | Pending |
| F8 | Phase 1 | Pending |
| F1 | Phase 2 | Pending |
| F2 | Phase 3 | Pending |
| F3 | Phase 3 | Pending |
| F5 | Phase 4 | Pending |
| F4 | Phase 5 | Pending |
| F6 | Phase 5 | Pending |

**Coverage:**
- v1 requirements: 9 total
- Mapped to phases: 9
- Unmapped: 0 ✓

---
*Requirements defined: 2026-04-16*
*Last updated: 2026-04-16 after initial definition*

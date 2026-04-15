# Todo List App with User Management

## What This Is

A single-page web application (HTML/CSS/JavaScript) that allows teams or individuals to manage their tasks with role-based access control. Users access the app via invite codes, and an admin manages user accounts and has visibility into all users' todo lists.

## Core Value

Users can organize their tasks and admins can control who has access — a todo app with a proper invite-based user management layer.

## Requirements

### Validated

(None yet — ship to validate)

### Active

- [ ] Admin can generate invite links with unique codes
- [ ] Admin can view all users in the system
- [ ] Admin has full access to all users' todo lists
- [ ] User can register via invite code
- [ ] User can add, update, and delete their own todo items
- [ ] User cannot access admin features
- [ ] Data persists via localStorage

### Out of Scope

- Real-time collaboration — localStorage-only; no backend sync
- Mobile app — web SPA only (responsive web acceptable)
- OAuth / third-party login — invite code registration only
- External database / backend — no server required

## Context

- Pure frontend app: HTML, CSS, JavaScript (single-file SPA or minimal file structure)
- Data storage: localStorage (no backend, no database)
- Fonts: Google Fonts (Outfit)
- No external frameworks (no React, Vue, Angular, etc.)
- Two user roles: Admin and User
- Access control enforced client-side
- Invite system uses unique codes to gate registration

## Constraints

- **Tech Stack**: Pure HTML/CSS/JS — no frameworks required
- **Data**: localStorage only — no server, no external DB
- **Fonts**: Google Fonts (Outfit) — for typography
- **Architecture**: Single-page app — all state client-side
- **Access Control**: Client-side role enforcement (Admin vs User)

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| localStorage for data | No backend needed; keeps app fully static and portable | — Pending |
| Invite code registration | Controls who can use the app without a full auth backend | — Pending |
| Pure HTML/CSS/JS (no frameworks) | Keeps app simple, no build step, single-file possible | — Pending |

---
*Last updated: 2026-04-15 after initialization*

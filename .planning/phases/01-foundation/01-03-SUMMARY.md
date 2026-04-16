---
phase: 01-foundation
plan: "03"
subsystem: ui
tags: [router, state-machine, ui-rendering, event-binding, subtlecrypto, first-run, bootstrap, javascript, single-file]

# Dependency graph
requires:
  - phase: 01-foundation
    provides: "Config/Storage/hashPassword/generateUUID/Bootstrap namespaces in index.html (Plans 01-01, 01-02)"
provides:
  - "Router namespace: navigate() with role/session guards, render() switch on 5 view states, start() with firstRun flag"
  - "UI namespace: renderLogin() with conditional first-run notice, renderRegister() stub, 3 additional view stubs"
  - "UI.bindEvents(): post-innerHTML event binding for go-register, go-login, login-form submit placeholder"
  - "DOMContentLoaded async entry point: SubtleCrypto guard, await Bootstrap.init(), Router.start(firstRun)"
  - "Complete Phase 1 foundation: app opens in browser, shows LOGIN view, handles first-run state and basic navigation"
affects: [02-auth, 03-registration, 04-todos, 05-admin]

# Tech tracking
tech-stack:
  added: []
  patterns:
    - "Router state machine pattern — 5 view states (LOGIN, REGISTER, ADMIN_DASHBOARD, USER_DASHBOARD, ADMIN_VIEW_USER), navigate() with guards"
    - "Post-innerHTML event binding — UI.bindEvents() always called after app.innerHTML assignment, never before"
    - "Secure context guard — window.crypto.subtle check in DOMContentLoaded before any crypto operations"
    - "firstRun flag threading — Bootstrap.init() → Router.start(firstRun) → UI.renderLogin({ firstRun }) → conditional notice"
    - "Role/session guards in Router.navigate() — admin-only views redirect to USER_DASHBOARD, USER_DASHBOARD redirects to LOGIN"

key-files:
  created: []
  modified: [index.html]

key-decisions:
  - "bindEvents() called AFTER innerHTML assignment in every render() path — ensures DOM elements exist before querySelector"
  - "SubtleCrypto guard in DOMContentLoaded entry point shows graceful error on insecure HTTP contexts"
  - "Router.start(firstRun) threading: firstRun boolean flows from Bootstrap.init() through Router into UI.renderLogin() conditional"
  - "5 view stubs (ADMIN_DASHBOARD, USER_DASHBOARD, ADMIN_VIEW_USER) defined now as placeholders — avoids switch default for unimplemented views"

patterns-established:
  - "Pattern: Router.navigate(view, context) → Router.render() → UI.render{View}(context) → UI.bindEvents(view, context)"
  - "Pattern: All navigation via Router.navigate() — never direct innerHTML manipulation outside Router.render()"
  - "Pattern: Session guards in Router.navigate() enforce view access control without separate auth middleware"

# Metrics
duration: 1min
completed: 2026-04-16
---

# Phase 1 Plan 03: Router, UI Render Functions, and Async Init Summary

**Router state machine with 5 view states, UI render functions with first-run notice, and DOMContentLoaded async entry point with SubtleCrypto guard — completing Phase 1 foundation**

## Performance

- **Duration:** 1 min
- **Started:** 2026-04-16T14:36:58Z
- **Completed:** 2026-04-16T14:38:05Z
- **Tasks:** 1 auto (+ 1 checkpoint awaiting human verify)
- **Files modified:** 1

## Accomplishments

- Implemented `UI` namespace with `renderLogin()` (conditional first-run notice), `renderRegister()` stub, and 3 additional view stubs (`renderAdminDashboard`, `renderUserDashboard`, `renderAdminViewUser`)
- Implemented `UI.bindEvents()` with post-innerHTML event binding: `go-register` → `Router.navigate('REGISTER')`, `go-login` → `Router.navigate('LOGIN')`, login-form submit placeholder for Phase 2
- Implemented `Router` namespace: `navigate()` with admin/session role guards, `render()` switch across all 5 view states, `start()` reads session and routes appropriately
- Implemented `DOMContentLoaded` async entry point: `window.crypto.subtle` guard with graceful error UI, `await Bootstrap.init()`, `Router.start(firstRun)` — complete app initialization chain

## Task Commits

Each task was committed atomically:

1. **Task 1: Router, UI render functions, async init()** - `65239dd` (feat)

**Checkpoint:** Task 2 (human-verify) — awaiting user verification before plan metadata commit

## Files Created/Modified

- `index.html` — Added UI namespace (renderLogin/renderRegister/stubs/bindEvents), Router namespace (navigate/render/start), DOMContentLoaded async entry point (479 lines total, ~190 lines added)

## Router Navigation States

| View | Render function | Guard |
|------|----------------|-------|
| LOGIN | `UI.renderLogin(context)` | None (public) |
| REGISTER | `UI.renderRegister()` | None (public) |
| ADMIN_DASHBOARD | `UI.renderAdminDashboard()` | session.role === 'admin' |
| USER_DASHBOARD | `UI.renderUserDashboard()` | session exists |
| ADMIN_VIEW_USER | `UI.renderAdminViewUser(context)` | session.role === 'admin' |

## First-Run Notice Implementation

Flow: `Bootstrap.init()` returns `{ firstRun: true }` on empty localStorage → `Router.start(firstRun)` → `Router.navigate('LOGIN', { firstRun: true })` → `UI.renderLogin({ firstRun: true })` → conditional `<div class="first-run-notice">` rendered → shows "admin / admin123" credentials.

On reload: `Bootstrap.init()` finds existing users → returns `{ firstRun: false }` → notice not rendered.

## Event Binding Pattern

`UI.bindEvents(view, context)` is called at the END of every `Router.render()` path, AFTER `app.innerHTML = ...`. This ensures all DOM elements referenced by `getElementById` exist at binding time. The pattern prevents "Cannot read properties of null" errors from early binding attempts.

## Decisions Made

- **bindEvents after innerHTML**: All event listeners attached post-render — elements must exist in DOM before event binding
- **SubtleCrypto guard position**: Check happens in DOMContentLoaded before any Bootstrap/Router calls — prevents misleading errors deep in stack
- **5 view stubs defined immediately**: Avoids switch `default` fallthrough for future views — each has a named case from day 1

## Deviations from Plan

None - plan executed exactly as written.

## Issues Encountered

None.

## User Setup Required

None - no external service configuration required. Pure browser JavaScript, no build step.

## Current State

Phase 1 Foundation: HTML scaffold (Plan 01-01) + CSS design system (Plan 01-01) + JavaScript data layer (Plan 01-02) + Router/UI/init (Plan 01-03) = **complete app framework**. Awaiting human visual verification.

## Next Phase Readiness

- Phase 1 complete pending human verification checkpoint
- `Router.navigate()` is the extension point for Phase 2 Auth (login form submission, session creation)
- `UI.bindEvents()` login-form submit handler is a placeholder stub ready for `Auth.login()` in Phase 2
- All 5 view states stubbed — Phase 2+ can implement content without structural changes
- No blockers

---
*Phase: 01-foundation*
*Completed: 2026-04-16*

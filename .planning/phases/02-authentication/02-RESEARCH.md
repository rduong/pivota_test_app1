# Phase 2: Authentication - Research

**Researched:** 2026-04-16
**Domain:** Vanilla JS client-side authentication — login form submission, session management via localStorage, role-based routing, logout, async SubtleCrypto password comparison
**Confidence:** HIGH

---

## Summary

Phase 2 adds all authentication logic on top of the Phase 1 foundation. The infrastructure is already in place: `Config`, `Storage`, `hashPassword()`, `generateUUID()`, `Bootstrap`, `Router`, and the login form HTML are all done. What's missing is the *logic* that makes login actually work: the `Auth` namespace (login/logout/getSession), the login form's `submit` event handler, the logout button in authenticated views, and stub views for `ADMIN_DASHBOARD` and `USER_DASHBOARD` that include the required header/logout button.

The most important technical constraint is that **`hashPassword()` is async** — the login validation chain must be fully async from the event handler through `Auth.login()`. The credential comparison must use constant-time-equivalent logic (hash both sides, compare hashes as strings) because there's no timing-safe comparison in vanilla JS. The session object must use `generateUUID()` for the token field and `new Date().toISOString()` for `loginAt`.

The architecture is additive: all new code integrates into the existing namespace pattern. Phase 2 replaces the placeholder `submit` handler in `UI.bindEvents()` (which currently has a `// Auth.login() wired in Phase 2` comment) and replaces the stub dashboard render functions with real views that include the app header and logout button.

**Primary recommendation:** Implement `Auth` namespace first, then update `UI.bindEvents()` to wire the login submit handler, then replace `UI.renderAdminDashboard()` and `UI.renderUserDashboard()` stubs with real header+logout views. Wire logout in `UI.bindEvents()` for both authenticated views.

---

## Standard Stack

### Core

| Technology | Version / Source | Purpose | Why Standard |
|------------|------------------|---------|--------------|
| Vanilla JavaScript (ES2020+) | Locked in TechArch | All auth logic | No-framework decision locked — no alternatives |
| `SubtleCrypto.digest('SHA-256')` | Web Crypto API — already in Phase 1 | Password hash comparison at login | Already implemented as `hashPassword()` in Phase 1 |
| `localStorage` via `Storage` wrapper | Already in Phase 1 | Persist session (`todoapp_session`) | Locked — no backend; resilient wrapper already built |
| `crypto.randomUUID()` / polyfill | Already in Phase 1 as `generateUUID()` | Session token generation | Already implemented with Safari 14 polyfill |

### Supporting

| Technology | Purpose | When to Use |
|------------|---------|-------------|
| `new Date().toISOString()` | `loginAt` ISO 8601 timestamp on session creation | Every login |
| `role="alert"` + `aria-live="assertive"` | Error message accessibility on login form | Inline error spans (already in Phase 1 HTML) |
| CSS `.btn-secondary` / logout button styles | Log Out button in header | Must be styled for all authenticated views |

### Alternatives Considered

| Instead of | Could Use | Tradeoff |
|------------|-----------|----------|
| Hash comparison (SHA-256 of input == stored hash) | Plain string comparison | Plain comparison is reversible — hash comparison is correct; already mandated by TechArch |
| localStorage session | sessionStorage | sessionStorage doesn't survive page refresh — violates FR-F1.3 |
| UUID token in session | Timestamp as token | UUID is cryptographically random; timestamp is predictable |

**Installation:** No packages — all native browser APIs already established in Phase 1.

---

## Architecture Patterns

### Recommended Project Structure

This phase adds code **inside** the existing `index.html` `<script>` block, in this order:

```
<script>
  const Config = { ... }       // Phase 1 — unchanged
  const Storage = { ... }      // Phase 1 — unchanged
  async function hashPassword() // Phase 1 — unchanged
  function generateUUID()       // Phase 1 — unchanged
  const Bootstrap = { ... }    // Phase 1 — unchanged

  // ── NEW IN PHASE 2 ──────────────────────────────────
  const Auth = {                // NEW: login(), logout(), getSession()
    async login(username, password) { ... },
    logout() { ... },
    getSession() { ... }
  };
  // ────────────────────────────────────────────────────

  const Router = { ... }        // Phase 1 — navigate() and start() unchanged
                                 //  Role guard already implemented

  const UI = {
    renderLogin(context)         // Phase 1 — unchanged (form HTML, errors already there)
    renderRegister()             // Phase 1 — stub, unchanged
    renderAdminDashboard()       // REPLACE stub with real view (header + logout + placeholder)
    renderUserDashboard()        // REPLACE stub with real view (header + logout + placeholder)
    renderAdminViewUser()        // Phase 1 — stub, unchanged
    bindEvents(view, context) {  // EXTEND: wire login submit + logout handlers
      if (view === 'LOGIN') {
        // 'go-register' already bound in Phase 1
        // REPLACE placeholder submit handler with Auth.login() call
      }
      if (view === 'ADMIN_DASHBOARD') { /* bind logout btn */ }
      if (view === 'USER_DASHBOARD')  { /* bind logout btn */ }
    }
  };

  // DOMContentLoaded — Phase 1 — unchanged
</script>
```

### Pattern 1: Auth Namespace — Login Flow

**What:** `Auth.login(username, password)` validates credentials, creates a session, writes to localStorage, and returns a result object. It does NOT navigate — the caller navigates on success.

**When to use:** Called by the login form's `submit` event handler only.

**Exact spec from FRD FR-F1.2 + TechArch Session interface:**

```javascript
// Source: FRD-TodoApp.md FR-F1.2, FR-F1.4, FR-F1.5 + TechArch §2.2 Session interface
const Auth = {

  // Returns: { ok: true, session } on success
  // Returns: { ok: false, error: 'EMPTY_FIELDS' | 'INVALID_CREDENTIALS' } on failure
  async login(username, password) {
    // Step 1: Non-empty check (FR-F1.2 rule 1)
    if (!username.trim() || !password) {
      return { ok: false, error: 'EMPTY_FIELDS' };
    }

    // Step 2: Look up user in storage (FR-F1.2 rule 2)
    const users = Storage.get(Config.KEYS.USERS, []);
    const user = users.find(u => u.username === username.trim());
    if (!user) {
      return { ok: false, error: 'INVALID_CREDENTIALS' };
    }

    // Step 3: Hash the submitted password and compare (FR-F1.2 rule 3)
    // MUST await — hashPassword() is async (SubtleCrypto)
    const submittedHash = await hashPassword(password);
    if (submittedHash !== user.passwordHash) {
      return { ok: false, error: 'INVALID_CREDENTIALS' };
    }

    // Step 4: Create session, write to localStorage (FR-F1.2 rule 4)
    const session = {
      username: user.username,
      role: user.role,            // 'admin' | 'user'
      token: generateUUID(),      // Random UUID per TechArch Session spec
      loginAt: new Date().toISOString()
    };
    Storage.set(Config.KEYS.SESSION, session);

    return { ok: true, session };
  },

  // Clears session, navigates to LOGIN (FR-F1.4)
  logout() {
    Storage.remove(Config.KEYS.SESSION);
    Router.navigate('LOGIN');
  },

  // Reads current session from localStorage (FR-F1.3)
  getSession() {
    return Storage.get(Config.KEYS.SESSION, null);
  }
};
```

### Pattern 2: Login Form Submit Handler (async event handler)

**What:** The submit handler must be `async` because it calls `Auth.login()` which is async. Disable the submit button during processing, show/clear errors, navigate on success.

**When to use:** Wired inside `UI.bindEvents()` when `view === 'LOGIN'`, replacing the Phase 1 placeholder.

```javascript
// Source: FRD-TodoApp.md FR-F1.1, FR-F1.2, UX-Mockup §5.1
// Inside UI.bindEvents(), LOGIN branch:
const loginForm = document.getElementById('login-form');
const loginBtn = document.getElementById('login-btn');

if (loginForm) {
  loginForm.addEventListener('submit', async (e) => {
    e.preventDefault();

    // Clear previous errors
    document.getElementById('username-error').textContent = '';
    document.getElementById('password-error').textContent = '';

    const username = document.getElementById('username').value;
    const password = document.getElementById('password').value;

    // Disable button during async operation (UX-Mockup §5.1)
    if (loginBtn) { loginBtn.disabled = true; loginBtn.textContent = 'Logging in…'; }

    const result = await Auth.login(username, password);

    if (loginBtn) { loginBtn.disabled = false; loginBtn.textContent = 'Log In'; }

    if (!result.ok) {
      if (result.error === 'EMPTY_FIELDS') {
        // Show field-specific errors for empty fields
        if (!username.trim()) {
          document.getElementById('username-error').textContent = 'Username is required.';
        }
        if (!password) {
          document.getElementById('password-error').textContent = 'Password is required.';
        }
      } else {
        // INVALID_CREDENTIALS — generic message, do not reveal which field (FR-F1.2)
        document.getElementById('username-error').textContent = 'Invalid username or password.';
      }
      return;
    }

    // Success: route by role (FR-F1.5)
    if (result.session.role === 'admin') {
      Router.navigate('ADMIN_DASHBOARD');
    } else {
      Router.navigate('USER_DASHBOARD');
    }
  });
}
```

### Pattern 3: Authenticated View Header with Logout

**What:** Both `ADMIN_DASHBOARD` and `USER_DASHBOARD` need a persistent header (FR-F1.4, FRD §4.1) containing: app name, username + role badge, and Log Out button. Phase 2 delivers minimal placeholder views (full content is later phases) but MUST have the real header.

**When to use:** Replace stub render functions for both authenticated views.

```javascript
// Source: FRD-TodoApp.md §4.1, UX-Mockup §Screen 3 / Screen 4
// Helper to build the app header HTML — used by both dashboard views
function renderAppHeader(session) {
  const roleBadgeClass = session.role === 'admin' ? 'badge badge-admin' : 'badge badge-user';
  const roleName = session.role === 'admin' ? 'Admin' : 'User';
  return `
    <header class="app-header">
      <span class="app-header-title">TodoApp</span>
      <div class="app-header-user">
        <span class="app-header-username">${escapeHtml(session.username)}</span>
        <span class="${roleBadgeClass}">${roleName}</span>
        <button id="logout-btn" class="btn btn-logout">Log Out</button>
      </div>
    </header>
  `;
}

// renderAdminDashboard — real header + placeholder body
renderAdminDashboard() {
  const session = Storage.get(Config.KEYS.SESSION, {});
  return `
    <div class="dashboard-page">
      ${renderAppHeader(session)}
      <main class="dashboard-main">
        <h2 class="section-title">Admin Dashboard</h2>
        <p class="placeholder-text">Invite codes and user management coming in Phase 3.</p>
      </main>
    </div>
  `;
},

// renderUserDashboard — real header + placeholder body
renderUserDashboard() {
  const session = Storage.get(Config.KEYS.SESSION, {});
  return `
    <div class="dashboard-page">
      ${renderAppHeader(session)}
      <main class="dashboard-main">
        <h2 class="section-title">My Tasks</h2>
        <p class="placeholder-text">Todo list coming in Phase 4.</p>
      </main>
    </div>
  `;
},
```

**bindEvents for logout (add to bindEvents for both dashboard views):**
```javascript
// Inside UI.bindEvents():
if (view === 'ADMIN_DASHBOARD' || view === 'USER_DASHBOARD') {
  const logoutBtn = document.getElementById('logout-btn');
  if (logoutBtn) {
    logoutBtn.addEventListener('click', () => Auth.logout());
  }
}
```

### Pattern 4: XSS Prevention — escapeHtml()

**What:** The session `username` field is rendered into the dashboard header HTML string. Since it came from user input at registration, it MUST be HTML-escaped before injection to prevent XSS.

**When to use:** Any time user-provided data is inserted into an HTML string via `innerHTML`.

```javascript
// Source: Standard DOM security practice
// No official spec URL — this is a universal pattern
function escapeHtml(str) {
  return String(str)
    .replace(/&/g, '&amp;')
    .replace(/</g, '&lt;')
    .replace(/>/g, '&gt;')
    .replace(/"/g, '&quot;')
    .replace(/'/g, '&#39;');
}
```

Note: For this app, username validation at registration (Phase 3) restricts input to alphanumeric + underscore, so XSS via username is practically impossible. Still, `escapeHtml()` should be the standard practice and is worth adding as a utility.

### Pattern 5: Session Persistence Check on Page Load

**What:** `Router.start()` already implements this (Phase 1). It calls `Storage.get(Config.KEYS.SESSION)` and routes accordingly. Phase 2 does NOT need to change `Router.start()` — it just needs `Auth.login()` to write the session correctly so `Router.start()` can read it on the next page load.

**Verify the data flow:**
```
Page load → DOMContentLoaded → Bootstrap.init() → Router.start()
                                                       ↓
                                              Storage.get('todoapp_session')
                                                       ↓
                                    null → LOGIN  |  admin → ADMIN_DASHBOARD  |  user → USER_DASHBOARD
```

This is already implemented. Phase 2 just needs to ensure `Auth.login()` writes the session with the exact shape Router.start() expects.

### Anti-Patterns to Avoid

- **Forgetting `await` on `Auth.login()`:** The submit handler must be `async function(e)`. Without `await`, `result` is a Promise object; `result.ok` is `undefined`; navigation never happens.
- **Navigating before writing session:** `Storage.set(Config.KEYS.SESSION, session)` MUST be called BEFORE `Router.navigate()`. The Router reads the session on render; if it's not written yet, guards may redirect.
- **Using `onclick="..."` attributes in HTML strings:** All event binding must happen in `UI.bindEvents()` after `innerHTML` assignment. Inline handlers don't work with `novalidate` forms and are harder to manage.
- **Calling `Router.navigate()` inside `Auth.login()`:** Auth should return a result, not navigate. Separation of concerns — the event handler (UI layer) navigates; Auth just validates and creates the session.
- **Not clearing errors before re-validation:** If the user fixes the form and resubmits, stale error messages from the previous attempt must be cleared first.
- **Re-rendering the full login view on error:** Don't call `Router.navigate('LOGIN')` on login failure — this re-renders the form and clears the input values (bad UX per FRD §4.2: "form MUST NOT clear successfully filled fields on error"). Instead, update error spans in-place.
- **Rendering username directly into innerHTML without escaping:** Always use `escapeHtml()` for any user-provided data interpolated into HTML strings.

---

## Don't Hand-Roll

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Password comparison | `password === storedPassword` (plaintext compare) | Hash both sides: `await hashPassword(input) === user.passwordHash` | Passwords stored as hashes; plaintext comparison impossible and wrong |
| Session token | Timestamp-based token, sequential ID | `generateUUID()` (already built in Phase 1) | Cryptographically random; already implemented with Safari 14 polyfill |
| Error display | `alert()` or re-render entire form | Update `textContent` of existing `#username-error` / `#password-error` spans | Preserves form field values; already in the login HTML from Phase 1 |
| Logout state clearing | Manually clearing every in-memory variable | `Storage.remove(Config.KEYS.SESSION)` + `Router.navigate('LOGIN')` | Router.render() rebuilds state from localStorage on every navigation; no stale state risk |
| Role routing | Custom role check after login | `Router.navigate()` which has built-in role guard already from Phase 1 | Role guard already prevents non-admin from reaching ADMIN_DASHBOARD |

**Key insight:** Phase 1 built the infrastructure. Phase 2 is almost entirely *wiring* — the hard work (async hashing, storage wrappers, router guards, session reading on page load) is already done. The main risk is async mistakes in the event handler chain.

---

## Common Pitfalls

### Pitfall 1: Non-async Submit Handler

**What goes wrong:** `loginForm.addEventListener('submit', (e) => { ... await Auth.login() ... })` — missing `async` keyword on the callback. The `await` inside is either a syntax error or silently ignored (depending on context). `Auth.login()` returns a Promise; `result.ok` is `undefined`; no navigation occurs.

**Why it happens:** Copy-paste from non-async examples; forgetting that the entire call chain from `Auth.login()` up to the event handler must be async.

**How to avoid:** The submit handler must be: `loginForm.addEventListener('submit', async (e) => { ... })`.

**Warning signs:** Click "Log In" with correct credentials → nothing happens, no error, no navigation. Console shows no errors. DevTools: `result` is a `Promise` object.

### Pitfall 2: Session Written With Wrong Shape

**What goes wrong:** `Router.start()` reads `session.role` to decide which view to navigate to. If the session is written with a different field name (e.g., `userRole` instead of `role`, or `loginTime` instead of `loginAt`), the Router silently redirects to USER_DASHBOARD (any session without `role === 'admin'` goes there).

**Why it happens:** Discrepancy between what `Auth.login()` writes and what `Router.start()` + role guards read.

**How to avoid:** Session object shape is EXACT per TechArch:
```javascript
{ username: string, role: 'admin' | 'user', token: string, loginAt: string }
```
Use `Config.KEYS.SESSION` (`'todoapp_session'`), not a hard-coded string.

**Warning signs:** Admin login → routes to USER_DASHBOARD (not ADMIN). Check localStorage value of `todoapp_session` in DevTools.

### Pitfall 3: Username Case Sensitivity Mismatch

**What goes wrong:** User registers as `Admin` (capital A); bootstrap seeded `admin` (lowercase). Login with `Admin` returns "Invalid username or password" because `users.find(u => u.username === 'Admin')` fails.

**Why it happens:** The TechArch username validation requires alphanumeric + underscore, but doesn't mandate lowercase. The bootstrap seed uses `'admin'` (lowercase). If the comparison is case-sensitive, `admin` ≠ `Admin`.

**How to avoid:** The spec says stored username is `'admin'` and the first-run notice tells users to type `admin`. The `.find()` comparison should be case-sensitive (as written). The important thing is that the login form does NOT silently lowercase the username before lookup — that would be incorrect behavior for future users who registered with mixed case.

**Warning signs:** Login fails with correct password; check exact username string in localStorage vs. input value.

### Pitfall 4: Logout Button Not in `bindEvents` for Authenticated Views

**What goes wrong:** `UI.renderAdminDashboard()` and `UI.renderUserDashboard()` generate HTML with `id="logout-btn"`, but `UI.bindEvents()` doesn't add a case for these views. The button renders but clicking it does nothing.

**Why it happens:** Phase 1 `bindEvents()` only handled `LOGIN` and `REGISTER` views. It's easy to forget to extend `bindEvents()` for the new views.

**How to avoid:** Add logout binding in `bindEvents()` for both `ADMIN_DASHBOARD` and `USER_DASHBOARD` cases.

**Warning signs:** Clicking Log Out button in dashboard → nothing happens. Check DevTools Event Listeners panel — logout-btn has no listeners.

### Pitfall 5: Button Re-enable Missing on Error Path

**What goes wrong:** The submit handler disables the button (`loginBtn.disabled = true`) before the async call. If `Auth.login()` throws an uncaught error (e.g., SubtleCrypto unavailable), the catch path doesn't re-enable the button. The form is permanently frozen.

**Why it happens:** Missing `try/catch` around the async login call; early returns that bypass the re-enable line.

**How to avoid:** Use `try/finally` to ensure the button is always re-enabled:
```javascript
try {
  if (loginBtn) { loginBtn.disabled = true; loginBtn.textContent = 'Logging in…'; }
  const result = await Auth.login(username, password);
  // ... handle result
} finally {
  if (loginBtn) { loginBtn.disabled = false; loginBtn.textContent = 'Log In'; }
}
```

**Warning signs:** After a login attempt (success or failure), the Log In button stays disabled/grayed.

### Pitfall 6: Stale In-Memory State After Logout

**What goes wrong:** After logout, navigating back to a dashboard (e.g., browser back button or `Router.navigate('ADMIN_DASHBOARD')` called from somewhere) shows the dashboard briefly because `Router.currentView` still holds the old state.

**Why it happens:** The router role guard is in `Router.navigate()`, not in a direct `render()` call. If `render()` is called directly without going through `navigate()`, the guard is bypassed.

**How to avoid:** Always navigate through `Router.navigate()`. Never call `Router.render()` directly from outside the Router. `Auth.logout()` calls `Router.navigate('LOGIN')` which clears `currentView` and triggers a clean render.

**Note:** The app has no browser back/forward navigation (FR-F0.1 says it's not required). This is a lower-risk pitfall for this specific app.

---

## Code Examples

Verified patterns from project spec sources:

### Complete Auth Namespace

```javascript
// Source: FRD-TodoApp.md §FR-F1.2, §FR-F1.4 + TechArch-TodoApp.md §2.2 Session interface
const Auth = {
  async login(username, password) {
    if (!username.trim() || !password) {
      return { ok: false, error: 'EMPTY_FIELDS' };
    }
    const users = Storage.get(Config.KEYS.USERS, []);
    const user = users.find(u => u.username === username.trim());
    if (!user) {
      return { ok: false, error: 'INVALID_CREDENTIALS' };
    }
    const submittedHash = await hashPassword(password);  // MUST await
    if (submittedHash !== user.passwordHash) {
      return { ok: false, error: 'INVALID_CREDENTIALS' };
    }
    const session = {
      username: user.username,
      role: user.role,
      token: generateUUID(),
      loginAt: new Date().toISOString()
    };
    Storage.set(Config.KEYS.SESSION, session);
    return { ok: true, session };
  },

  logout() {
    Storage.remove(Config.KEYS.SESSION);
    Router.navigate('LOGIN');
  },

  getSession() {
    return Storage.get(Config.KEYS.SESSION, null);
  }
};
```

### Session Object Shape (Exact — from TechArch §2.2)

```javascript
// Source: TechArch-TodoApp.md §2.2 — TypeScript interface for Session
// localStorage key: 'todoapp_session' (Config.KEYS.SESSION)
const session = {
  username: 'admin',                          // string
  role: 'admin',                              // 'admin' | 'user'
  token: generateUUID(),                      // Random UUID v4 string
  loginAt: new Date().toISOString()           // ISO 8601, e.g. "2026-04-16T12:00:00.000Z"
};
```

### Router Security Guard (from Phase 1 — already implemented)

```javascript
// Source: TechArch-TodoApp.md §3.4 (already in Router.navigate from Phase 1)
// Included here for reference — Phase 2 does NOT need to change this
Router.navigate = function(view, context = {}) {
  const session = Storage.get(Config.KEYS.SESSION);
  if (['ADMIN_DASHBOARD', 'ADMIN_VIEW_USER'].includes(view)) {
    if (!session || session.role !== 'admin') {
      this.navigate('USER_DASHBOARD');
      return;
    }
  }
  // ... proceed with navigation
};
```

### CSS for App Header (New Styles Required)

```css
/* Source: UX-Mockup-TodoApp.md §Screen 3, Screen 4, §4.1 Header spec */
.app-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: var(--space-sm) var(--space-lg);
  background-color: var(--color-bg);
  border-bottom: 1px solid var(--color-border);
  position: sticky;
  top: 0;
  z-index: 10;
}

.app-header-title {
  font-size: var(--font-size-h3);
  font-weight: 700;
  color: var(--color-primary);
}

.app-header-user {
  display: flex;
  align-items: center;
  gap: var(--space-sm);
}

.app-header-username {
  font-size: var(--font-size-sm);
  color: var(--color-text-muted);
}

.badge {
  font-size: var(--font-size-sm);
  font-weight: 500;
  padding: 2px var(--space-sm);
  border-radius: var(--radius-pill);
}

.badge-user {
  background-color: #EFF6FF;
  color: var(--color-primary);
}

.badge-admin {
  background-color: #F3E8FF;     /* Light purple */
  color: var(--color-accent-admin);
}

.btn-logout {
  background-color: transparent;
  color: var(--color-text-muted);
  border: 1px solid var(--color-border);
  font-size: var(--font-size-sm);
  padding: var(--space-xs) var(--space-sm);
  min-height: 32px;
  border-radius: var(--radius-md);
  cursor: pointer;
}

.btn-logout:hover {
  background-color: var(--color-surface);
  color: var(--color-text);
}

.dashboard-page {
  min-height: 100vh;
  background-color: var(--color-surface);
}

.dashboard-main {
  max-width: 640px;     /* User dashboard — UX-Mockup §6 */
  margin: 0 auto;
  padding: var(--space-xl) var(--space-lg);
}

.dashboard-main.admin {
  max-width: 900px;     /* Admin dashboard — UX-Mockup §6 */
}

.section-title {
  font-size: var(--font-size-h2);
  font-weight: 600;
  color: var(--color-text);
  margin-bottom: var(--space-lg);
}
```

### escapeHtml Utility

```javascript
// Source: Standard DOM security practice (no library needed)
// Required for rendering session.username into innerHTML
function escapeHtml(str) {
  return String(str)
    .replace(/&/g, '&amp;')
    .replace(/</g, '&lt;')
    .replace(/>/g, '&gt;')
    .replace(/"/g, '&quot;')
    .replace(/'/g, '&#39;');
}
```

---

## State of the Art

| Old Approach | Current Approach | When Changed | Impact for This App |
|--------------|------------------|--------------|---------------------|
| `sessionStorage` for sessions | `localStorage` for persistence across refresh | N/A — locked by spec | FR-F1.3 requires persistence — `sessionStorage` cannot be used |
| `MD5` / `SHA-1` password hashing | `SubtleCrypto.digest('SHA-256')` | Industry standard since ~2010 | SHA-256 already implemented in Phase 1 |
| `onclick` attributes in HTML | Post-`innerHTML` `addEventListener` | Always best practice | Phase 1 established this pattern; Phase 2 must follow same convention |
| `confirm()` dialogs for destructive actions | Inline confirmation UI | Modern UX expectation | Not relevant for Phase 2 (no destructive actions in auth) |

**Note on session expiry:** This app has no session expiry. The TechArch spec does not include an `expiresAt` field or TTL logic. The session persists until explicit logout. This is correct per spec — do NOT add expiry logic.

---

## Open Questions

1. **Error message placement — generic vs. field-specific**
   - What we know: FR-F1.2 rule 2 says "show 'Invalid username or password' if not found (do not reveal which field is wrong)". Rule 3 says the same for password mismatch. Rule 1 says "inline validation errors shown if empty".
   - What's unclear: For EMPTY_FIELDS case, should each empty field get its own error (`"Username is required"` below username field) or a single generic error?
   - Recommendation: Show field-specific "required" errors for empty fields (rules 1 is separate from rules 2-3). Show generic "Invalid username or password" only for rules 2 and 3. The FRD is clear on this distinction — rule 1 is about empty validation (field-specific is more helpful), rules 2-3 are about security (don't reveal which field is wrong). Place generic error under the username field since it's the first field.

2. **`escapeHtml` utility placement**
   - What we know: Session username needs HTML escaping when rendered into the header.
   - What's unclear: Where to define `escapeHtml()` — before `Storage`? After `Auth`?
   - Recommendation: Define `escapeHtml()` as a top-level function near the top of the script block (alongside `hashPassword` and `generateUUID`). It's a utility, not a namespace member.

3. **Dashboard placeholder content depth**
   - What we know: Phase 2 must deliver `ADMIN_DASHBOARD` and `USER_DASHBOARD` views with a real header and logout button. Full content is later phases.
   - What's unclear: Should the placeholder body include any content at all, or just the header?
   - Recommendation: Include a visible placeholder message (e.g., "Invite codes and user management coming soon.") so the view is clearly recognizable. Do NOT include any todo CRUD or invite code UI — those are Phase 3 and 4 respectively.

---

## Sources

### Primary (HIGH confidence)

- **FRD-TodoApp.md** — FR-F1.1 through FR-F1.5, §4.1 Header spec, §5 Error messages — complete authentication functional requirements
- **TechArch-TodoApp.md** — §2.2 Session interface (exact shape), §3.1 Router state transitions, §3.2 UI Module Structure, §3.4 Security Guard pattern
- **UX-Mockup-TodoApp.md** — §Screen 1 (Login view states), §Screen 3 (User Dashboard header), §Screen 4 (Admin Dashboard header), §5.1 Form Submission pattern
- **Phase 1 RESEARCH.md** — Confirmed Phase 1 deliverables: `hashPassword`, `generateUUID`, `Storage`, `Config`, `Bootstrap`, `Router` (including role guard), `UI.renderLogin()`, `UI.bindEvents()` with placeholder submit handler
- **Phase 1 PLAN.md (01-03)** — Exact code already implemented: Router, UI, bindEvents with `// Auth.login() wired in Phase 2` comment
- **MDN Web Docs — SubtleCrypto.digest()** — Async behavior confirmed; `await` mandatory
- **MDN Web Docs — localStorage** — `Storage.remove()` for session deletion on logout

### Secondary (MEDIUM confidence)

- None required — all critical facts verified via project spec documents

### Tertiary (LOW confidence)

- None

---

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH — No new libraries introduced; all APIs established in Phase 1 foundation
- Architecture: HIGH — Exact module structure, function signatures, and data shapes from project spec documents
- Pitfalls: HIGH — All pitfalls derived from Phase 1 research findings (async SHA-256 chain) plus direct analysis of the spec requirements

**Research date:** 2026-04-16
**Valid until:** 2026-10-16 (stable web APIs; 6 months — no third-party dependencies to track)

---

*Phase 2 Authentication research complete. Key insight: this phase is almost entirely wiring existing infrastructure. The Auth namespace is ~30 lines; the main risk is the async event handler chain. Router.start() session-on-load is already done. The planner should structure tasks as: (1) add CSS for header/dashboard, (2) add Auth + escapeHtml, (3) update UI.renderAdminDashboard + renderUserDashboard, (4) update UI.bindEvents for submit + logout, (5) human verify all 4 success criteria.*

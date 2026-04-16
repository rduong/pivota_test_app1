# Phase 1: Foundation - Research

**Researched:** 2026-04-16
**Domain:** Vanilla HTML/CSS/JS SPA — no framework, no build step; localStorage persistence; SubtleCrypto SHA-256 hashing; client-side routing; Google Fonts Outfit
**Confidence:** HIGH

---

## Summary

Phase 1 establishes the complete application shell for a pure vanilla JS SPA: a styled LOGIN view with the Outfit font, a client-side state machine router covering 5 views, localStorage persistence with namespace safety and parse-error resilience, and idempotent first-run bootstrapping that seeds a default admin account and displays a first-run notice.

The stack is locked: **zero dependencies, single `index.html` file** (or optionally `index.html + styles.css + app.js`), with JavaScript organized into namespace objects (Config, Storage, Auth, Users, InviteCodes, Todos, Router, UI). No `import`/`export`, no bundler, no Node.js. The architecture is State-Driven View Pattern: localStorage → plain JS objects → DOM manipulation.

The single most important technical constraint for this phase is that **`SubtleCrypto.digest()` is async** (returns a Promise). The app must use `async/await` for all password hashing, meaning the entire `Bootstrap.init()` startup sequence and all login/register flows must be async. The `file://` protocol is explicitly listed as a "potentially trustworthy origin" by MDN, so `SubtleCrypto` and `crypto.randomUUID()` work correctly when the file is opened locally — no HTTPS server required.

**Primary recommendation:** Structure the entire app startup as an `async function init()` called with `document.addEventListener('DOMContentLoaded', init)`. All password hashing awaits `SubtleCrypto.digest()`. The first-run bootstrap check runs inside `init()` before any view is rendered.

---

## Standard Stack

### Core
| Technology | Version / Source | Purpose | Why Standard |
|------------|------------------|---------|--------------|
| HTML5 | Living Standard | Single document, view containers | Required by spec (F0) |
| CSS3 Custom Properties | Living Standard | Design tokens (colors, type scale, spacing) | Enables consistent theming without preprocessor |
| Vanilla JavaScript (ES2020+) | Chrome 90+, FF88+, Safari 14+ | All app logic, DOM manipulation | No-framework decision locked in TechArch |
| `localStorage` | Web Storage API (Baseline: 2015) | All data persistence | Required by F7; works in `file://` |
| `crypto.randomUUID()` | Web Crypto API (Baseline: March 2022) | UUID generation for IDs | Required by TechArch; synchronous; works in `file://` |
| `SubtleCrypto.digest('SHA-256')` | Web Crypto API (Baseline: Jan 2020) | Password hashing | Required by TechArch; async; works in `file://` |
| Google Fonts (Outfit) | CDN: fonts.googleapis.com | Primary typeface (wgts 300–700) | Required by F0, UX-Mockup |

### Supporting
| Technology | Purpose | When to Use |
|------------|---------|-------------|
| `TextEncoder` | Encode string to `Uint8Array` for SubtleCrypto | Password hashing (encode password string before digest) |
| CSS Flexbox | Layout: login card centering, header, list rows | All layout positioning |
| CSS Grid | Two-column admin dashboard layout (≥768px) | Admin view on tablet/desktop |
| `Array.from(new Uint8Array(buffer)).map(b => b.toString(16).padStart(2,'0')).join('')` | Convert ArrayBuffer digest to hex string | Password hash display/storage |

### Alternatives Considered
| Instead of | Could Use | Tradeoff |
|------------|-----------|----------|
| `SubtleCrypto` SHA-256 | Simple `btoa()` encoding | `btoa` is NOT a hash — reversible, not suitable; SHA-256 is correct per TechArch |
| `crypto.randomUUID()` | `Math.random()` based ID | `Math.random()` is not cryptographically random; `crypto.randomUUID()` is available on all target browsers since 2022 |
| Inline `<style>` + `<script>` | Separate `styles.css` + `app.js` | Both valid; single file preferred for portability (TechArch Option A) |

**Installation:** No packages — all native browser APIs. No `npm install` required.

---

## Architecture Patterns

### Recommended Project Structure
```
index.html
├── <head>
│   ├── Google Fonts link (Outfit, weights 300–700)
│   └── <style> (embedded CSS)
└── <body>
    ├── <div id="app"> (single mount point for all views)
    └── <script> (all JavaScript)
        ├── const Config = { ... }    // App constants, localStorage keys
        ├── const Storage = { ... }   // localStorage read/write helpers
        ├── const Auth = { ... }      // Login, logout, session
        ├── const Users = { ... }     // User list CRUD
        ├── const InviteCodes = { ... }// Invite code management
        ├── const Todos = { ... }     // Todo CRUD per user
        ├── const Router = { ... }    // View state machine
        ├── const UI = { ... }        // Render functions + event bindings
        └── async function init() { ... } // Bootstrap + start
```

### Pattern 1: Namespace Object Pattern (No ES Modules)
**What:** All code in a single script context, organized as plain JS objects acting as namespaces.
**When to use:** Required — no `import`/`export`, no module bundler.
**Example:**
```javascript
// Source: TechArch-TodoApp.md §1.4
const Config = {
  KEYS: {
    USERS:   'todoapp_users',
    INVITES: 'todoapp_invites',
    SESSION: 'todoapp_session',
    TODOS:   (username) => `todoapp_todos_${username}`,
  },
  DEFAULTS: {
    ADMIN_USERNAME: 'admin',
    ADMIN_PASSWORD: 'admin123',
  }
};

const Storage = {
  get(key, fallback = null) {
    try {
      const raw = localStorage.getItem(key);
      return raw === null ? fallback : JSON.parse(raw);
    } catch (e) {
      console.warn(`[Storage] parse failed for key "${key}"`, e);
      return fallback;
    }
  },
  set(key, value) {
    localStorage.setItem(key, JSON.stringify(value));
  },
  remove(key) {
    localStorage.removeItem(key);
  }
};
```

### Pattern 2: Async Password Hashing with SubtleCrypto
**What:** `SubtleCrypto.digest()` returns a Promise. Use `async/await` throughout. Convert ArrayBuffer result to hex string.
**When to use:** All password operations — hashing at register/bootstrap, comparison at login.
**Example:**
```javascript
// Source: https://developer.mozilla.org/en-US/docs/Web/API/SubtleCrypto/digest (Dec 2025)
async function hashPassword(password) {
  const msgUint8 = new TextEncoder().encode(password);
  const hashBuffer = await window.crypto.subtle.digest('SHA-256', msgUint8);
  const hashArray = Array.from(new Uint8Array(hashBuffer));
  return hashArray.map(b => b.toString(16).padStart(2, '0')).join('');
}
```
**Note:** `Uint8Array.prototype.toHex()` was introduced in 2025 and is not yet universally supported. Use the `Array.from(...).map(...)` fallback for maximum compatibility with the target browsers (Chrome 90+, Safari 14+).

### Pattern 3: Async Init / Bootstrap Pattern
**What:** Entire startup is async. Bootstrap checks for first-run state, seeds admin if needed, then routes to appropriate view.
**When to use:** App startup — required because SHA-256 hashing of the default admin password is async.
**Example:**
```javascript
// Source: FRD-TodoApp.md §FR-F8
const Bootstrap = {
  async init() {
    const users = Storage.get(Config.KEYS.USERS, []);
    if (users.length === 0) {
      // First run — seed admin account
      const passwordHash = await hashPassword(Config.DEFAULTS.ADMIN_PASSWORD);
      const adminUser = {
        username: Config.DEFAULTS.ADMIN_USERNAME,
        passwordHash,
        role: 'admin',
        registeredAt: new Date().toISOString(),
        inviteCode: null
      };
      Storage.set(Config.KEYS.USERS, [adminUser]);
      // Signal first-run state for notice display
      return { firstRun: true };
    }
    return { firstRun: false };
  }
};

document.addEventListener('DOMContentLoaded', async () => {
  const { firstRun } = await Bootstrap.init();
  Router.start(firstRun);
});
```

### Pattern 4: View State Machine Router
**What:** A `Router` object with `currentView`, `currentContext`, `navigate(view, context)`, and `render()` methods. All view transitions go through `navigate()`.
**When to use:** All navigation in the app.
**Example:**
```javascript
// Source: TechArch-TodoApp.md §3.2
const Router = {
  currentView: null,
  currentContext: {},
  navigate(view, context = {}) {
    const session = Storage.get(Config.KEYS.SESSION);
    // Role guard: admin views require admin session
    if (['ADMIN_DASHBOARD', 'ADMIN_VIEW_USER'].includes(view)) {
      if (!session || session.role !== 'admin') {
        this.navigate('USER_DASHBOARD');
        return;
      }
    }
    this.currentView = view;
    this.currentContext = context;
    this.render();
  },
  render() {
    const app = document.getElementById('app');
    switch (this.currentView) {
      case 'LOGIN':           app.innerHTML = UI.renderLogin(this.currentContext); break;
      case 'REGISTER':        app.innerHTML = UI.renderRegister(); break;
      case 'ADMIN_DASHBOARD': app.innerHTML = UI.renderAdminDashboard(); break;
      case 'USER_DASHBOARD':  app.innerHTML = UI.renderUserDashboard(); break;
      case 'ADMIN_VIEW_USER': app.innerHTML = UI.renderAdminViewUser(this.currentContext); break;
    }
    UI.bindEvents(this.currentView, this.currentContext);
  },
  start(firstRun = false) {
    const session = Storage.get(Config.KEYS.SESSION);
    if (!session) {
      this.navigate('LOGIN', { firstRun });
    } else if (session.role === 'admin') {
      this.navigate('ADMIN_DASHBOARD');
    } else {
      this.navigate('USER_DASHBOARD');
    }
  }
};
```

### Pattern 5: CSS Custom Properties Design System
**What:** CSS variables at `:root` level for all design tokens — colors, type scale, spacing.
**When to use:** Required — all components reference variables, never hard-coded values.
**Example:**
```css
/* Source: UX-Mockup-TodoApp.md §2, TechArch-TodoApp.md §3.3 */
:root {
  /* Color Palette */
  --color-bg:          #FFFFFF;
  --color-surface:     #F8F9FA;
  --color-border:      #E9ECEF;
  --color-primary:     #4361EE;
  --color-primary-hover: #3A56D4;
  --color-accent-admin: #7B2FBE;
  --color-text:        #212529;
  --color-text-muted:  #6C757D;
  --color-error:       #DC3545;
  --color-success:     #198754;
  --color-completed:   #ADB5BD;

  /* Typography */
  --font-family: 'Outfit', sans-serif;
  --font-size-h1:   28px;
  --font-size-h2:   20px;
  --font-size-h3:   16px;
  --font-size-body: 15px;
  --font-size-sm:   13px;
  --font-size-btn:  14px;

  /* Spacing Scale (8px base) */
  --space-xs:  4px;
  --space-sm:  8px;
  --space-md:  16px;
  --space-lg:  24px;
  --space-xl:  32px;
  --space-2xl: 48px;

  /* Border Radius */
  --radius-sm:   4px;
  --radius-md:   8px;
  --radius-lg:   12px;
  --radius-pill: 999px;
}
```

### Pattern 6: Google Fonts Outfit Link Tag
**What:** Load Outfit font from Google Fonts CDN in `<head>`. Include `font-display: swap` (handled by Google Fonts automatically), specify weights 300–700.
**Example:**
```html
<!-- Source: verified via https://fonts.googleapis.com/css2?family=Outfit:wght@300;400;500;600;700&display=swap -->
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Outfit:wght@300;400;500;600;700&display=swap" rel="stylesheet">
```

### Anti-Patterns to Avoid
- **Synchronous SHA-256 assumption:** `SubtleCrypto.digest()` returns a Promise. Never call it without `await`. Never assume it's synchronous.
- **Inline `onXxx` attributes:** Do not use `onclick="..."` in HTML strings. Bind events after `innerHTML` assignment in `bindEvents()` using `document.getElementById` or `querySelector`.
- **Calling `localStorage` directly:** Always use `Storage.get()` and `Storage.set()` wrappers with try/catch. Direct access is fine for simple apps but violates the parse-error resilience requirement (F7.3).
- **Forgetting null check on `getItem`:** `localStorage.getItem()` returns `null` (not `undefined`) for missing keys. `JSON.parse(null)` returns `null` in modern JS — handle this as the `fallback` case.
- **Re-seeding on every load:** Bootstrap MUST check `users.length === 0` before seeding. If users array already has entries, do nothing (idempotency requirement, FR-F8.4).
- **Losing form field values on error:** On validation error, re-render the form pre-populated with the submitted values. Don't reset the whole form.

---

## Don't Hand-Roll

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Password hashing | Custom hash function | `SubtleCrypto.digest('SHA-256')` | Native browser API; available since 2020; deterministic |
| UUID / ID generation | `Math.random()`-based ID | `crypto.randomUUID()` | Cryptographically secure; native since March 2022; 36-char UUID v4 |
| localStorage safe access | Scattered try/catch | `Storage.get(key, fallback)` wrapper | Centralizes error handling; prevents parse failures from crashing app |
| View state transitions | Direct DOM manipulation everywhere | `Router.navigate(view, context)` | Enforces role guards, single render path, prevents inconsistent state |

**Key insight:** All necessary APIs are native to modern browsers. No libraries needed. The risk is in *how* they are used (async patterns, error handling), not which library to choose.

---

## Common Pitfalls

### Pitfall 1: Forgetting `await` on SubtleCrypto
**What goes wrong:** `passwordHash` is a `Promise` object, not a hex string. Stored as `[object Promise]` in localStorage. Login never succeeds — hash comparison always fails.
**Why it happens:** Developers treat `hashPassword()` as synchronous. The function is `async` so it always returns a Promise, even if the `await` inside resolves immediately.
**How to avoid:** The entire call chain must be async. `Bootstrap.init()` must be `async`. `Auth.login()` must be `async`. Event handlers that call these must use `async function(e) { ... }`.
**Warning signs:** `passwordHash` value in localStorage starts with `Promise {` or `[object`. Login always returns "Invalid username or password".

### Pitfall 2: `SubtleCrypto` Unavailable in Non-Secure Contexts (Non-file:// HTTP)
**What goes wrong:** App hosted on `http://` (not `https://`) fails with `TypeError: Cannot read properties of undefined (reading 'digest')`.
**Why it happens:** `window.crypto.subtle` is `undefined` in non-secure HTTP contexts. `SubtleCrypto` requires a secure context.
**How to avoid:** For this app, `file://` URLs are classified as "potentially trustworthy" by MDN (verified: Nov 2025 MDN docs). The TechArch target is `file://` or HTTPS — both are safe. If accessed via plain `http://`, the app should gracefully degrade.
**Warning signs:** Console error: `Cannot read properties of undefined (reading 'digest')`. Check `window.isSecureContext` to confirm.
**Mitigation:** Add a guard in `init()`:
```javascript
if (!window.crypto?.subtle) {
  document.getElementById('app').innerHTML = '<p class="error">This app requires a secure context (HTTPS or local file). Please open index.html directly or use HTTPS.</p>';
  return;
}
```

### Pitfall 3: localStorage QuotaExceededError
**What goes wrong:** `localStorage.setItem()` throws `QuotaExceededError` (DOMException) when storage is full. If uncaught, this crashes the app.
**Why it happens:** localStorage has a 5MB limit per origin. Unlikely for this app's data, but possible with many todos.
**How to avoid:** Wrap `localStorage.setItem()` in try/catch in `Storage.set()`. Log a warning; re-throw or handle gracefully.
**Warning signs:** `DOMException: Failed to execute 'setItem' on 'Storage': Setting the value of 'todoapp_todos_...' exceeded the quota.`

### Pitfall 4: innerHTML Event Binding Race Condition
**What goes wrong:** `document.getElementById('login-form')` returns `null` because it's called before `innerHTML` assignment completes, OR because the element ID doesn't exist in the rendered template.
**Why it happens:** Binding events in a separate pass after `innerHTML = renderLogin()` works, but only if the IDs in the template exactly match what the binding code queries.
**How to avoid:** Always set `innerHTML` first, then call `UI.bindEvents()`. Use consistent, documented element IDs. Define element IDs as constants in `Config` to prevent typos.
**Warning signs:** `Cannot read properties of null (reading 'addEventListener')` — element not found.

### Pitfall 5: First-Run Notice Showing After Initial Login
**What goes wrong:** The first-run banner keeps showing on subsequent visits because it's derived from `users.length === 0` on every page load, but after bootstrapping, there IS a user — so the banner shows only on truly empty storage.
**What the requirement actually says (FR-F8.3):** Show notice "when app is in first-run state OR on the first render of the login view after seeding." The banner should be dismissible or auto-hide after successful login.
**How to avoid:** The `Router.start(firstRun)` approach — pass `firstRun` flag from Bootstrap result to the login render, so notice only shows once per fresh install. Dismiss/hide on successful login transition. The flag is derived from whether Bootstrap just performed the seed (not from checking users array again later).

### Pitfall 6: CSS `font-display` and Outfit Fallback
**What goes wrong:** Login view shows with incorrect font briefly (flash of unstyled text) or the layout shifts because Outfit has different metrics than `sans-serif`.
**Why it happens:** Google Fonts CDN request is async. If user has slow/no connection, the fallback font renders first.
**How to avoid:** Google Fonts already includes `font-display: swap` in the served CSS (verified via CDN request). Specify a close system fallback: `font-family: 'Outfit', Arial, sans-serif`. Since F0 requires "no console errors" and font load errors are silent (not console errors), this is aesthetic only.
**Warning signs:** Brief flash of different font; layout shifts on slow connections.

### Pitfall 7: `crypto.randomUUID()` Not Available in Safari < 15.4
**What goes wrong:** `crypto.randomUUID` is `undefined` on Safari 14–15.3, causing `TypeError: crypto.randomUUID is not a function`.
**Why it happens:** `crypto.randomUUID()` became available in Safari 15.4 (March 2022), but TechArch targets Safari 14+.
**How to avoid:** The TechArch document lists `crypto.randomUUID()` as requiring Safari 15.4+ but still targets Safari 14+. For this phase (foundation), randomUUID is only needed for session token. If Safari 14 support is a hard requirement, add a polyfill. Recommended: check availability and fall back to `crypto.getRandomValues()` if needed.
```javascript
function generateUUID() {
  if (crypto.randomUUID) return crypto.randomUUID();
  // Polyfill using getRandomValues (available in all targets)
  return ([1e7]+-1e3+-4e3+-8e3+-1e11).replace(/[018]/g, c =>
    (c ^ crypto.getRandomValues(new Uint8Array(1))[0] & 15 >> c / 4).toString(16)
  );
}
```

---

## Code Examples

Verified patterns from official sources:

### localStorage Read with Error Resilience
```javascript
// Source: FRD-TodoApp.md §FR-F7.3, TechArch-TodoApp.md §2.3
// Pattern verified against MDN Web Storage API (Feb 2025)
function safeGet(key, fallback = null) {
  try {
    const raw = localStorage.getItem(key);
    if (raw === null) return fallback;
    return JSON.parse(raw);
  } catch (e) {
    console.warn(`[Storage] Failed to parse key "${key}":`, e);
    return fallback;
  }
}
```

### SHA-256 Password Hash to Hex String
```javascript
// Source: https://developer.mozilla.org/en-US/docs/Web/API/SubtleCrypto/digest (Dec 28, 2025)
async function hashPassword(password) {
  const msgUint8 = new TextEncoder().encode(password);
  const hashBuffer = await window.crypto.subtle.digest('SHA-256', msgUint8);
  // Uint8Array.toHex() is available since 2025; use Array fallback for Safari 14+ compat
  const hashArray = Array.from(new Uint8Array(hashBuffer));
  return hashArray.map(b => b.toString(16).padStart(2, '0')).join('');
}
```

### First-Run Bootstrap (Idempotent)
```javascript
// Source: FRD-TodoApp.md §FR-F8.1–FR-F8.4
async function bootstrap() {
  const users = safeGet('todoapp_users', []);
  if (users.length > 0) return { firstRun: false };  // Already initialized
  
  const passwordHash = await hashPassword('admin123');
  const adminUser = {
    username: 'admin',
    passwordHash,
    role: 'admin',
    registeredAt: new Date().toISOString(),
    inviteCode: null
  };
  localStorage.setItem('todoapp_users', JSON.stringify([adminUser]));
  return { firstRun: true };
}
```

### Session Check and Route-on-Load
```javascript
// Source: FRD-TodoApp.md §FR-F1.3, TechArch-TodoApp.md §3.1
function routeOnLoad(firstRun) {
  const session = safeGet('todoapp_session');
  if (!session) {
    renderLogin({ firstRun });
  } else if (session.role === 'admin') {
    renderAdminDashboard();
  } else {
    renderUserDashboard();
  }
}
```

### Google Fonts Outfit — Verified CDN URL (April 2026)
```html
<!-- Verified: fonts.googleapis.com returns correct Outfit CSS with font-display: swap -->
<!-- Weights available and in use: 300 (light), 400 (regular), 500 (medium), 600 (semibold), 700 (bold) -->
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Outfit:wght@300;400;500;600;700&display=swap" rel="stylesheet">
```

### Login View HTML Structure (Minimal)
```html
<!-- Source: UX-Mockup-TodoApp.md §Screen 1 -->
<div id="app">
  <main class="auth-page">
    <h1 class="app-title">TodoApp</h1>
    
    <!-- First-run notice — shown only when firstRun=true -->
    <div class="first-run-notice" role="status" aria-live="polite">
      ⓘ First time setup: Login with <strong>admin</strong> / <strong>admin123</strong>
    </div>
    
    <div class="auth-card">
      <form id="login-form" novalidate>
        <div class="field-group">
          <label for="username">Username</label>
          <input type="text" id="username" name="username" 
                 autocomplete="username" maxlength="30" required>
          <span class="field-error" id="username-error" role="alert" aria-live="assertive"></span>
        </div>
        
        <div class="field-group">
          <label for="password">Password</label>
          <input type="password" id="password" name="password" 
                 autocomplete="current-password" required>
          <span class="field-error" id="password-error" role="alert" aria-live="assertive"></span>
        </div>
        
        <button type="submit" id="login-btn" class="btn btn-primary">Log In</button>
        
        <p class="auth-link">
          Don't have an account? <a href="#" id="go-register">Register →</a>
        </p>
      </form>
    </div>
  </main>
</div>
```

---

## State of the Art

| Old Approach | Current Approach | When Changed | Impact |
|--------------|------------------|--------------|--------|
| `Array.from(new Uint8Array(buffer)).map(...).join('')` for hex | `new Uint8Array(buffer).toHex()` | 2025 (not in all targets) | Use Array fallback for Safari 14+ compat |
| `Math.random()` for IDs | `crypto.randomUUID()` | Widely available since March 2022 | Cryptographically secure IDs; simpler code |
| Explicit `storageAvailable()` check | Direct try/catch on operations | Modern practice | Simpler; catches specific errors (QuotaExceeded) |

**Deprecated/outdated:**
- `btoa()` for password "hashing": This encodes, not hashes. It's reversible. The TechArch explicitly requires `SubtleCrypto.digest('SHA-256')`.
- `SHA-1` for digest: Don't use — considered vulnerable. Use SHA-256 (specified in TechArch).

---

## Open Questions

1. **Safari 14 vs `crypto.randomUUID()` discrepancy**
   - What we know: TechArch targets Safari 14+ but `crypto.randomUUID()` requires Safari 15.4+
   - What's unclear: Is Safari 14 a hard requirement or aspirational? Does the app need to work on Safari 14?
   - Recommendation: Implement `generateUUID()` polyfill using `crypto.getRandomValues()` as fallback. Zero cost, eliminates the gap. Include in Phase 1 foundation code.

2. **`SubtleCrypto` on plain HTTP (not file://)**
   - What we know: `file://` is a "potentially trustworthy origin" per MDN; SubtleCrypto works there. Plain `http://` (not localhost) does NOT qualify.
   - What's unclear: Will users ever serve this over plain HTTP? The TechArch says file:// or static host (likely HTTPS).
   - Recommendation: Add a guard check in `init()` and show a helpful error message if `window.crypto?.subtle` is undefined. Minimal cost, prevents confusing errors.

3. **First-run notice persistence strategy**
   - What we know: FR-F8.3 says the notice should be dismissible or auto-hide after successful login.
   - What's unclear: Should the notice re-appear if the user visits the login page again after logging out the first time, or only on the very first ever load?
   - Recommendation: Show the notice only when `Router.start()` is called with `firstRun: true` (i.e., only in the same session where Bootstrap just seeded). After login, the notice disappears. On subsequent visits (Bootstrap returns `firstRun: false`), the notice is not shown. This is the simplest correct behavior.

---

## Sources

### Primary (HIGH confidence)
- MDN Web Docs — `SubtleCrypto.digest()` — https://developer.mozilla.org/en-US/docs/Web/API/SubtleCrypto/digest (last modified Dec 28, 2025)
- MDN Web Docs — `crypto.randomUUID()` — https://developer.mozilla.org/en-US/docs/Web/API/Crypto/randomUUID (last modified Sep 25, 2024)
- MDN Web Docs — Web Storage API Guide — https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API/Using_the_Web_Storage_API (last modified Feb 22, 2025)
- MDN Web Docs — Secure Contexts — https://developer.mozilla.org/en-US/docs/Web/Security/Defenses/Secure_Contexts (last modified Nov 30, 2025) — confirms `file://` is "potentially trustworthy"
- MDN Web Docs — Non-cryptographic uses of SubtleCrypto — https://developer.mozilla.org/en-US/docs/Web/API/Web_Crypto_API/Non-cryptographic_uses_of_subtle_crypto (last modified Oct 28, 2025)
- Google Fonts CDN — Outfit font CSS — https://fonts.googleapis.com/css2?family=Outfit:wght@300;400;500;600;700&display=swap (verified April 2026 — returns correct @font-face with font-display: swap)
- TechArch-TodoApp.md — Full architecture, module org, data model, type definitions
- FRD-TodoApp.md — FR-F0, FR-F7, FR-F8 detailed behavior requirements
- UX-Mockup-TodoApp.md — Login/Register/Dashboard wireframes, color palette, typography scale

### Secondary (MEDIUM confidence)
- None required — all critical facts verified via MDN official docs

### Tertiary (LOW confidence)
- None

---

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH — All technologies are native browser APIs verified via MDN (2024-2025 dates); no third-party libraries
- Architecture: HIGH — Directly specified in TechArch-TodoApp.md and FRD-TodoApp.md (both project-spec documents)
- Pitfalls: HIGH — `SubtleCrypto` async behavior and secure-context requirement verified via MDN official docs; Safari 14 gap verified from MDN compatibility table

**Research date:** 2026-04-16
**Valid until:** 2026-10-16 (stable web APIs; 6 months)

---

*Phase 1 Foundation research complete. Key insight: this phase is pure browser API work — no libraries, no framework, no build. The primary architectural challenge is correctly handling async SHA-256 hashing throughout the app initialization and event handling chain.*

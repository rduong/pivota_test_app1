# Technical Architecture Document
## Todo List App with User Management (TodoApp)

**Version:** 1.0  
**Date:** 2026-04-15  
**Status:** Validated  
**Sources:** PRD-TodoApp.md, FRD-TodoApp.md  

---

## 1. Architectural Approach

### 1.1 Architecture Philosophy

TodoApp follows a **zero-dependency, single-origin static SPA** architecture. The entire application — logic, styling, and markup — is delivered as a static file that runs entirely in the browser. There is no build step, no bundler, no runtime server, and no network requests except for the initial Google Fonts CDN load.

The architecture is driven by three principles:
1. **Portability:** The app must function when opened as a local `file://` URL
2. **Simplicity:** No framework overhead; plain JavaScript handles all state and DOM management
3. **Persistence:** All state lives in `localStorage`; nothing is lost on page refresh

### 1.2 Architectural Pattern

The app uses a **State-Driven View Pattern** (a simplified MVC without a framework):

- **Model:** Plain JavaScript objects + `localStorage` as the backing store
- **View:** DOM manipulation functions that render HTML strings or modify DOM nodes
- **Controller:** Event handlers that call model functions and trigger view re-renders

There is no virtual DOM, no reactive data binding, and no component lifecycle. State changes trigger explicit re-render calls on the affected UI region.

```
User Action
    ↓
Event Handler (Controller)
    ↓
Data Mutation (Model) → localStorage write
    ↓
View Re-render (View) → DOM update
```

### 1.3 File Structure

The application can be delivered in one of two equivalent structures:

**Option A — Single File (Recommended for portability):**
```
index.html          (HTML + embedded <style> + embedded <script>)
```

**Option B — Minimal Multi-File:**
```
index.html          (HTML skeleton + link/script tags)
styles.css          (All CSS)
app.js              (All JavaScript)
```

Both options are functionally identical. The single-file option (`index.html`) is the canonical delivery format.

### 1.4 Module Organization (within app.js or script tag)

JavaScript is organized into logical namespaces / IIFE modules within the single script:

```
├── Config            — App constants (localStorage keys, defaults)
├── Storage           — localStorage read/write helpers
├── Auth              — Login, logout, session management
├── Users             — User registration, user list
├── InviteCodes       — Code generation, validation, listing
├── Todos             — CRUD operations per user
├── Router            — View state machine and navigation
└── UI                — DOM rendering functions, event bindings
```

No ES module imports (`import`/`export`) are used; all code is in a single JavaScript execution context using the module namespace pattern or IIFE scope.

---

## 2. Data Model

### 2.1 localStorage Schema

All application data is stored in `localStorage` under namespaced keys. The entire data layer is a thin wrapper around `JSON.parse` / `JSON.stringify` calls.

**Key Definitions:**

| Key | Value Type | Description |
|---|---|---|
| `todoapp_users` | `User[]` | All registered user accounts |
| `todoapp_invites` | `InviteCode[]` | All generated invite codes |
| `todoapp_todos_{username}` | `Todo[]` | Per-user todo items (dynamic key) |
| `todoapp_session` | `Session \| null` | Current active session |

### 2.2 Type Interfaces (JavaScript object shapes)

```typescript
// User Account
interface User {
  username: string;           // 3–30 chars, alphanumeric + underscore
  passwordHash: string;       // Client-side hash of password
  role: 'admin' | 'user';
  registeredAt: string;       // ISO 8601 timestamp
  inviteCode: string | null;  // Code used to register; null for seeded admin
}

// Invite Code
interface InviteCode {
  code: string;               // 8-char uppercase alphanumeric
  createdAt: string;          // ISO 8601 timestamp
  createdBy: string;          // Admin username
  usedBy: string | null;      // Username of user who redeemed it
  usedAt: string | null;      // ISO 8601 timestamp of redemption
}

// Todo Item
interface Todo {
  id: string;                 // UUID v4
  title: string;              // 1–200 chars
  description: string | null; // Optional, max 1000 chars
  completed: boolean;         // Default: false
  createdAt: string;          // ISO 8601 timestamp
  updatedAt: string;          // ISO 8601 timestamp (same as createdAt on creation)
}

// Session
interface Session {
  username: string;
  role: 'admin' | 'user';
  token: string;              // Random UUID (for future use / CSRF prevention)
  loginAt: string;            // ISO 8601 timestamp
}
```

### 2.3 Data Access Patterns

**Read Pattern:**
```javascript
function getUsers() {
  try {
    return JSON.parse(localStorage.getItem('todoapp_users') || '[]');
  } catch (e) {
    console.warn('todoapp_users parse failed', e);
    return [];
  }
}
```

**Write Pattern:**
```javascript
function saveUsers(users) {
  localStorage.setItem('todoapp_users', JSON.stringify(users));
}
```

**Per-user todos key:**
```javascript
const TODOS_KEY = (username) => `todoapp_todos_${username}`;
```

### 2.4 Password Hashing

Since there is no backend, passwords are hashed client-side using a deterministic method. The recommended approach is a simple hash function (e.g., a custom `hashString()` using `TextEncoder` + `SubtleCrypto.digest('SHA-256', ...)`) or an equivalent synchronous approach if async APIs are undesirable.

**Note:** Client-side hashing provides obfuscation, not true security. This is acceptable and documented as a known limitation for a client-side-only app with no sensitive data. The stored hash MUST NOT be the plaintext password.

### 2.5 UUID / ID Generation

For todo IDs and invite code generation, the app MUST use `crypto.randomUUID()` (available in all modern browsers) or a polyfill equivalent. Invite codes use the first 8 characters of a UUID converted to uppercase.

```javascript
function generateInviteCode() {
  return crypto.randomUUID().replace(/-/g, '').substring(0, 8).toUpperCase();
}
```

---

## 3. Component Architecture

### 3.1 View State Machine

The application has 5 view states managed by a `Router` module:

```
                    ┌──────────────────────────────────────┐
                    │         Application Start            │
                    └────────────────┬─────────────────────┘
                                     ↓
                           Check localStorage session
                    ┌────────────────┴─────────────────────┐
                    ↓ no session                    ↓ session exists
              ┌──────────┐                ┌─────────────────────┐
              │  LOGIN   │                │  Role-based routing  │
              └──────────┘                └──────────┬──────────┘
               ↓       ↓                    ↓                ↓
         Register   Login             ADMIN_DASHBOARD   USER_DASHBOARD
              ↓       ↓ (role-based)
         REGISTER  ADMIN_DASHBOARD
                   USER_DASHBOARD
```

**Full State Transitions:**

| From State | Action | To State |
|---|---|---|
| (any) | App load, no session | LOGIN |
| (any) | App load, admin session | ADMIN_DASHBOARD |
| (any) | App load, user session | USER_DASHBOARD |
| LOGIN | Click register link | REGISTER |
| LOGIN | Successful admin login | ADMIN_DASHBOARD |
| LOGIN | Successful user login | USER_DASHBOARD |
| REGISTER | Successful registration | USER_DASHBOARD |
| REGISTER | Click login link | LOGIN |
| ADMIN_DASHBOARD | Click "View Todos" for user X | ADMIN_VIEW_USER (with context: username) |
| ADMIN_VIEW_USER | Click "Back" | ADMIN_DASHBOARD |
| (any authenticated) | Click Logout | LOGIN |

### 3.2 UI Module Structure

**Router Module:**
```javascript
const Router = {
  currentView: null,
  currentContext: {},       // e.g., { viewingUser: 'jordan' } for ADMIN_VIEW_USER
  navigate(view, context = {}) { ... },
  render() { ... }          // calls the correct render function for currentView
};
```

**UI Rendering Functions (one per view):**
- `UI.renderLogin()` — login form HTML
- `UI.renderRegister()` — registration form HTML
- `UI.renderAdminDashboard()` — invite codes + user list
- `UI.renderUserDashboard()` — todo list + add form + filters
- `UI.renderAdminViewUser(username)` — read-only todo list for target user

**Event Binding:**  
Each render function also binds event listeners immediately after DOM injection. Event delegation on parent containers is preferred over per-element listeners.

### 3.3 CSS Architecture

All styles are organized into sections within the single stylesheet:

```
1. Reset & base styles
2. Typography (Outfit font, scale)
3. Layout (container, grid/flex wrappers)
4. Navigation / header
5. Forms (inputs, labels, buttons, error states)
6. Todo list item styles
7. Admin panels (invite code list, user list)
8. Filter tabs
9. Empty states
10. Badge / role indicators
11. Responsive media queries (≥375px base, 768px tablet, 1024px desktop)
```

CSS Custom Properties (variables) MUST be used for:
- Primary color palette (brand color, accent, text, background)
- Typography scale (font sizes for heading, body, small)
- Spacing scale (8px base unit: 8, 16, 24, 32, 48)

### 3.4 Security Considerations

**Client-Side Access Control:**  
Every view render function checks the current session role before rendering admin or user content. Admin views check `session.role === 'admin'` at the router level; if a user somehow navigates to an admin state, the router re-routes them to USER_DASHBOARD.

```javascript
Router.navigate = function(view, context = {}) {
  const session = Storage.getSession();
  if (['ADMIN_DASHBOARD', 'ADMIN_VIEW_USER'].includes(view)) {
    if (!session || session.role !== 'admin') {
      this.navigate('USER_DASHBOARD');
      return;
    }
  }
  // ... proceed with navigation
};
```

**Known Limitations (documented):**
- localStorage can be read/modified via browser DevTools by any user
- Client-side password hashing does not provide cryptographic security equivalent to server-side bcrypt
- Invite code brute-forcing is theoretically possible; mitigated by 8-char random codes (~2.8 trillion combinations)
- These limitations are acceptable for a zero-backend, non-sensitive-data application

---

## 4. Browser Compatibility & Performance

### 4.1 Target Browsers
- Chrome 90+
- Firefox 88+
- Safari 14+
- Edge 90+

### 4.2 APIs Used

| API | Usage | Availability |
|---|---|---|
| `localStorage` | Data persistence | All target browsers |
| `crypto.randomUUID()` | ID and invite code generation | Chrome 92+, Firefox 95+, Safari 15.4+ |
| `navigator.clipboard.writeText()` | Copy invite code | Chrome 66+, Firefox 63+, Safari 13.1+ |
| `SubtleCrypto.digest()` | Password hashing | All target browsers (async) |
| Google Fonts CDN | Outfit font | Requires internet; graceful fallback to sans-serif |

### 4.3 Performance Targets

- Initial page load (cold, broadband): < 2 seconds
- localStorage read/write operations: < 50ms
- DOM re-render for todo list (up to 100 items): < 16ms (one frame)
- Total JavaScript: < 500 lines
- Total CSS: < 300 lines

---

## 5. Deployment Model

The application is deployed by providing the static file(s):

**Local Use:** Open `index.html` directly in a browser (file:// protocol)  
**Web Hosting:** Upload to any static file host (GitHub Pages, Netlify, S3 static, etc.)  
**No server required:** No Node.js, no web server, no environment variables

---

*Document generated by Pivota Spec TechArch Generator | TodoApp v1.0 | 2026-04-15*

# Personas Document
## Todo List App with User Management (TodoApp)

**Version:** 1.0  
**Date:** 2026-04-15  
**Status:** Validated  
**Source:** PRD-TodoApp.md  

---

## 1. Overview

This document defines the user personas for TodoApp. Two primary personas represent the full spectrum of users: an administrative power user who manages team access, and a regular member who uses the app for daily task management. A third edge-case persona captures non-technical users who may struggle with the invite flow.

---

## 2. Persona Profiles

### PER-01: Alex — The Team Admin

**Role:** Admin  
**Age:** 32  
**Occupation:** Project Manager / Team Lead  
**Technical Proficiency:** Moderate-to-High  

**Background:**  
Alex manages a small cross-functional team of 4–8 people. They are comfortable with web tools and already use browser-based apps for task tracking, but need something lightweight and fully self-contained — no IT provisioning, no SaaS sign-ups, no monthly billing. Alex was given the TodoApp to run for a short sprint cycle or internal project.

**Goals:**
- Quickly set up the app and get teammates onboarded within minutes
- Generate invite codes and share them with team members easily
- Monitor team progress by checking individual users' todo lists
- Keep admin access secure while making user access frictionless

**Frustrations:**
- Heavyweight project management tools with steep learning curves
- Having to manage user accounts through a separate admin panel or email flow
- No visibility into what team members have on their plate

**Behaviors:**
- Logs in first, generates all invite codes upfront before inviting the team
- Checks in on team todo lists weekly or after milestones
- Occasionally needs to verify whether a particular user has registered

**Key Scenarios:**
- First-run setup: Logs in with default credentials, generates invite codes, shares them with team
- Ongoing oversight: Navigates to a team member's todo list to see their progress
- Access review: Views list of all users to confirm everyone has registered

---

### PER-02: Jordan — The Everyday User

**Role:** User  
**Age:** 27  
**Occupation:** Developer / Analyst / Contributor  
**Technical Proficiency:** Moderate  

**Background:**  
Jordan is a team member who received an invite code from Alex. They use the app daily to keep track of their assigned tasks, personal action items, and reminders. Jordan values simplicity — they want to add tasks quickly, check things off, and get back to their actual work.

**Goals:**
- Register quickly using the invite code without friction
- Add, complete, and remove tasks with minimal clicks
- See their task list clearly without clutter
- Know their data will be there next time they open the browser

**Frustrations:**
- Overly complex UIs for a simple todo list
- Having to re-enter tasks after a browser refresh
- Unclear registration flows that make them feel like they're doing something wrong

**Behaviors:**
- Opens the app at the start of the day to review their task list
- Adds new tasks throughout the day as they come up
- Marks tasks complete as they finish them
- Returns after a weekend and expects all data to still be there

**Key Scenarios:**
- Onboarding: Receives invite code, registers, and adds first task within 2 minutes
- Daily use: Logs in, marks completed tasks, adds new tasks, logs out
- Returning user: Opens app, auto-login resumes session, sees yesterday's tasks

---

### PER-03: Casey — The Non-Technical Invitee

**Role:** User (edge case)  
**Age:** 45  
**Occupation:** Office Manager / Non-Technical Staff  
**Technical Proficiency:** Low  

**Background:**  
Casey was invited by Alex to track their tasks but is not particularly tech-savvy. They are familiar with basic web forms and email but have never heard of "localStorage" and wouldn't know to look in browser developer tools. Casey's primary concern is whether the app will "remember" their tasks and whether they'll be able to figure out the registration form.

**Goals:**
- Register without confusion using the invite code
- Add tasks using a simple, clearly labeled form
- Find their existing tasks easily when returning to the app
- Get meaningful error messages if something goes wrong

**Frustrations:**
- Error messages that use technical jargon
- Forms that don't explain what's expected
- Losing data unexpectedly (e.g., after clearing browser history)

**Behaviors:**
- May mistype the invite code and need a clear error message
- Relies on clear labels and placeholder text to understand form fields
- Does not use keyboard shortcuts; relies entirely on mouse/tap interaction
- May clear browser cache/history, inadvertently wiping localStorage data

**Key Scenarios:**
- Failed registration: Types invite code incorrectly, sees helpful error, corrects and succeeds
- Clear data risk: Clears browser history, loses localStorage data — needs clear documentation warning
- Simple use: Adds a handful of tasks, marks them done, happy with the experience

---

## 3. Persona Summary

| Persona ID | Name | Role | Tech Level | Primary Goal |
|---|---|---|---|---|
| PER-01 | Alex — Team Admin | Admin | Moderate-High | Manage team access and oversee task progress |
| PER-02 | Jordan — Everyday User | User | Moderate | Manage personal todo list efficiently |
| PER-03 | Casey — Non-Technical Invitee | User | Low | Register and use the app without confusion |

---

## 4. Feature–Persona Matrix

This matrix maps each PRD feature to the personas that directly interact with or are impacted by it.

| Feature ID | Feature Name | PER-01 (Admin) | PER-02 (User) | PER-03 (Non-Tech User) |
|---|---|---|---|---|
| F0 | Application Shell & Navigation | ✓ Primary | ✓ Primary | ✓ Primary |
| F1 | User Authentication & Session Management | ✓ Primary | ✓ Primary | ✓ Primary |
| F2 | Invite Code Registration | ✓ Generates codes | ✓ Uses codes | ✓ Uses codes (struggles) |
| F3 | Admin — Invite Code Management | ✓ Primary | — | — |
| F4 | Admin — User Management | ✓ Primary | — | — |
| F5 | User — Todo Management (CRUD) | ✓ (own todos) | ✓ Primary | ✓ Primary |
| F6 | Admin — Todo Visibility (Read-Only) | ✓ Primary | — | — |
| F7 | Data Persistence Layer | ✓ Transparent | ✓ Transparent | ⚠ At-risk (clears browser data) |
| F8 | Default Admin Bootstrapping | ✓ Primary (first run) | — | — |

**Legend:** ✓ Primary = main user of this feature | ✓ = uses feature | ⚠ = at-risk interaction | — = not applicable

---

## 5. Design Implications per Persona

### For PER-01 (Alex — Admin):
- Admin dashboard must surface all users and invite codes without requiring navigation depth
- Invite code copy-to-clipboard is essential for sharing workflow
- Clear differentiation between admin and user views

### For PER-02 (Jordan — Everyday User):
- Todo CRUD actions should be reachable in ≤2 clicks
- Auto-login on page load removes daily friction
- Filter tabs (All / Active / Completed) support daily review workflow

### For PER-03 (Casey — Non-Technical):
- All form fields must have descriptive placeholder text and visible labels
- Error messages must be in plain language (no "localStorage error" or "undefined")
- First-run notice with default admin credentials must be clearly visible
- Warning about browser data clearing should appear in the UI footer or about section

---

*Document generated by Pivota Spec Personas Generator | TodoApp v1.0 | 2026-04-15*

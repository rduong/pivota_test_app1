# Customer Journey Maps
## Todo List App with User Management (TodoApp)

**Version:** 1.0  
**Date:** 2026-04-15  
**Status:** Validated  
**Sources:** PERSONAS-TodoApp.md, JTBD-TodoApp.md, PRD-TodoApp.md  

---

## 1. Overview

This document captures step-by-step journey maps for each persona across their primary scenarios. Journeys are organized by Persona × Scenario and use hierarchical IDs scoped to the persona.

**Journey ID format:** `JRN-{PER}.{NN}` (e.g., JRN-PER01.01 = first journey for PER-01)

**Touchpoint Key:**
- 🖥 Screen / View
- 👆 User Action
- 💭 Thought
- 😊/😐/😟 Emotion (positive / neutral / negative)
- ⚡ Pain Point
- ✅ Success Moment

---

## 2. Journeys for PER-01: Alex — The Team Admin

---

### JRN-PER01.01: First-Time Setup and Team Onboarding

**Scenario:** Alex opens the app for the first time, logs in, generates invite codes for 3 teammates, and verifies that all codes were created correctly.

**JTBD Alignment:** JOB-PER01-01 (Get the Team Onboarded Quickly)  
**Entry Point:** Opening the app URL or `index.html` for the first time  
**Exit Point:** Alex has 3 invite codes copied and ready to send

---

| Step | Touchpoint | Action | Thought | Emotion | Pain Points / Notes |
|---|---|---|---|---|---|
| 1 | 🖥 Login View | Sees the default login form | 💭 "I need the default login. Let me look for it." | 😐 Neutral — slightly uncertain | ⚡ Would be confused if no default credentials hint is shown |
| 2 | 🖥 Login View — First-Run Notice | Reads the "First time setup: admin / admin123" banner | 💭 "Great, right there. Easy." | 😊 Relieved | ✅ First-run notice removes any setup friction |
| 3 | 👆 Login Action | Types `admin` / `admin123`, clicks Log In | 💭 "Let's see the dashboard." | 😊 Confident | — |
| 4 | 🖥 Admin Dashboard | Lands on Admin Dashboard; sees invite code section and user list | 💭 "Where do I generate a code?" | 😐 Scanning — orienting to the UI | — |
| 5 | 👆 Generate Code | Clicks "Generate Invite Code" | 💭 "One click — perfect." | 😊 Pleased | — |
| 6 | 🖥 New Code Displayed | Sees the generated code with a "Copy" button | 💭 "I can copy it and paste into Slack." | 😊 Satisfied | ✅ Copy affordance reduces effort |
| 7 | 👆 Copy Code | Clicks Copy, sees "Copied!" confirmation | 💭 "Good. Now I'll generate two more for the others." | 😊 Confident | — |
| 8 | 👆 Generate 2 More | Repeats steps 5–7 twice more | 💭 "This is fast." | 😊 Efficient feel | — |
| 9 | 🖥 Invite Code List | Scrolls to see all 3 codes listed as "Unused" | 💭 "Perfect — I can track who uses each one." | 😊 In control | ✅ Visibility of code status is reassuring |
| 10 | 👆 Exit / Send Codes | Pastes codes into Slack messages to teammates | 💭 "Done. Everyone can register now." | 😊 Accomplished | ✅ Journey complete |

**Journey Outcome:** Alex successfully onboarded the team in under 5 minutes without reading any documentation.

**Key Moments of Truth:**
- First-run notice making credentials visible immediately (Step 2)
- One-click code generation with instant copy (Steps 5–7)
- Invite code list showing all codes and their status (Step 9)

---

### JRN-PER01.02: Checking Team Progress Mid-Sprint

**Scenario:** It's day 3 of a sprint. Alex wants to quickly review Jordan's and Casey's todo lists before the stand-up meeting.

**JTBD Alignment:** JOB-PER01-02 (Monitor Team Task Progress)  
**Entry Point:** Alex opens the app (auto-logged in as admin)  
**Exit Point:** Alex has seen both users' todo lists and closes the tab

---

| Step | Touchpoint | Action | Thought | Emotion | Pain Points / Notes |
|---|---|---|---|---|---|
| 1 | 🖥 Admin Dashboard | Auto-login resumes; sees Admin Dashboard | 💭 "No need to log in again — good." | 😊 Efficient | ✅ Auto-login saves time |
| 2 | 🖥 User List Section | Scrolls to user list, sees Jordan and Casey | 💭 "I can click into Jordan's list." | 😐 Focused | — |
| 3 | 👆 View Jordan's Todos | Clicks "View Todos" next to Jordan | 💭 "Let me see what they're working on." | 😐 Curious | — |
| 4 | 🖥 Admin View User — Jordan | Sees Jordan's todo list with all items, read-only | 💭 "Jordan has 5 active items. Looks reasonable." | 😊 Informed | ✅ Read-only view reassures — no risk of accidental changes |
| 5 | 👆 Back to Dashboard | Clicks "Back" to return to Admin Dashboard | 💭 "Now let me check Casey." | 😐 Neutral | — |
| 6 | 👆 View Casey's Todos | Clicks "View Todos" next to Casey | 💭 "Hope Casey has some tasks logged." | 😐 Slightly concerned | — |
| 7 | 🖥 Admin View User — Casey | Sees Casey's list; Casey has 2 todos | 💭 "Not much — I should check in with Casey." | 😐 Slightly worried | — |
| 8 | 👆 Back to Dashboard | Returns to Admin Dashboard | 💭 "All done. Stand-up is in 5 minutes." | 😊 Prepared | ✅ Journey complete |

**Journey Outcome:** Alex reviewed 2 users' task lists in under 2 minutes, fully prepared for the stand-up.

**Key Moments of Truth:**
- Auto-login removing friction on return (Step 1)
- Clean read-only view with clear attribution (Step 4)
- Fast back navigation between users (Steps 5–6)

---

### JRN-PER01.03: Verifying Registration Status

**Scenario:** 2 days after sending invite codes, Alex checks who has actually registered and which codes are still unused.

**JTBD Alignment:** JOB-PER01-03 (Verify Who Has Joined)  
**Entry Point:** Alex opens the app  
**Exit Point:** Alex knows which teammates haven't registered and has their still-valid codes ready to re-share

---

| Step | Touchpoint | Action | Thought | Emotion | Pain Points / Notes |
|---|---|---|---|---|---|
| 1 | 🖥 Admin Dashboard | Auto-login; lands on dashboard | 💭 "Let me check who's registered." | 😐 Purposeful | — |
| 2 | 🖥 User List | Reviews user list | 💭 "I see Jordan but not Casey." | 😐 Noting the gap | — |
| 3 | 🖥 Invite Code List | Scrolls to invite code list; sees 1 unused code | 💭 "The unused code must be Casey's. I can re-share it." | 😊 Informed | ✅ Code status directly answers the question |
| 4 | 👆 Copy Unused Code | Copies the still-valid code | 💭 "I'll ping Casey to try registering again." | 😊 Proactive | — |
| 5 | — | Closes the app | 💭 "Done." | 😊 Efficient | ✅ Journey complete in 1 minute |

---

## 3. Journeys for PER-02: Jordan — The Everyday User

---

### JRN-PER02.01: First-Time Registration and First Todo

**Scenario:** Jordan received an invite code in Slack and registers for the first time, then adds their first task.

**JTBD Alignment:** JOB-PER02-01 (Register and Start Tracking Immediately)  
**Entry Point:** Jordan opens the app URL for the first time (no session)  
**Exit Point:** Jordan has registered and added their first todo item

---

| Step | Touchpoint | Action | Thought | Emotion | Pain Points / Notes |
|---|---|---|---|---|---|
| 1 | 🖥 Login View | Sees login form with "Register" link | 💭 "I need to register first." | 😐 Oriented | — |
| 2 | 👆 Click Register | Clicks the Register link | 💭 "Registration form — straightforward." | 😐 Neutral | — |
| 3 | 🖥 Registration Form | Reads the form: username, password, confirm, invite code | 💭 "The invite code — I have it in Slack." | 😐 Checking Slack | — |
| 4 | 👆 Fill Form | Types username `jordan`, password, and pastes invite code | 💭 "All fields filled. Let's go." | 😐 Focused | — |
| 5 | 👆 Submit | Clicks "Create Account" | 💭 "Please work…" | 😐 Brief anticipation | — |
| 6 | 🖥 User Dashboard | Immediately routed to personal dashboard | 💭 "That was fast and easy." | 😊 Delighted | ✅ Instant transition to dashboard after registration |
| 7 | 🖥 Add Todo Form | Sees the add todo form prominent at top of dashboard | 💭 "Let me add my first task." | 😊 Motivated | — |
| 8 | 👆 Add Todo | Types "Set up development environment" and clicks Add | 💭 "Perfect." | 😊 Accomplished | — |
| 9 | 🖥 Todo List | Sees the new todo appear in the list | 💭 "My list is live." | 😊 Satisfied | ✅ Journey complete |

**Journey Outcome:** Jordan went from invite code to first task in under 2 minutes.

---

### JRN-PER02.02: Daily Task Management

**Scenario:** Jordan opens the app at the start of a workday, reviews their list, marks yesterday's tasks complete, and adds two new ones.

**JTBD Alignment:** JOB-PER02-02 (Manage Daily Tasks Without Friction)  
**Entry Point:** Jordan opens the app in a new tab  
**Exit Point:** Jordan's list reflects today's state and they close the tab

---

| Step | Touchpoint | Action | Thought | Emotion | Pain Points / Notes |
|---|---|---|---|---|---|
| 1 | 🖥 User Dashboard | Auto-login; lands on dashboard with yesterday's todos | 💭 "Good — everything's still here." | 😊 Relieved | ✅ Persistence working as expected |
| 2 | 🖥 Todo List | Reviews the list: 3 active, 1 completed | 💭 "I finished 'Set up dev env' yesterday." | 😐 Reviewing | — |
| 3 | 👆 Mark Complete | Clicks checkbox on "Set up development environment" | 💭 "Done and done." | 😊 Satisfying click | ✅ Completion feel |
| 4 | 👆 Mark Complete | Marks "Write unit tests" as complete | 💭 "Two down." | 😊 Productive | — |
| 5 | 👆 Add New Todo | Types "Review PR feedback" and clicks Add | 💭 "From today's stand-up." | 😐 Focused | — |
| 6 | 👆 Add New Todo | Types "Update README with setup steps" and clicks Add | 💭 "I keep forgetting this one." | 😐 Focused | — |
| 7 | 🖥 Updated List | List shows 2 completed (strikethrough) + 2 new active | 💭 "Looks right." | 😊 Organized | — |
| 8 | 👆 Filter to Active | Clicks "Active" filter | 💭 "Just see what's left." | 😐 Focused | ✅ Filter immediately useful |
| 9 | 🖥 Filtered List | Sees only 2 active items | 💭 "Clean. I know exactly what to do." | 😊 Clarity | ✅ Journey complete |

---

### JRN-PER02.03: Editing and Deleting Todos

**Scenario:** Jordan realizes a task title was unclear and needs to be edited, and another task is obsolete and should be deleted.

**JTBD Alignment:** JOB-PER02-02 (Manage Daily Tasks Without Friction)  
**Entry Point:** Jordan is on their User Dashboard  
**Exit Point:** One todo edited, one deleted

---

| Step | Touchpoint | Action | Thought | Emotion | Pain Points / Notes |
|---|---|---|---|---|---|
| 1 | 🖥 User Dashboard | Reviews todo list, spots a vague title "Call them" | 💭 "Who? I need to fix this." | 😐 Slightly frustrated | ⚡ Vague titles are a UX reminder, not an app issue |
| 2 | 👆 Edit Todo | Clicks "Edit" on the todo | 💭 "I can edit inline — good." | 😐 Focused | — |
| 3 | 🖥 Edit Form | Edit form opens pre-filled with "Call them" | 💭 "I'll change it to 'Call Jordan about invoice.'" | 😐 Typing | — |
| 4 | 👆 Save Edit | Updates title and clicks Save | 💭 "Done." | 😊 Fixed | ✅ Title updated in list instantly |
| 5 | 🖥 Todo List | Updated title visible | 💭 "Now to remove the obsolete one." | 😐 Scanning | — |
| 6 | 👆 Delete Todo | Clicks "Delete" on an outdated todo | 💭 "I'm sure I want to delete this." | 😐 Intentional | — |
| 7 | 🖥 Confirmation Prompt | "Are you sure? [Yes] [No]" appears | 💭 "Yes, delete it." | 😐 Confirming | — |
| 8 | 👆 Confirm Delete | Clicks "Yes" | 💭 "Gone." | 😊 Clean | ✅ Journey complete |

---

## 4. Journeys for PER-03: Casey — The Non-Technical Invitee

---

### JRN-PER03.01: Registration With an Error (and Recovery)

**Scenario:** Casey tries to register, mistyped the invite code, gets an error, fixes it, and successfully registers.

**JTBD Alignment:** JOB-PER03-01 (Register Without Getting Confused)  
**Entry Point:** Casey opens the app for the first time  
**Exit Point:** Casey successfully registers and sees their (empty) dashboard

---

| Step | Touchpoint | Action | Thought | Emotion | Pain Points / Notes |
|---|---|---|---|---|---|
| 1 | 🖥 Login View | Sees login form; looks for "register" | 💭 "I need to sign up, not log in." | 😐 Slightly uncertain | — |
| 2 | 👆 Click Register | Finds and clicks Register link | 💭 "OK, a form." | 😐 Cautious | — |
| 3 | 🖥 Registration Form | Reads labels carefully | 💭 "Username — OK. Password — twice? OK. Invite code — what's that?" | 😐 Uncertain about invite code | ⚡ Need clear helper text: "This code was sent to you by your admin" |
| 4 | 👆 Fill Form | Fills username and password; copies invite code from email (with a space at the end by accident) | 💭 "I think that's the code from the email." | 😐 Careful but imprecise | — |
| 5 | 👆 Submit | Clicks "Create Account" | 💭 "Let's hope this works." | 😐 Anxious | — |
| 6 | 🖥 Inline Error | Error below invite code field: "Invite code not found. Please check the code and try again." | 💭 "Oh no. But at least it says what to check." | 😟 Mildly frustrated | ✅ Inline error with plain language; does NOT say "undefined" or "error 422" |
| 7 | 🖥 Registration Form | Notices username and password fields are still filled | 💭 "Good — I don't have to re-type everything." | 😐 Recovering | ✅ Field retention reduces frustration |
| 8 | 👆 Fix Invite Code | Carefully re-copies the code, trims the trailing space | 💭 "Let me try again." | 😐 Focused | — |
| 9 | 👆 Submit Again | Clicks "Create Account" | 💭 "Please work this time." | 😐 Hopeful | — |
| 10 | 🖥 User Dashboard | Routed to empty user dashboard | 💭 "It worked! I'm in." | 😊 Relieved and pleased | ✅ Journey complete despite error |

**Journey Outcome:** Casey recovered from a form error without help and successfully registered.

**Key Moments of Truth:**
- Invite code helper text (Step 3)
- Plain-language error message (Step 6)
- Field values retained after error (Step 7)

---

### JRN-PER03.02: Daily Todo Use — Simple and Reliable

**Scenario:** Casey uses the app for a routine work morning — adds 2 tasks and checks off 1 from yesterday.

**JTBD Alignment:** JOB-PER03-02 (Trust That Tasks Will Be There Tomorrow)  
**Entry Point:** Casey opens the app the morning after adding tasks  
**Exit Point:** Casey sees their updated list and closes the tab

---

| Step | Touchpoint | Action | Thought | Emotion | Pain Points / Notes |
|---|---|---|---|---|---|
| 1 | 🖥 User Dashboard | Auto-login; yesterday's tasks visible | 💭 "Oh good, they're still there. I was worried." | 😊 Relieved | ✅ Persistence removes the anxiety Casey felt |
| 2 | 🖥 Todo List | Sees "Order office supplies" marked incomplete | 💭 "I did that yesterday!" | 😐 Remembering | — |
| 3 | 👆 Mark Complete | Clicks the checkbox | 💭 "There we go." | 😊 Satisfied click | ✅ Simple toggle, clear feedback |
| 4 | 👆 Add Todo | Types "Book conference room for Thursday" in add form | 💭 "Short title. Simple." | 😐 Typing | — |
| 5 | 👆 Submit | Clicks Add | 💭 "It appeared. Good." | 😊 | — |
| 6 | 👆 Add Another | Types "Send invoice to vendor" | 💭 "Two new tasks. That's all I have for today." | 😐 Done | — |
| 7 | 🖥 Todo List | Updated list with 1 completed, 2 new active items | 💭 "Looks right. Easy." | 😊 Content | ✅ Journey complete |

---

## 5. Journey Summary

| Journey ID | Persona | Scenario | JTBD | Steps | Outcome |
|---|---|---|---|---|---|
| JRN-PER01.01 | Alex | First-Time Setup & Onboarding | JOB-PER01-01 | 10 | 3 invite codes ready in < 5 min |
| JRN-PER01.02 | Alex | Check Team Progress Mid-Sprint | JOB-PER01-02 | 8 | 2 users reviewed in < 2 min |
| JRN-PER01.03 | Alex | Verify Registration Status | JOB-PER01-03 | 5 | Gap identified, code re-shared |
| JRN-PER02.01 | Jordan | First Registration + First Todo | JOB-PER02-01 | 9 | Registered and first task created in < 2 min |
| JRN-PER02.02 | Jordan | Daily Task Management | JOB-PER02-02 | 9 | List updated reflecting day's state |
| JRN-PER02.03 | Jordan | Edit and Delete Todos | JOB-PER02-02 | 8 | Todo edited + deleted cleanly |
| JRN-PER03.01 | Casey | Registration With Error Recovery | JOB-PER03-01 | 10 | Registered after correcting invite code |
| JRN-PER03.02 | Casey | Daily Routine Todo Use | JOB-PER03-02 | 7 | List managed reliably |

**Total Journeys:** 8 (3 for PER-01, 3 for PER-02, 2 for PER-03)  
All personas have at least 2 journeys. ✅

---

## 6. Cross-Journey Pain Point Summary

| Pain Point | Affected Personas | Journey(s) | Mitigation |
|---|---|---|---|
| No default credentials visible on first load | PER-01 | JRN-PER01.01 | First-run notice (F8) |
| Invite code field purpose unclear | PER-03 | JRN-PER03.01 | Helper text below field (FR-F2.1) |
| Fear of data loss on browser close | PER-03 | JRN-PER03.02 | Auto-login + visible session state (F1, F7) |
| Accidental data edit in admin view | PER-01 | JRN-PER01.02 | Read-only admin view + "Read Only" label (F6) |

---

*Document generated by Pivota Spec Journeys Generator | TodoApp v1.0 | 2026-04-15*

# Jobs-To-Be-Done (JTBD) Document
## Todo List App with User Management (TodoApp)

**Version:** 1.0  
**Date:** 2026-04-15  
**Status:** Validated  
**Sources:** PERSONAS-TodoApp.md, PRD-TodoApp.md  

---

## 1. Overview

This document defines outcome-driven job statements for each TodoApp persona. Jobs are written in the standard JTBD format: **"When [situation], I want to [motivation], so I can [expected outcome]."** Each job includes hiring criteria (what makes a solution "good enough" to hire for the job) and success measures.

Jobs are scoped to persona IDs from PERSONAS-TodoApp.md. Every persona has a minimum of 2 jobs.

---

## 2. Job Statements

---

### Jobs for PER-01: Alex — The Team Admin

---

#### JOB-PER01-01: Get the Team Onboarded Quickly

**Job Statement:**  
When I've just been given a new tool for my team, I want to set it up and get everyone a way to log in as fast as possible, so I can start managing tasks without losing time to onboarding friction.

**Context:**  
Alex opens the app for the first time. They need to create invite codes and distribute them to team members before anyone else can register.

**Functional Job:**  
Generate invite codes and share them with team members.

**Emotional Job:**  
Feel in control and competent as the team lead from day one.

**Social Job:**  
Be seen as someone who brings useful, frictionless tools to the team.

**Hiring Criteria (what makes the solution good enough to hire):**
- Default admin credentials are displayed clearly so Alex doesn't have to guess or read documentation
- Invite code generation requires one click (no configuration form)
- Generated code is immediately copyable to paste into a message or email
- Alex can generate multiple codes in one sitting without leaving the dashboard

**Success Measures:**
- Time from first app open to having a shareable invite code: under 2 minutes
- Zero support questions from team members about how to register
- All team members successfully registered within 24 hours of receiving codes

**Related Features:** F3 (Invite Code Management), F8 (Default Admin Bootstrapping), F1 (Auth)

---

#### JOB-PER01-02: Monitor Team Task Progress Without Interrupting People

**Job Statement:**  
When I want to check how the team is progressing on tasks, I want to view anyone's todo list without having to ask them for a status update, so I can make informed decisions about workload and priorities.

**Context:**  
Alex is mid-sprint and wants a quick view of what Jordan and Casey have on their plates before a stand-up meeting.

**Functional Job:**  
Navigate to any user's todo list and read it directly.

**Emotional Job:**  
Feel informed and confident without having to bother team members.

**Social Job:**  
Be seen as a proactive, prepared manager.

**Hiring Criteria:**
- User list is accessible from the main admin view without deep navigation
- Each user's todo list loads immediately when selected
- Admin view clearly shows all items regardless of completion status
- No edit controls are shown — Alex does not want to accidentally change someone's data

**Success Measures:**
- Time to navigate from admin dashboard to a user's todo list: under 3 clicks
- Admin can see all users and their full task lists in a single session
- No accidental data modification possible from admin view

**Related Features:** F4 (User Management), F6 (Admin Todo Visibility), F0 (App Shell)

---

#### JOB-PER01-03: Verify Who Has Joined the App

**Job Statement:**  
When I've sent out invite codes to my team, I want to see who has successfully registered, so I can follow up with anyone who hasn't joined yet.

**Context:**  
It's been 2 days since Alex sent invite codes. They want to know if all 5 team members have registered.

**Functional Job:**  
View a list of all registered users with registration dates.

**Hiring Criteria:**
- User list shows username and registration date
- Invite code list shows which codes have been used and by whom
- Clear visual distinction between used and unused codes

**Success Measures:**
- Alex can confirm registration status for all team members in under 1 minute
- Invite code status (used/unused) is visible without opening additional panels

**Related Features:** F4 (User Management), F3 (Invite Code Management)

---

### Jobs for PER-02: Jordan — The Everyday User

---

#### JOB-PER02-01: Get Registered and Start Tracking Tasks Immediately

**Job Statement:**  
When I receive an invite code from my team admin, I want to register and create my first task in under 2 minutes, so I can start using the tool right away without wasting time on setup.

**Context:**  
Jordan received an invite code via Slack. They open the app, see the registration form, and want to be up and running fast.

**Functional Job:**  
Register a new account using the invite code and add the first todo item.

**Emotional Job:**  
Feel like the tool respects their time and is genuinely simple to use.

**Hiring Criteria:**
- Registration form is clear: username, password, invite code — no unnecessary fields
- Error messages tell them exactly what's wrong without technical jargon
- After registration, they land immediately on their dashboard with an empty list
- The add-todo form is the first thing they see and is ready to use

**Success Measures:**
- Registration to first todo created: under 2 minutes
- Zero confusion about what the invite code field is for
- No page refreshes or unexpected redirects during the flow

**Related Features:** F2 (Invite Code Registration), F5 (Todo CRUD), F0 (App Shell)

---

#### JOB-PER02-02: Manage Daily Tasks Without Friction

**Job Statement:**  
When I'm in the middle of a workday, I want to quickly add new tasks, mark things as done, and delete what's no longer relevant, so I can keep my list accurate without breaking my focus.

**Context:**  
Jordan starts each morning by reviewing their list, marks a few things complete, adds new items from a morning meeting, and deletes a task that's been cancelled.

**Functional Job:**  
Perform CRUD operations on todos quickly and reliably.

**Emotional Job:**  
Feel organized, in control, and productive.

**Hiring Criteria:**
- Adding a task requires typing a title and hitting Enter or clicking one button
- Completing a task is a single click (checkbox or toggle)
- Deleting a task requires at most 2 clicks (click delete, confirm)
- The list updates immediately without page reload
- Changes are still there the next morning after closing the browser

**Success Measures:**
- Add a new task: under 5 seconds
- Mark a task complete: 1 click
- Delete a task: 2 clicks with confirmation
- Data survives browser close and reopen: 100% reliability

**Related Features:** F5 (Todo CRUD), F7 (Data Persistence)

---

#### JOB-PER02-03: Find What Still Needs to Be Done

**Job Statement:**  
When I have a mix of completed and active tasks in my list, I want to see only my remaining open tasks at a glance, so I can quickly know what still needs my attention.

**Context:**  
Jordan has 15 tasks in their list, 8 of which are complete. They want a filtered view of just the open ones.

**Functional Job:**  
Filter todo list to show only active (incomplete) items.

**Hiring Criteria:**
- Filter tabs or buttons are visible without scrolling
- "Active" filter immediately hides completed items
- Item count reflects the filtered view
- Switching between filters is instant

**Success Measures:**
- Time to switch to Active filter: 1 click
- Count accurately reflects number of active tasks at all times

**Related Features:** F5 (Todo CRUD — filter tabs)

---

### Jobs for PER-03: Casey — The Non-Technical Invitee

---

#### JOB-PER03-01: Register Without Getting Confused or Stuck

**Job Statement:**  
When I'm given an invite code and told to sign up, I want the registration process to be simple enough to complete on my own, so I don't have to ask anyone for help and can feel confident using the tool.

**Context:**  
Casey received an invite code in an email. They open the app, see the form, and aren't sure what "invite code" means or what format the username should be.

**Functional Job:**  
Complete registration form successfully using an invite code.

**Emotional Job:**  
Feel competent and not embarrassed by confusion.

**Hiring Criteria:**
- Each form field has a clear label and descriptive placeholder text
- If an error occurs (wrong code, username taken), the message says exactly what to do next
- The form does not clear successfully completed fields when an error is shown
- The "invite code" field has a label or helper text explaining what it is

**Success Measures:**
- Casey completes registration on first attempt (or second, after correcting a typo) without help
- Error messages are in plain language — no technical terms
- No fields are confusing or ambiguous

**Related Features:** F2 (Invite Code Registration), F0 (App Shell)

---

#### JOB-PER03-02: Trust That My Tasks Will Be There Tomorrow

**Job Statement:**  
When I add tasks to my list, I want to know they'll still be there when I come back the next day, so I can rely on this tool as a real part of my workflow.

**Context:**  
Casey adds 3 tasks at the end of the day, closes the laptop, and returns the next morning. They need to feel confident the data is there.

**Functional Job:**  
Verify data persists across browser sessions (implicit — perceived reliability).

**Emotional Job:**  
Feel safe trusting the tool with important reminders.

**Hiring Criteria:**
- Data is visibly still present on returning to the app (via auto-login + restored list)
- App never shows a blank list when data should exist
- Some visible indication (session info, username shown) reassures Casey they're "logged in"

**Success Measures:**
- 100% of added todos visible on return to app (same browser, same device)
- Auto-login resumes session so Casey doesn't have to re-enter credentials
- No blank screen or error shown on return visits

**Related Features:** F7 (Data Persistence), F1 (Session Management)

---

## 3. Job Summary Table

| Job ID | Persona | Job Title | Priority | Related Features |
|---|---|---|---|---|
| JOB-PER01-01 | PER-01 (Admin) | Get the Team Onboarded Quickly | High | F3, F8, F1 |
| JOB-PER01-02 | PER-01 (Admin) | Monitor Team Task Progress | High | F4, F6, F0 |
| JOB-PER01-03 | PER-01 (Admin) | Verify Who Has Joined | Medium | F4, F3 |
| JOB-PER02-01 | PER-02 (User) | Register and Start Tracking Immediately | High | F2, F5, F0 |
| JOB-PER02-02 | PER-02 (User) | Manage Daily Tasks Without Friction | High | F5, F7 |
| JOB-PER02-03 | PER-02 (User) | Find What Still Needs to Be Done | Medium | F5 |
| JOB-PER03-01 | PER-03 (Non-Tech) | Register Without Getting Confused | High | F2, F0 |
| JOB-PER03-02 | PER-03 (Non-Tech) | Trust That Tasks Will Be There Tomorrow | Medium | F7, F1 |

---

## 4. Outcome-Driven Innovation (ODI) Statements

For each high-priority job, the primary desired outcome:

| Job ID | Desired Outcome Statement |
|---|---|
| JOB-PER01-01 | Minimize the time and effort required to get all team members registered and using the app |
| JOB-PER01-02 | Minimize the effort required to find and read any team member's current task list |
| JOB-PER02-01 | Minimize the time between receiving an invite code and tracking the first task |
| JOB-PER02-02 | Minimize the number of steps required to add, complete, and remove todo items |
| JOB-PER03-01 | Minimize the likelihood of registration failure due to unclear instructions or unhelpful errors |

---

*Document generated by Pivota Spec JTBD Generator | TodoApp v1.0 | 2026-04-15*

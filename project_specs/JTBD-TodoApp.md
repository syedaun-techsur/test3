# Jobs-to-be-Done Document
## Basic To-Do App (TodoApp)

| Field | Value |
|---|---|
| **Product Name** | TodoApp — Basic To-Do App |
| **Version** | 1.0 |
| **Date** | 2026-05-07 |
| **Status** | Draft |
| **Related Personas** | PERSONAS-TodoApp.md (PER-01 Marcus Webb, PER-02 Priya Nolan) |
| **Related PRD** | PRD-TodoApp.md |

---

## JTBD Summary Table

| JTBD ID | Persona | Job Statement (short form) | Priority |
|---|---|---|---|
| JTBD-01.1 | PER-01 Marcus Webb | Capture a task the instant it occurs to me, without interrupting my flow | P0 |
| JTBD-01.2 | PER-01 Marcus Webb | Know at a glance what I still need to do in the current session | P0 |
| JTBD-01.3 | PER-01 Marcus Webb | Clear completed and cancelled tasks so the list reflects reality | P0 |
| JTBD-01.4 | PER-01 Marcus Webb | Trust that my tasks are waiting for me after any interruption | P0 |
| JTBD-02.1 | PER-02 Priya Nolan | Capture meeting action items without breaking the flow of the meeting | P0 |
| JTBD-02.2 | PER-02 Priya Nolan | Give my team a single, always-current view of what needs doing | P0 |
| JTBD-02.3 | PER-02 Priya Nolan | Signal progress to the team by marking work as done | P0 |
| JTBD-02.4 | PER-02 Priya Nolan | Rely on the task list to be intact the next time any team member opens it | P0 |

---

## PER-01: Marcus Webb — Everyday Task Tracker

---

### JTBD-01.1: Frictionless Task Capture

**Job Statement:**
When a task or commitment surfaces while I am mid-work, I want to record it immediately without leaving my current context, so I can stay focused and trust nothing is forgotten.

**Current Alternatives:**
- Types tasks into a plain text file kept open on a second monitor
- Switches to a notes app, which disrupts browser tab arrangement
- Abandons the thought and risks forgetting the task entirely

**Hiring Criteria:**
- Task input is available the moment the page loads — no login, modal, or onboarding screen
- Typing and pressing Enter is the entire submission flow (≤ 2 interactions)
- New task appears in the list immediately with no page reload required

**Success Measure:** Marcus records a new task within 5 seconds of opening the app, with zero prior configuration and zero account steps.

**Related Features:** F0, F1
**Priority:** P0

---

### JTBD-01.2: Rapid Workday Prioritisation

**Job Statement:**
When I sit down after a break or switch between client projects, I want to scan all my open tasks in a single view, so I can immediately decide what to work on next without hunting through files or tabs.

**Current Alternatives:**
- Scrolls through a plain text file, manually scanning for unchecked items
- Keeps a mental model of remaining tasks and risks missing items
- Checks multiple browser tabs or documents to piece together the full picture

**Hiring Criteria:**
- All tasks are visible in one uncluttered list on the default view — no filtering, tab switching, or scrolling through menus required
- Completed tasks are visually distinct so incomplete items stand out at a glance
- List loads within 1 second so the scan is instant

**Success Measure:** Marcus identifies which tasks remain open within 15 seconds of opening the app, with no interaction beyond page load.

**Related Features:** F1, F2
**Priority:** P0

---

### JTBD-01.3: List Hygiene Without Friction

**Job Statement:**
When tasks are done or no longer relevant, I want to remove them from the list with a single action, so I can keep the list short, accurate, and free of visual noise.

**Current Alternatives:**
- Manually deletes lines from a text file, sometimes leaving stale items for days
- Uses strikethrough formatting as a workaround, causing the list to grow cluttered over time
- Periodically rewrites the entire text file to start fresh

**Hiring Criteria:**
- A delete action is available per task without navigating to a separate screen
- Task disappears from the list immediately upon deletion — no page reload needed
- Completion toggle lets Marcus mark done without deleting, keeping the decision separate

**Success Measure:** Marcus removes or completes a task in ≤ 2 interactions; visible list reflects current state within 1 second of the action.

**Related Features:** F2, F3
**Priority:** P0

---

### JTBD-01.4: Reliable Continuity Across Interruptions

**Job Statement:**
When I close the browser, clear my session, or step away from the computer, I want my task list to be fully restored when I return, so I can pick up exactly where I left off without reconstructing my context from memory.

**Current Alternatives:**
- Relies on a local text file that survives tab closes but is lost if the file is overwritten
- Has learned to avoid clearing browser cache because localStorage-based apps lose all data
- Accepts occasional data loss as the cost of using lightweight tools

**Hiring Criteria:**
- Tasks are stored server-side, not in browser localStorage or session storage alone
- Full task list — including completion status — is restored automatically on page load with no manual action
- Data survives a hard browser refresh (Ctrl+Shift+R / Cmd+Shift+R) with zero loss

**Success Measure:** 100% of tasks and their completion states are present after a hard browser refresh; zero data loss in any normal-use scenario.

**Related Features:** F4, F1
**Priority:** P0

---

## PER-02: Priya Nolan — Lightweight Project Coordinator

---

### JTBD-02.1: In-Meeting Action Item Capture

**Job Statement:**
When action items are called out during a team meeting, I want to log each one immediately into a shared list, so I can keep pace with the meeting without asking people to pause or repeat themselves.

**Current Alternatives:**
- Types items into a Google Doc meeting notes section, then manually copies them to a separate tracker later
- Jots items on paper and transcribes them after the meeting, introducing delay and transcription errors
- Tries to add items to Asana, but account prompts on other team members' devices block the flow

**Hiring Criteria:**
- App opens immediately from a URL with no login, invitation, or account step for any team member's device
- Task entry requires only typing and pressing Enter — no category, assignee, or due date fields to fill
- New task appears in the shared list within 1 second so the next action item can be entered right away

**Success Measure:** Priya enters 5 consecutive action items during a live meeting in under 90 seconds, with the list remaining consistent on any team member's browser after a page reload.

**Related Features:** F0, F1, F4
**Priority:** P0

---

### JTBD-02.2: Shared Team Visibility Without Accounts

**Job Statement:**
When team members check in throughout the day, I want everyone to see the same current task list by opening a single URL, so I can eliminate the need to maintain separate per-person lists or send status update messages.

**Current Alternatives:**
- Copies the task list into the team's group chat whenever it changes, creating multiple out-of-sync versions
- Shares a Google Doc that everyone can edit, but it lacks any task-completion signalling
- Uses a shared spreadsheet that quickly becomes cluttered and hard to read at a glance

**Hiring Criteria:**
- Any team member can open the app URL in any modern browser and immediately see the current task list — no signup, no installation
- The list reflects the latest backend state on every page load, so all team members see the same data regardless of device
- No per-user isolation: all sessions share the same task state

**Success Measure:** A task added by Priya on her laptop is visible to a team member loading the same URL on a separate device within the same session, with no manual sync step required.

**Related Features:** F1, F4
**Priority:** P0

---

### JTBD-02.3: Progress Signalling to the Team

**Job Statement:**
When a task is completed, I want to mark it done in a way the whole team can see, so I can communicate progress without sending a separate message or updating a secondary document.

**Current Alternatives:**
- Sends a "done" message in the group chat referencing the task by name — easily lost in conversation history
- Deletes completed tasks from the Google Doc, losing the record of what was accomplished
- Leaves tasks unchecked and relies on verbal updates during the next meeting

**Hiring Criteria:**
- A completion toggle is available per task and requires only one interaction
- Completed tasks are visually differentiated (e.g., strikethrough or muted style) so done versus open is instantly readable
- Completion state is persisted to the backend immediately so any team member reloading the page sees the updated status

**Success Measure:** After Priya marks a task complete, any team member reloading the page within 5 seconds sees the updated completion status — no additional sync action required.

**Related Features:** F2, F4
**Priority:** P0

---

### JTBD-02.4: Durable Cross-Session Reliability

**Job Statement:**
When the team starts a new day or checks in after hours away from the app, I want the full task list to be intact and accurate, so I can trust the app as the team's single source of truth without manually verifying or re-entering tasks.

**Current Alternatives:**
- Screenshot the task list at end-of-day as a backup, then re-enters any lost items the following morning
- Treats any lightweight browser-only tool as temporary and keeps a Google Doc as the "real" record
- Avoids lightweight tools entirely after a previous data-loss incident with a localStorage-based app

**Hiring Criteria:**
- All task data persists using durable server-side storage (not in-memory only); survives a server restart
- Task titles and completion states are fully restored on every page load without any user action
- No data loss during normal CRUD operations across multiple sessions

**Success Measure:** Zero tasks are missing or in an incorrect completion state after a server restart or 12-hour gap between sessions; the list matches exactly what was entered the previous day.

**Related Features:** F4, F1, F2
**Priority:** P0

---

## Outcome-to-Feature Traceability

| JTBD ID | Related Feature(s) | Expected Outcome |
|---|---|---|
| JTBD-01.1 | F0, F1 | New task captured in ≤ 5 seconds with no account or config steps; appears in list immediately |
| JTBD-01.2 | F1, F2 | Full open task list visible in ≤ 1 second; completed items visually distinct from active items |
| JTBD-01.3 | F2, F3 | Task marked complete or deleted in ≤ 2 interactions; list updates within 1 second |
| JTBD-01.4 | F4, F1 | 100% of tasks and completion states restored after hard refresh; zero data loss |
| JTBD-02.1 | F0, F1, F4 | 5 action items logged in < 90 seconds; list consistent on any browser after reload |
| JTBD-02.2 | F1, F4 | Same task list visible to all team members from any device via shared URL; no login required |
| JTBD-02.3 | F2, F4 | Completion state visible to any team member within 5 seconds of toggling; persisted to backend |
| JTBD-02.4 | F4, F1, F2 | Zero data loss after server restart; full list and completion states restored the following day |

**Feature Coverage Check:**

| Feature ID | Covered By |
|---|---|
| F0 | JTBD-01.1, JTBD-02.1 |
| F1 | JTBD-01.1, JTBD-01.2, JTBD-01.4, JTBD-02.1, JTBD-02.2, JTBD-02.4 |
| F2 | JTBD-01.2, JTBD-01.3, JTBD-02.3, JTBD-02.4 |
| F3 | JTBD-01.3 |
| F4 | JTBD-01.4, JTBD-02.1, JTBD-02.2, JTBD-02.3, JTBD-02.4 |

---

## NaC Preview

> Candidate Natural Acceptance Criteria derived from JTBD success measures. These will be refined in STORY-MAP.

| JTBD ID | Outcome | Candidate Natural Acceptance Criterion |
|---|---|---|
| JTBD-01.1 | First task captured in ≤ 5 seconds with zero setup | Given a user opens the app for the first time, when they type a task and press Enter, then the task appears in the list within 1 second — with no prior login, account creation, or configuration required |
| JTBD-01.2 | All open tasks visible within 15 seconds of page load | Given tasks exist in the backend, when the page loads, then all tasks with their completion status are displayed in under 1 second and no filtering or navigation is required to see the full list |
| JTBD-01.3 | Task removed or completed in ≤ 2 interactions | Given a task exists in the list, when the user clicks the delete action, then the task is removed from the list and the backend within 1 second; when the user toggles completion, the visual state updates immediately |
| JTBD-01.4 | 100% task state restored after hard refresh | Given tasks were added and some marked complete, when the user performs a hard browser refresh, then all tasks and their completion states are present — identical to the state before the refresh |
| JTBD-02.1 | 5 action items logged in < 90 seconds during a meeting | Given a user enters 5 tasks in rapid succession, when each Enter is pressed, then each task appears in the list within 1 second and remains visible after a page reload on a different device |
| JTBD-02.2 | Same list visible to all team members with no login | Given two browsers load the same app URL independently, when one adds a task and the other refreshes, then both see an identical task list with no authentication step for either session |
| JTBD-02.3 | Completion state visible to team within 5 seconds of toggle | Given a task exists and is marked complete in one session, when a second browser reloads the page within 5 seconds, then the task is displayed in the completed state in that second session |
| JTBD-02.4 | Zero data loss after server restart or 12-hour gap | Given tasks exist in the backend storage, when the server is restarted and a user loads the page, then every task — including titles and completion states — is fully restored with no manual re-entry required |

---

*Document generated: 2026-05-07*
*Related documents: PERSONAS-TodoApp.md, PRD-TodoApp.md, FRD-TodoApp.md*

# Personas Document
## Basic To-Do App (TodoApp)

| Field | Value |
|---|---|
| **Product Name** | TodoApp — Basic To-Do App |
| **Version** | 1.0 |
| **Date** | 2026-05-07 |
| **Status** | Draft |
| **Related PRD** | PRD-TodoApp.md |
| **Derived From** | PRD Section 2 (Problem Statement), Section 5 (Features), Section 7 (Success Metrics) |

> **Note:** PRD-TodoApp.md does not include a formal Section 2.2 Target Users table. Personas are derived from the Problem Statement pain points, product vision, and feature capabilities, which together imply two distinct user archetypes.

---

## Persona Summary

| Persona ID | Name | Role | Primary Goal |
|---|---|---|---|
| PER-01 | Marcus Webb | Everyday Task Tracker | Capture and clear daily tasks instantly, with zero setup friction |
| PER-02 | Priya Nolan | Lightweight Project Coordinator | Maintain a shared, reliable task list for small team use without any tooling overhead |

---

## PER-01: Marcus Webb

**Everyday Task Tracker**

**Role & Context:**
Marcus is a freelance graphic designer who juggles multiple client deliverables, personal errands, and ad-hoc commitments throughout the day. He works from a home office across two browser tabs — one for client work, one for reference — and keeps a plain text file open on his second monitor as a running task list. He has tried several productivity apps (Todoist, Notion, TickTick) but abandoned each one after the onboarding and workspace setup got in the way of simply writing things down. What Marcus wants is something he can open in a browser tab, type a task, and move on. He adds 5–15 tasks per day, completes most within the same session, and rarely needs to revisit tasks across multiple days.

**Goals:**
- Add a task immediately without creating an account or configuring a workspace (F0, addresses PRD pain point: "multi-step onboarding before first task")
- See all his open tasks at a glance in a single, clean list (F1)
- Mark tasks done as he finishes them so the list reflects actual progress (F2)
- Remove completed or cancelled tasks to keep the list uncluttered (F3)
- Find his tasks still there when he returns after closing the tab (F4)

**Pain Points:**
- Every productivity tool he has tried demanded sign-up, email verification, or workspace naming before he could type his first task
- Browser-based lists built on localStorage vanished when he cleared his cache or switched devices
- Feature-heavy UIs (labels, priorities, due dates, integrations) add visual noise he has no use for
- Switching between tools mid-day breaks his flow; he wants a single persistent tab he never has to think about

**Technical Expertise:** Intermediate — fluent with web apps and browser tools, not a developer; avoids anything requiring configuration or setup

**Top Tasks:**
1. Add a new task by typing and pressing Enter (multiple times per day, critical)
2. Scan the full task list to decide what to work on next (several times per day, high)
3. Check off a task as complete when finished (several times per day, high)
4. Delete stale or irrelevant tasks to keep the list tidy (daily, medium)
5. Reload the page and confirm tasks are still there after an interruption (occasionally, medium)

**Success Criteria:**
- Can add his first task within 10 seconds of opening the app for the first time (no login, no form, no onboarding — per PRD Success Metrics)
- All four CRUD actions are reachable in ≤ 2 clicks or interactions (per PRD Non-Functional: Usability)
- Task list is fully restored after a hard browser refresh with no data loss (per PRD F4, Success Metrics: Persistence verified)
- Zero configuration required to start using the app

---

## PER-02: Priya Nolan

**Lightweight Project Coordinator**

**Role & Context:**
Priya is a program coordinator at a small non-profit, responsible for tracking action items across weekly team meetings and volunteer initiatives. Her team of four operates with minimal tooling — they share a Google Doc for meeting notes and communicate via a group chat app. Priya has been looking for a simple shared task list that anyone on the team can open from a link and immediately see what needs doing, without requiring each person to create an account or install anything. She is comfortable with spreadsheets and web tools but has no engineering background. She opens the app 2–3 times per day: once in the morning to review pending items, once mid-day to add items captured during a meeting, and once at end-of-day to tidy up completed tasks. She values reliability above all — if a task is entered, it must still be there tomorrow.

**Goals:**
- Maintain a single shared task list her team can access from any browser without accounts (F1, F4; addresses PRD pain point: "no account management")
- Quickly add action items captured during a meeting without interrupting the meeting flow (F0)
- Visually distinguish what is done from what is still open at a glance (F2)
- Remove resolved items to keep the list focused on active work (F3)
- Trust that tasks entered today will be present when the team checks in tomorrow (F4)

**Pain Points:**
- Shared task tools like Asana or Monday.com require every team member to create an account and join a workspace before seeing a single task
- Lightweight browser-only lists (sticky note apps, localStorage tools) lose data without warning
- Tools with complex UIs (boards, sprints, labels) create confusion among non-technical volunteers
- When someone adds a task on one device and another person checks on a different device, the lists don't match (no backend persistence)

**Technical Expertise:** Basic-to-intermediate — confident with web apps and forms; avoids anything requiring installation, terminal use, or developer setup

**Top Tasks:**
1. Review all open tasks at the start of the day to prioritize team work (daily, critical)
2. Add 3–8 new action items after a team meeting (2–3 times per week, high)
3. Mark completed action items as done to signal progress to the team (daily, high)
4. Delete resolved or cancelled tasks to declutter the active list (several times per week, medium)
5. Verify the list is current and consistent after a page reload or device switch (as needed, medium)

**Success Criteria:**
- Any team member can open the app URL and immediately see the current shared task list — no login, no invitation required (per PRD: Zero auth friction)
- Tasks added on one session are visible to any other session loading the page (per PRD F4: server-side persistence)
- Task list loads within 1 second so meetings are not interrupted while adding items (per PRD Non-Functional: Performance)
- The task list survives a server restart with no data loss (per PRD Non-Functional: Reliability)

---

## Persona Relationships

| Interaction | PER-01 (Marcus) | PER-02 (Priya) |
|---|---|---|
| **Usage pattern** | Solo, single-session focus | Shared state; multiple people load same list |
| **Session frequency** | Many short bursts throughout the day | 2–3 structured check-ins per day |
| **Tolerance for complexity** | Very low — wants zero configuration | Low — accepts minimal setup if zero per-user accounts |
| **Persistence need** | Medium — same browser, same device | High — must survive across devices and sessions |
| **Overlap** | Both need instant task creation and clean list view | — |
| **Divergence** | Single-user personal list | Implicitly shared/team list context |

> Both personas are served by the same single-user, no-auth application. PER-02's shared-list usage is an emergent behavior made possible by the absence of user isolation — multiple people can load the same URL and see the same backend state. This is acknowledged in the PRD Non-Functional Requirements ("single-user or shared state is acceptable").

---

## Feature-Persona Matrix

| Feature ID | Feature Name | PER-01 Marcus | PER-02 Priya | Notes |
|---|---|---|---|---|
| **F0** | Task Creation | **Primary** | **Primary** | Core daily action for both personas |
| **F1** | Task List View | **Primary** | **Primary** | Entry point for both; Priya depends on consistent shared view |
| **F2** | Task Completion | **Primary** | **Primary** | Marcus tracks personal progress; Priya signals team status |
| **F3** | Task Deletion | **Primary** | **Primary** | Both use deletion to maintain a clean, current list |
| **F4** | Task Persistence | **Primary** | **Primary** | Higher stakes for Priya (cross-device/session); still critical for Marcus |

**Legend:**
- **Primary** — Persona directly depends on this feature; it addresses their stated goals or pain points
- **Secondary** — Persona benefits from this feature but it is not central to their core workflow
- **None** — Feature has no relevance to this persona

> All five features are Primary for both personas. This reflects the MVP nature of the product — every feature in the PRD is a P0 Core CRUD or Infrastructure capability with no optional or persona-specific features in v1.

---

*Document generated: 2026-05-07*
*Related documents: PRD-TodoApp.md, FRD-TodoApp.md, UserStories-TodoApp.md*

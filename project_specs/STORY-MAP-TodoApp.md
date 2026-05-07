# Story Map
## Basic To-Do App (TodoApp)

| Field | Value |
|---|---|
| **Product Name** | TodoApp — Basic To-Do App |
| **Version** | 1.0 |
| **Date** | 2026-05-07 |
| **Status** | Draft |
| **Related PRD** | PRD-TodoApp.md |
| **Related Personas** | PERSONAS-TodoApp.md (PER-01 Marcus Webb, PER-02 Priya Nolan) |
| **Related JTBD** | JTBD-TodoApp.md |
| **Related Journeys** | JOURNEYS-TodoApp.md |
| **Related UserStories** | UserStories-TodoApp.md |

---

## Overview

This Story Map organizes all 21 user stories from UserStories-TodoApp.md onto a two-dimensional grid where:

- **X-axis (columns)** — Journey stages drawn from JOURNEYS-TodoApp.md, grouped by persona journey
- **Y-axis (rows)** — Epics (F0–F4) and their constituent stories
- **NaC column** — Natural Acceptance Criteria derived from the intersection of a JTBD outcome, the journey stage, and the story being built
- **Release column** — Increment assignment based on priority and journey completeness

**NaC Concept:** NaC bridges a JTBD functional outcome to a testable criterion scoped to the moment in the journey where the story is exercised. Every NaC in this document is traceable to a specific JTBD ID and journey stage — none are invented.

**Release Strategy:** All 21 stories are P0 (MVP). The single release (R1) delivers every feature required to complete both primary journeys end-to-end. Post-MVP increments are noted for backlog items identified in the gap analysis.

---

## Story Map Matrix

### Journeys Covered

| Journey ID | Persona | Scenario | Stages |
|---|---|---|---|
| JRN-01.1 | PER-01 Marcus Webb | First-time task capture mid-work session | Arrive → Orient → Capture → Confirm → Return |
| JRN-01.2 | PER-01 Marcus Webb | End-of-day list cleanup after a busy session | Review → Complete → Delete → Verify → Close & Return |
| JRN-02.1 | PER-02 Priya Nolan | In-meeting action item capture for the team | Pre-Meeting Setup → First Capture → Rapid Re-Entry → Mid-Meeting Scan → Post-Meeting Share → Cross-Device Verification |
| JRN-02.2 | PER-02 Priya Nolan | Morning team check-in and progress signalling | Morning Load → Review → Mark Complete → Delete Cancelled Task → Verify & Close |

---

### Epic 0: Task Creation (F0)

| SM ID | Persona | Journey | Stage | Story | NaC | Release |
|---|---|---|---|---|---|---|
| SM-0.1 | PER-01 Marcus | JRN-01.1 | Capture | US-0.1: Add a Task by Pressing Enter | JTBD-01.1 → JRN-01.1:Capture — Task submitted via type+Enter in ≤ 2 interactions; appears in list within 1 second | R1 |
| SM-0.2 | PER-02 Priya | JRN-02.1 | First Capture | US-0.2: Add a Task by Clicking the Add Button | JTBD-02.1 → JRN-02.1:First Capture — Add button visible adjacent to input; task appears within 1 second of click; reachable in ≤ 2 interactions | R1 |
| SM-0.3 | PER-01 Marcus | JRN-01.1 | Capture | US-0.3: Prevent Empty Task Submission | JTBD-01.1 → JRN-01.1:Capture — Submitting a blank input does not create a task; inline error appears; input stays focused so the next entry is immediate | R1 |
| SM-0.4 | PER-02 Priya | JRN-02.1 | Rapid Re-Entry | US-0.4: Enforce Task Title Character Limit | JTBD-02.1 → JRN-02.1:Rapid Re-Entry — Titles over 500 chars are rejected with inline feedback; typed text remains so the user can trim without re-typing | R1 |
| SM-0.5 | PER-01 Marcus | JRN-01.1 | Capture | US-0.5: Handle Task Creation Backend Failure | JTBD-01.1 → JRN-01.1:Capture — If save fails, error message shown and input text preserved; task not added to list; user can retry without re-typing | R1 |

---

### Epic 1: Task List View (F1)

| SM ID | Persona | Journey | Stage | Story | NaC | Release |
|---|---|---|---|---|---|---|
| SM-1.1 | PER-01 Marcus | JRN-01.1 | Arrive / Orient | US-1.1: View All Tasks on Page Load | JTBD-01.2 → JRN-01.1:Arrive — All persisted tasks render within 1 second of page load with no interaction required; input field is immediately available | R1 |
| SM-1.2 | PER-01 Marcus | JRN-01.1 | Orient | US-1.2: See Empty State When No Tasks Exist | JTBD-01.2 → JRN-01.1:Orient — When no tasks exist, a placeholder message confirms the app is working and guides the user to add their first task | R1 |
| SM-1.3 | PER-02 Priya | JRN-02.2 | Review | US-1.3: View Completed and Active Tasks Together | JTBD-02.2 → JRN-02.2:Review — Both completed and open tasks appear in one unified list; visual differentiation lets the team assess status at a glance with no filtering | R1 |
| SM-1.4 | PER-02 Priya | JRN-02.1 | Rapid Re-Entry | US-1.4: List Reflects Changes Without Page Reload | JTBD-02.1 → JRN-02.1:Rapid Re-Entry — Each newly created task appears in the list within 1 second of Enter; no manual reload required between entries during a meeting | R1 |
| SM-1.5 | PER-02 Priya | JRN-02.1 | Pre-Meeting Setup | US-1.5: Handle Task List Load Failure | JTBD-02.1 → JRN-02.1:Pre-Meeting Setup — If the list fails to load, a clear error (not an empty list) is shown so the user knows to refresh rather than assume there are no tasks | R1 |

---

### Epic 2: Task Completion (F2)

| SM ID | Persona | Journey | Stage | Story | NaC | Release |
|---|---|---|---|---|---|---|
| SM-2.1 | PER-01 Marcus | JRN-01.2 | Complete | US-2.1: Mark a Task as Complete | JTBD-01.3 → JRN-01.2:Complete — Clicking the completion toggle changes visual state immediately (≤ 1 interaction); PATCH persisted to backend; task remains visible | R1 |
| SM-2.2 | PER-01 Marcus | JRN-01.2 | Complete | US-2.2: Unmark a Completed Task | JTBD-01.3 → JRN-01.2:Complete — Clicking a completed task's toggle immediately reverts it to active; round-trip toggle returns task to original state; no reload required | R1 |
| SM-2.3 | PER-02 Priya | JRN-02.2 | Review / Mark Complete | US-2.3: See Visual Distinction Between Active and Completed Tasks | JTBD-02.3 → JRN-02.2:Review — Completed tasks show strikethrough/muted style; active tasks are prominent; distinction applied immediately on toggle across all tasks simultaneously | R1 |
| SM-2.4 | PER-01 Marcus | JRN-01.2 | Complete | US-2.4: Handle Task Completion Backend Failure | JTBD-01.3 → JRN-01.2:Complete — If PATCH fails, visual state reverts to pre-toggle; error message shown; list accurately reflects what is actually saved, not a false state | R1 |

---

### Epic 3: Task Deletion (F3)

| SM ID | Persona | Journey | Stage | Story | NaC | Release |
|---|---|---|---|---|---|---|
| SM-3.1 | PER-01 Marcus | JRN-01.2 | Delete | US-3.1: Delete a Task | JTBD-01.3 → JRN-01.2:Delete — Single click removes task from list immediately; DELETE sent to backend; task gone within 1 second; no confirmation dialog adds friction | R1 |
| SM-3.2 | PER-02 Priya | JRN-02.2 | Delete Cancelled Task | US-3.2: See Empty State After Deleting Last Task | JTBD-01.3 → JRN-01.2:Verify — After the last task is deleted, empty-state placeholder appears immediately; confirms deletion succeeded rather than suggesting a display error | R1 |
| SM-3.3 | PER-01 Marcus | JRN-01.2 | Delete | US-3.3: Handle Task Deletion Backend Failure | JTBD-01.3 → JRN-01.2:Delete — If DELETE fails, task is restored to the list and error message shown; user does not lose a task that was not actually deleted | R1 |

---

### Epic 4: Task Persistence (F4)

| SM ID | Persona | Journey | Stage | Story | NaC | Release |
|---|---|---|---|---|---|---|
| SM-4.1 | PER-01 Marcus | JRN-01.2 | Close & Return | US-4.1: Tasks Survive a Browser Refresh | JTBD-01.4 → JRN-01.2:Close & Return — After hard browser refresh, all tasks and their completion states are present and correct; fetched from backend, not reconstructed from localStorage | R1 |
| SM-4.2 | PER-02 Priya | JRN-02.2 | Morning Load | US-4.2: Tasks Survive a Server Restart | JTBD-02.4 → JRN-02.2:Morning Load — After server restart, GET /api/tasks returns every previously created task with correct titles and completion states; durable storage confirmed | R1 |
| SM-4.3 | PER-01 Marcus | JRN-01.1 | Arrive | US-4.3: Backend Auto-Initializes Storage on First Run | JTBD-01.1 → JRN-01.1:Arrive — On first startup, backend creates DB/schema automatically; app serves a valid empty task list immediately; user can add first task within 10 seconds of open | R1 |
| SM-4.4 | PER-02 Priya | JRN-02.1 | Cross-Device Verification | US-4.4: All Task Writes Are Committed Before Success Response | JTBD-02.2 → JRN-02.1:Cross-Device Verification — Backend only returns success after write is confirmed durable; a task confirmed in one session is visible to any session loading the URL within seconds | R1 |

---

## NaC Derivation Table

Full traceability chain: JTBD Outcome → Journey Stage → Natural Acceptance Criterion → User Story

| JTBD ID | Outcome (from JTBD doc) | Journey Stage | Natural Acceptance Criterion | Story |
|---|---|---|---|---|
| JTBD-01.1 | First task captured in ≤ 5 seconds with zero setup | JRN-01.1:Arrive | App opens with no login, modal, or onboarding; input field is immediately focused and ready | US-4.3 |
| JTBD-01.1 | Task submitted via Enter in ≤ 2 interactions; appears in list within 1 second | JRN-01.1:Capture | Pressing Enter on non-empty input submits the task; it appears in the list within 1 second; input clears and refocuses | US-0.1 |
| JTBD-01.1 | No task created for blank or invalid input; user can correct immediately | JRN-01.1:Capture | Blank or whitespace-only submission is rejected with inline error; input stays focused; no API call made | US-0.3 |
| JTBD-01.1 | Task not lost on backend failure; user retains typed text | JRN-01.1:Capture | On save failure, error shown inline; task not added to list; typed text preserved for retry | US-0.5 |
| JTBD-01.2 | All open tasks visible within 15 seconds of page load; completed items visually distinct | JRN-01.1:Arrive/Orient | All persisted tasks rendered within 1 second; each item shows title and completion state; no filtering required | US-1.1 |
| JTBD-01.2 | App confirms it is working even when the list is empty | JRN-01.1:Orient | Empty-state placeholder shown when task array is empty; disappears as soon as a task is added | US-1.2 |
| JTBD-01.3 | Task marked complete or deleted in ≤ 2 interactions; list reflects change within 1 second | JRN-01.2:Complete | Completion toggle changes visual state immediately in ≤ 1 click; PATCH sent to backend; task stays visible | US-2.1 |
| JTBD-01.3 | Task toggled back to active in ≤ 1 interaction; round-trip is idempotent | JRN-01.2:Complete | Clicking a completed task's toggle reverts visual state immediately; PATCH with completed:false sent | US-2.2 |
| JTBD-01.3 | Task removed in 1 interaction; disappears from list within 1 second | JRN-01.2:Delete | Single click on delete removes task immediately; DELETE sent to backend; no confirmation dialog | US-3.1 |
| JTBD-01.3 | List reflects current state after all deletions; empty-state shows when appropriate | JRN-01.2:Verify | After last task deleted, empty-state placeholder appears immediately without page reload | US-3.2 |
| JTBD-01.3 | Task restored if deletion fails; user does not silently lose a task | JRN-01.2:Delete | On DELETE failure, task is restored to list and error message shown; user can retry | US-3.3 |
| JTBD-01.3 | Visual state reverts if completion update fails; list reflects true backend state | JRN-01.2:Complete | On PATCH failure, visual state reverts before or simultaneously with error message; no false completed state | US-2.4 |
| JTBD-01.4 | 100% task state restored after hard browser refresh; zero data loss | JRN-01.2:Close & Return | After hard refresh, all tasks and completion states present; data from backend API, not localStorage | US-4.1 |
| JTBD-02.1 | App loads in < 1 second; input immediately ready; no login or setup | JRN-02.1:Pre-Meeting Setup | Page load with full task restoration in < 1 second; no auth step; input ready for immediate use | US-1.5 |
| JTBD-02.1 | Each task appears within 1 second; input clears and refocuses automatically | JRN-02.1:First Capture | Add button submits task within 1 second; input clears and refocuses; reachable in ≤ 2 interactions | US-0.2 |
| JTBD-02.1 | 5 consecutive tasks captured in < 90 seconds; each appears immediately | JRN-02.1:Rapid Re-Entry | List updates live after each submission with no reload; title > 500 chars caught with inline error; text preserved | US-0.4, US-1.4 |
| JTBD-02.2 | Same task list visible to all team members from any device via shared URL | JRN-02.1:Cross-Device Verification | Backend returns success only after durable write; any session loading the URL sees the same state | US-4.4 |
| JTBD-02.2 | Single consistent list view; completed tasks visually distinct from open tasks | JRN-02.2:Review | Completed and active tasks in one unified list; visual differentiation applied; no tab-switching required | US-1.3 |
| JTBD-02.3 | Completion toggle requires 1 interaction; state persisted to backend immediately | JRN-02.2:Mark Complete | Single-click toggle; immediate visual change; completion state synced so any reload shows updated status | US-2.3 |
| JTBD-02.4 | Zero data loss after server restart; full list and completion states restored | JRN-02.2:Morning Load | After server restart, GET /api/tasks returns every task with correct title and completion state | US-4.2 |

---

## Release Planning

### R1: Complete MVP — "It Just Works"

**Theme:** Deliver every capability required for both primary journeys end-to-end, so Marcus can capture personal tasks immediately and Priya can coordinate team action items reliably.

**Justification:** All 21 stories are P0. Every story addresses at least one journey stage. Releasing them together is the only configuration that delivers a complete journey for either persona — no journey can be left half-complete without the app being non-functional.

| Story | Title | Persona(s) | JTBD Addressed | Journey Stage(s) |
|---|---|---|---|---|
| US-0.1 | Add a Task by Pressing Enter | PER-01 Marcus | JTBD-01.1 | JRN-01.1:Capture |
| US-0.2 | Add a Task by Clicking the Add Button | PER-02 Priya | JTBD-02.1 | JRN-02.1:First Capture |
| US-0.3 | Prevent Empty Task Submission | PER-01 Marcus | JTBD-01.1 | JRN-01.1:Capture |
| US-0.4 | Enforce Task Title Character Limit | PER-02 Priya | JTBD-02.1 | JRN-02.1:Rapid Re-Entry |
| US-0.5 | Handle Task Creation Backend Failure | PER-01 Marcus | JTBD-01.1 | JRN-01.1:Capture |
| US-1.1 | View All Tasks on Page Load | PER-01 Marcus | JTBD-01.2 | JRN-01.1:Arrive/Orient |
| US-1.2 | See Empty State When No Tasks Exist | PER-01 Marcus | JTBD-01.2 | JRN-01.1:Orient |
| US-1.3 | View Completed and Active Tasks Together | PER-02 Priya | JTBD-02.2 | JRN-02.2:Review |
| US-1.4 | List Reflects Changes Without Page Reload | PER-02 Priya | JTBD-02.1 | JRN-02.1:Rapid Re-Entry |
| US-1.5 | Handle Task List Load Failure | PER-02 Priya | JTBD-02.1 | JRN-02.1:Pre-Meeting Setup |
| US-2.1 | Mark a Task as Complete | PER-01 Marcus | JTBD-01.3 | JRN-01.2:Complete |
| US-2.2 | Unmark a Completed Task | PER-01 Marcus | JTBD-01.3 | JRN-01.2:Complete |
| US-2.3 | See Visual Distinction Between Active and Completed Tasks | PER-02 Priya | JTBD-02.3 | JRN-02.2:Review/Mark Complete |
| US-2.4 | Handle Task Completion Backend Failure | PER-01 Marcus | JTBD-01.3 | JRN-01.2:Complete |
| US-3.1 | Delete a Task | PER-01 Marcus | JTBD-01.3 | JRN-01.2:Delete |
| US-3.2 | See Empty State After Deleting Last Task | PER-02 Priya | JTBD-01.3 | JRN-01.2:Verify |
| US-3.3 | Handle Task Deletion Backend Failure | PER-01 Marcus | JTBD-01.3 | JRN-01.2:Delete |
| US-4.1 | Tasks Survive a Browser Refresh | PER-01 Marcus | JTBD-01.4 | JRN-01.2:Close & Return |
| US-4.2 | Tasks Survive a Server Restart | PER-02 Priya | JTBD-02.4 | JRN-02.2:Morning Load |
| US-4.3 | Backend Auto-Initializes Storage on First Run | PER-01 Marcus | JTBD-01.1 | JRN-01.1:Arrive |
| US-4.4 | All Task Writes Are Committed Before Success Response | PER-02 Priya | JTBD-02.2 | JRN-02.1:Cross-Device Verification |

**Personas served:** PER-01 (Marcus Webb), PER-02 (Priya Nolan)
**JTBD addressed:** JTBD-01.1, JTBD-01.2, JTBD-01.3, JTBD-01.4, JTBD-02.1, JTBD-02.2, JTBD-02.3, JTBD-02.4 (all 8)
**Journey completeness:** All 4 journeys (JRN-01.1, JRN-01.2, JRN-02.1, JRN-02.2) are fully covered end-to-end

---

## Coverage Analysis

### Persona Coverage

| Persona | R1 Stories | Journeys Covered | JTBD Addressed |
|---|---|---|---|
| PER-01 Marcus Webb | US-0.1, US-0.3, US-0.5, US-1.1, US-1.2, US-2.1, US-2.2, US-2.4, US-3.1, US-3.3, US-4.1, US-4.3 (12 stories) | JRN-01.1, JRN-01.2 | JTBD-01.1, JTBD-01.2, JTBD-01.3, JTBD-01.4 |
| PER-02 Priya Nolan | US-0.2, US-0.4, US-1.3, US-1.4, US-1.5, US-2.3, US-3.2, US-4.2, US-4.4 (9 stories) | JRN-02.1, JRN-02.2 | JTBD-02.1, JTBD-02.2, JTBD-02.3, JTBD-02.4 |

> Note: Both personas are served in R1 with full journey coverage. Story count differs because Marcus's journeys have more error-handling and detail-level stories; all features are shared and benefit both personas equally.

---

### JTBD Coverage

| JTBD ID | Outcome | Stories Addressing It | Release |
|---|---|---|---|
| JTBD-01.1 | Frictionless task capture | US-0.1, US-0.3, US-0.5, US-4.3 | R1 |
| JTBD-01.2 | Rapid workday prioritisation | US-1.1, US-1.2 | R1 |
| JTBD-01.3 | List hygiene without friction | US-2.1, US-2.2, US-2.4, US-3.1, US-3.2, US-3.3 | R1 |
| JTBD-01.4 | Reliable continuity across interruptions | US-4.1 | R1 |
| JTBD-02.1 | In-meeting action item capture | US-0.2, US-0.4, US-1.4, US-1.5 | R1 |
| JTBD-02.2 | Shared team visibility without accounts | US-1.3, US-4.4 | R1 |
| JTBD-02.3 | Progress signalling to the team | US-2.3 | R1 |
| JTBD-02.4 | Durable cross-session reliability | US-4.2 | R1 |

**All 8 JTBD outcomes are addressed in R1. No unaddressed outcomes.**

---

### Journey Stage Coverage

| Journey | Stage | Feature | Stories Mapped | Covered? |
|---|---|---|---|---|
| JRN-01.1 | Arrive | F1, F4 | US-1.1, US-4.3 | ✅ |
| JRN-01.1 | Orient | F1 | US-1.1, US-1.2 | ✅ |
| JRN-01.1 | Capture | F0 | US-0.1, US-0.3, US-0.5 | ✅ |
| JRN-01.1 | Confirm | F1 | US-1.4 | ✅ |
| JRN-01.1 | Return | — (external) | n/a — browser behavior | ✅ (no story needed) |
| JRN-01.2 | Review | F1, F2 | US-1.1, US-1.3 | ✅ |
| JRN-01.2 | Complete | F2 | US-2.1, US-2.2, US-2.4 | ✅ |
| JRN-01.2 | Delete | F3 | US-3.1, US-3.3 | ✅ |
| JRN-01.2 | Verify | F1 | US-3.2 | ✅ |
| JRN-01.2 | Close & Return | F4, F1 | US-4.1 | ✅ |
| JRN-02.1 | Pre-Meeting Setup | F1, F4 | US-1.1, US-1.5, US-4.3 | ✅ |
| JRN-02.1 | First Capture | F0 | US-0.2 | ✅ |
| JRN-02.1 | Rapid Re-Entry | F0, F1 | US-0.4, US-1.4 | ✅ |
| JRN-02.1 | Mid-Meeting Scan | F1 | US-1.3 | ✅ |
| JRN-02.1 | Post-Meeting Share | — (external) | n/a — URL sharing, no story needed | ✅ (no story needed) |
| JRN-02.1 | Cross-Device Verification | F1, F4 | US-4.4 | ✅ |
| JRN-02.2 | Morning Load | F1, F4 | US-1.1, US-4.2 | ✅ |
| JRN-02.2 | Review | F1, F2 | US-1.3, US-2.3 | ✅ |
| JRN-02.2 | Mark Complete | F2 | US-2.1, US-2.3 | ✅ |
| JRN-02.2 | Delete Cancelled Task | F3 | US-3.1, US-3.2 | ✅ |
| JRN-02.2 | Verify & Close | F1 | US-1.4 | ✅ |

**All journey stages with product touchpoints are covered. Zero gaps.**

---

### Gap Analysis

**Journey Stages Without Coverage:**
- None. All 19 product-touchpoint stages across 4 journeys are mapped to at least one story.
- Two stages (JRN-01.1:Return, JRN-02.1:Post-Meeting Share) are browser or external URL behaviors outside the product's scope — correctly without stories.

**JTBD Outcomes Without Derived NaC:**
- None. All 8 JTBD outcomes have at least one NaC derived and mapped to a story.

**Orphan Stories (not mapped to any journey stage):**
- None. All 21 user stories (US-0.1 through US-4.4) are placed on the map.

**Potential Post-MVP Gaps Noted (not in current UserStories):**

| Gap | JTBD | Journey | Suggested R2 Enhancement |
|---|---|---|---|
| Bulk delete | JTBD-01.3 | JRN-01.2:Delete | Marcus explicitly notes the absence of bulk delete in the Delete stage; flagged as v2 consideration in JOURNEYS doc |
| Undo after delete | JTBD-01.3 | JRN-02.2:Delete Cancelled Task | JOURNEYS doc flags a 2–3 second undo window as a v2 enhancement; accidental deletion has no recovery in v1 |
| Timestamps / visual freshness | JTBD-02.1 | JRN-02.1:Mid-Meeting Scan | Priya cannot distinguish new meeting items from prior items without timestamps or visual grouping |

---

## NaC-to-Acceptance Criteria Alignment

Verification that NaC statements align with the acceptance criteria in UserStories-TodoApp.md.

| SM ID | Story | NaC Statement | Matching Acceptance Criterion | Aligned? |
|---|---|---|---|---|
| SM-0.1 | US-0.1 | Task submitted via Enter; appears in list within 1 second; input clears and refocuses | "Pressing Enter while the input is non-empty submits the task" + "The new task appears at the bottom of the task list immediately" + "The input field is cleared and focus returns to it" | ✅ |
| SM-0.2 | US-0.2 | Add button submits task within 1 second; reachable in ≤ 2 interactions | "Clicking the button while the input is non-empty submits the task" + "The button is reachable in ≤ 2 interactions" | ✅ |
| SM-0.3 | US-0.3 | Blank submission rejected with inline error; input stays focused | "Submitting an empty input does not create a task" + "The input field remains focused so the user can correct the entry immediately" | ✅ |
| SM-0.4 | US-0.4 | Titles > 500 chars rejected inline; typed text preserved for editing | "Task titles exceeding 500 characters are rejected" + "The user's typed text remains in the input field so they can edit it" | ✅ |
| SM-0.5 | US-0.5 | Save failure shows error; typed text preserved; task not added | "The task is not added to the list if the API call fails" + "The user's typed text remains in the input field after the failure" | ✅ |
| SM-1.1 | US-1.1 | All persisted tasks rendered within 1 second; no interaction required | "The task list is fetched from the backend automatically on every page load" + "The list loads and renders within 1 second" + "No user action is required to trigger the initial load" | ✅ |
| SM-1.2 | US-1.2 | Empty-state placeholder shown; disappears when a task is added | "When the backend returns an empty array, a placeholder message is displayed" + "The placeholder does not display when at least one task exists" | ✅ |
| SM-1.3 | US-1.3 | Both completed and active in one list; visual differentiation; no filtering | "Active tasks and completed tasks appear in the same unified list" + "No filtering, sorting, or tab-switching is required" | ✅ |
| SM-1.4 | US-1.4 | List updates live after each action; no reload required | "A newly created task appears in the list immediately" + "A deleted task disappears from the list immediately" + "A task's visual state updates immediately when completion is toggled" | ✅ |
| SM-1.5 | US-1.5 | Load failure shows clear error, not an empty list | "An empty task list is not rendered in place of the error message" + specific error messages for 503/500/parse failure | ✅ |
| SM-2.1 | US-2.1 | Toggle changes visual state immediately; PATCH persisted; task stays visible | "Clicking the control immediately changes its visual style" + "A PATCH request is sent" + "The task remains visible in the list after being marked complete" | ✅ |
| SM-2.2 | US-2.2 | Toggle reverts visual state immediately; round-trip idempotent | "Clicking the completion control on a completed task immediately reverts its visual style" + "Toggling the same task twice returns it to its original state" | ✅ |
| SM-2.3 | US-2.3 | Strikethrough/muted for completed; active tasks in default style; immediate on toggle | "Completed tasks display with a visual differentiator" + "The visual distinction is applied immediately on toggle" | ✅ |
| SM-2.4 | US-2.4 | Visual state reverts on PATCH failure; error message shown | "The task's visual state reverts to its pre-toggle state" + "The revert happens before or simultaneously with the error message display" | ✅ |
| SM-3.1 | US-3.1 | Single click removes task immediately; no confirmation dialog | "A single click on the delete control immediately removes the task from the rendered list (no confirmation dialog)" | ✅ |
| SM-3.2 | US-3.2 | Empty-state appears after last task deleted; no reload | "When the last task in the list is deleted, the empty-state message appears immediately" + "No page reload is required" | ✅ |
| SM-3.3 | US-3.3 | Task restored on DELETE failure; error message shown | "If the DELETE request returns 503, the task is restored to the list" + "The restore happens before or simultaneously with the error message" | ✅ |
| SM-4.1 | US-4.1 | Hard refresh restores all tasks and completion states from backend | "After a hard browser refresh, all previously created tasks are present" + "Tasks are fetched from the backend API on reload — not reconstructed from localStorage" | ✅ |
| SM-4.2 | US-4.2 | Server restart preserves all tasks with correct states | "After a server process restart, all previously created tasks are returned by GET /api/tasks" + "Task data is stored in durable server-side storage" | ✅ |
| SM-4.3 | US-4.3 | Backend auto-creates DB on first run; app immediately serves empty task list | "On first startup, the backend automatically creates the database/storage file and tasks table" + "No manual database setup or migration step is required" | ✅ |
| SM-4.4 | US-4.4 | Success returned only after durable write; any session sees same state | "The backend only returns a 201/200 success response after the task record is confirmed written to durable storage" | ✅ |

**All 21 NaC statements are aligned with their corresponding acceptance criteria. Zero mismatches.**

---

## Validation Checklist

- [x] Every UserStory (US-0.1 through US-4.4) appears in the map — 21/21 placed
- [x] Every mapped story has a NaC derived from a specific JTBD outcome
- [x] NaC Derivation Table provides full traceability: JTBD ID → Journey Stage → NaC → Story
- [x] Release R1 defined with story list, personas served, and JTBD addressed
- [x] Coverage analysis confirms zero journey stage gaps and zero unaddressed JTBD outcomes
- [x] Zero orphan stories (all 21 stories mapped to at least one journey stage)
- [x] R1 enables all 4 complete journeys (JRN-01.1, JRN-01.2, JRN-02.1, JRN-02.2)
- [x] NaC-to-Acceptance Criteria alignment verified: 21/21 aligned
- [x] Post-MVP gaps identified: bulk delete, undo after delete, timestamp visibility

---

*Document generated: 2026-05-07*
*Related documents: PERSONAS-TodoApp.md, JTBD-TodoApp.md, JOURNEYS-TodoApp.md, UserStories-TodoApp.md, PRD-TodoApp.md*

# User Stories
## Basic To-Do App (TodoApp)

| Field | Value |
|---|---|
| **Product Name** | TodoApp — Basic To-Do App |
| **Version** | 1.0 |
| **Date** | 2026-05-07 |
| **Status** | Draft |
| **Related PRD** | PRD-TodoApp.md |
| **Related FRD** | FRD-TodoApp.md |
| **Personas** | PERSONAS-TodoApp.md |

---

## Priority Definitions

| Priority | Label | Description |
|---|---|---|
| **P0** | Critical | Required for MVP launch; product is non-functional without it |
| **P1** | High | Ship in first post-MVP iteration; significant user value |
| **P2** | Medium | Planned for near-term roadmap |
| **P3** | Low | Backlog; deferred to future consideration |

---

## Epic 0: Task Creation (F0)

Users can add new tasks to the list via a text input. This is the entry point for all task data in the system and must work instantly with zero friction.

---

### US-0.1: Add a Task by Pressing Enter
**As a** Marcus Webb (Everyday Task Tracker), **I want to** type a task title and press Enter to add it, **so that** I can capture tasks quickly without reaching for the mouse.

**Acceptance Criteria:**
- [ ] A text input field is visible and focused (or easily focusable) on the page
- [ ] Pressing Enter while the input is non-empty submits the task
- [ ] The new task appears at the bottom of the task list immediately after submission
- [ ] The input field is cleared and focus returns to it after successful submission
- [ ] No page reload occurs during or after submission

**Priority:** P0 | **Feature Ref:** F0

---

### US-0.2: Add a Task by Clicking the Add Button
**As a** Priya Nolan (Lightweight Project Coordinator), **I want to** click an Add button to submit a typed task, **so that** I can add action items in a way that is obvious and accessible during a meeting.

**Acceptance Criteria:**
- [ ] An Add button (or "+" icon) is visible adjacent to the task input field
- [ ] Clicking the button while the input is non-empty submits the task
- [ ] The new task appears at the bottom of the task list immediately after submission
- [ ] The input field is cleared and focus returns to it after successful submission
- [ ] The button is reachable in ≤ 2 interactions from the page landing state

**Priority:** P0 | **Feature Ref:** F0

---

### US-0.3: Prevent Empty Task Submission
**As a** Marcus Webb (Everyday Task Tracker), **I want to** be stopped from accidentally submitting a blank task, **so that** my task list only contains meaningful items.

**Acceptance Criteria:**
- [ ] Submitting an empty input (or whitespace-only input) does not create a task
- [ ] No API call is made when the input fails validation
- [ ] An inline error message "Task title cannot be empty." is displayed near the input field
- [ ] The error message dismisses after 5 seconds or on the next user interaction
- [ ] The input field remains focused so the user can correct the entry immediately

**Priority:** P0 | **Feature Ref:** F0

---

### US-0.4: Enforce Task Title Character Limit
**As a** Priya Nolan (Lightweight Project Coordinator), **I want to** be informed if my task title is too long, **so that** I can shorten it and still save the task successfully.

**Acceptance Criteria:**
- [ ] Task titles exceeding 500 characters are rejected
- [ ] Frontend validation catches the limit before making an API call
- [ ] An inline error message "Task title must be 500 characters or fewer." is displayed
- [ ] If the backend returns a 400 `TITLE_TOO_LONG` error, the same message is shown
- [ ] The user's typed text remains in the input field so they can edit it

**Priority:** P0 | **Feature Ref:** F0

---

### US-0.5: Handle Task Creation Backend Failure
**As a** Marcus Webb (Everyday Task Tracker), **I want to** see a clear error message if saving my task fails, **so that** I know to try again and do not lose my input.

**Acceptance Criteria:**
- [ ] If the backend returns 503, the error message "Unable to save task. Please try again." is shown inline
- [ ] If the backend returns 500, the error message "Something went wrong. Please try again." is shown inline
- [ ] The task is not added to the list if the API call fails
- [ ] The user's typed text remains in the input field after the failure
- [ ] Error message dismisses after 5 seconds or on the next user interaction

**Priority:** P0 | **Feature Ref:** F0

---

## Epic 1: Task List View (F1)

Users can see all their tasks in a single ordered list immediately upon opening the app. The list reflects live state after every action.

---

### US-1.1: View All Tasks on Page Load
**As a** Marcus Webb (Everyday Task Tracker), **I want to** see all my tasks as soon as I open the app, **so that** I can immediately scan what I need to work on without any extra steps.

**Acceptance Criteria:**
- [ ] The task list is fetched from the backend automatically on every page load
- [ ] All persisted tasks are rendered in the list ordered by creation time (oldest first)
- [ ] Each task item displays its title and completion status indicator
- [ ] Each task item displays a delete action control
- [ ] The list loads and renders within 1 second under normal conditions (verified on a local development environment with frontend and backend on the same machine)
- [ ] No user action (click, scroll, button press) is required to trigger the initial load

**Priority:** P0 | **Feature Ref:** F1

---

### US-1.2: See Empty State When No Tasks Exist
**As a** Marcus Webb (Everyday Task Tracker), **I want to** see a helpful placeholder message when my task list is empty, **so that** I know the app is working and I understand how to get started.

**Acceptance Criteria:**
- [ ] When the backend returns an empty array, a placeholder message is displayed (e.g., "No tasks yet. Add one above!")
- [ ] The placeholder does not display when at least one task exists
- [ ] The placeholder appears in the task list area, not as a page-level alert
- [ ] The empty state is shown after the last task is deleted without a page reload

**Priority:** P0 | **Feature Ref:** F1

---

### US-1.3: View Completed and Active Tasks Together
**As a** Priya Nolan (Lightweight Project Coordinator), **I want to** see both completed and open tasks in the same list, **so that** I can track overall progress without switching views or filters.

**Acceptance Criteria:**
- [ ] Active tasks (completed: false) and completed tasks (completed: true) appear in the same unified list
- [ ] Completed tasks are visually differentiated from active tasks (e.g., strikethrough text, muted color)
- [ ] No filtering, sorting, or tab-switching is required to see all tasks
- [ ] The visual distinction is clear enough to distinguish done from pending at a glance

**Priority:** P0 | **Feature Ref:** F1

---

### US-1.4: List Reflects Changes Without Page Reload
**As a** Priya Nolan (Lightweight Project Coordinator), **I want to** see the task list update immediately after any action, **so that** I can stay focused without triggering manual refreshes during a meeting.

**Acceptance Criteria:**
- [ ] A newly created task appears in the list immediately after submission (no reload required)
- [ ] A task's visual state updates immediately when its completion is toggled (no reload required)
- [ ] A deleted task disappears from the list immediately after deletion (no reload required)
- [ ] The list count and empty-state logic update correctly after each action

**Priority:** P0 | **Feature Ref:** F1

---

### US-1.5: Handle Task List Load Failure
**As a** Priya Nolan (Lightweight Project Coordinator), **I want to** see a clear error if the task list cannot be loaded, **so that** I know to refresh the page rather than assuming the list is simply empty.

**Acceptance Criteria:**
- [ ] If the backend returns 503 on load, the message "Unable to load tasks. Please refresh the page." is shown
- [ ] If the backend returns 500 on load, the message "Something went wrong loading your tasks." is shown
- [ ] If the API response cannot be parsed, the message "Unable to display tasks. Please refresh the page." is shown
- [ ] An empty task list is not rendered in place of the error message
- [ ] The error message is shown in the task list area so context is clear

**Priority:** P0 | **Feature Ref:** F1

---

## Epic 2: Task Completion (F2)

Users can mark tasks as complete or revert them to active. Completion state is visually clear and immediately persisted.

---

### US-2.1: Mark a Task as Complete
**As a** Marcus Webb (Everyday Task Tracker), **I want to** check off a task when I finish it, **so that** my list accurately reflects what I've completed and what still needs doing.

**Acceptance Criteria:**
- [ ] Each task item has a checkbox or toggle control
- [ ] Clicking the control on an active task (completed: false) immediately changes its visual style to indicate completion (e.g., strikethrough text, muted appearance)
- [ ] A PATCH request is sent to the backend with `{ "completed": true }`
- [ ] The backend confirms the update with a 200 OK response
- [ ] The task remains visible in the list after being marked complete (it is not auto-deleted or hidden)
- [ ] The task does not move position in the list — it stays at its original `created_at` order regardless of completion state
- [ ] The entire toggle interaction is reachable in ≤ 2 clicks from the list view

**Priority:** P0 | **Feature Ref:** F2

---

### US-2.2: Unmark a Completed Task
**As a** Marcus Webb (Everyday Task Tracker), **I want to** reopen a task I accidentally marked complete, **so that** I can correct my list without deleting and re-entering the task.

**Acceptance Criteria:**
- [ ] Clicking the completion control on a completed task (completed: true) immediately reverts its visual style to active (normal text, no strikethrough)
- [ ] A PATCH request is sent to the backend with `{ "completed": false }`
- [ ] The backend confirms the update with a 200 OK response
- [ ] Toggling the same task twice returns it to its original state (idempotent round-trip)

**Priority:** P0 | **Feature Ref:** F2

---

### US-2.3: See Visual Distinction Between Active and Completed Tasks
**As a** Priya Nolan (Lightweight Project Coordinator), **I want to** immediately tell at a glance which tasks are done and which are still open, **so that** I can quickly assess team progress without reading every item carefully.

**Acceptance Criteria:**
- [ ] Completed tasks display with a visual differentiator (strikethrough text and/or muted color)
- [ ] Active tasks display in the default/standard style (no strikethrough, full opacity)
- [ ] The visual distinction is applied immediately on toggle without a page reload
- [ ] The distinction is consistent across all tasks in the list simultaneously

**Priority:** P0 | **Feature Ref:** F2

---

### US-2.4: Handle Task Completion Backend Failure
**As a** Marcus Webb (Everyday Task Tracker), **I want to** see an error and have my task revert if the completion update fails, **so that** the list accurately reflects what is actually saved rather than showing a false state.

**Acceptance Criteria:**
- [ ] If the PATCH request returns 404, the task's visual state reverts to its pre-toggle state and the message "Task not found. Please refresh the page." is shown
- [ ] If the PATCH request returns 503, the task's visual state reverts and the message "Unable to update task. Please try again." is shown
- [ ] If the PATCH request returns 500, the task's visual state reverts and the message "Something went wrong. Please try again." is shown
- [ ] The revert happens before or simultaneously with the error message display
- [ ] Error messages dismiss after 5 seconds or on the next user interaction

**Priority:** P0 | **Feature Ref:** F2

---

## Epic 3: Task Deletion (F3)

Users can permanently remove tasks from the list. Deletion is immediate, irreversible, and requires no confirmation dialog in v1.

---

### US-3.1: Delete a Task
**As a** Marcus Webb (Everyday Task Tracker), **I want to** delete a task with a single click, **so that** I can keep my list clean and uncluttered without unnecessary confirmation steps.

**Acceptance Criteria:**
- [ ] Each task item has a visible delete button or icon
- [ ] A single click on the delete control immediately removes the task from the rendered list (no confirmation dialog)
- [ ] A DELETE request is sent to the backend for the task's ID
- [ ] The backend confirms deletion with a 200 OK or 204 No Content response
- [ ] The task is permanently removed from the database (no soft-delete or archive)
- [ ] The deletion control is reachable in ≤ 2 clicks from the list view

**Priority:** P0 | **Feature Ref:** F3

---

### US-3.2: See Empty State After Deleting Last Task
**As a** Priya Nolan (Lightweight Project Coordinator), **I want to** see the empty-state placeholder after deleting the last task, **so that** I know the list is empty rather than thinking there is a display error.

**Acceptance Criteria:**
- [ ] When the last task in the list is deleted, the empty-state message (e.g., "No tasks yet. Add one above!") appears immediately in the task list area
- [ ] No page reload is required for the empty state to appear
- [ ] The empty state disappears as soon as a new task is added

**Priority:** P0 | **Feature Ref:** F3

---

### US-3.3: Handle Task Deletion Backend Failure
**As a** Marcus Webb (Everyday Task Tracker), **I want to** have my task restored to the list if deletion fails, **so that** I do not lose track of a task that was not actually deleted.

**Acceptance Criteria:**
- [ ] If the DELETE request returns 503, the task is restored to the list and the message "Unable to delete task. Please try again." is shown
- [ ] If the DELETE request returns 500, the task is restored to the list and the message "Something went wrong. Please try again." is shown
- [ ] If the DELETE request returns 404, the frontend shows "Task not found. It may have already been deleted." and no state change is needed (the item was already removed visually)
- [ ] The restore happens before or simultaneously with the error message display
- [ ] Error messages dismiss after 5 seconds or on the next user interaction

**Priority:** P0 | **Feature Ref:** F3

---

## Epic 4: Task Persistence (F4)

All task data is durably stored server-side so the full list — titles and completion states — is restored on every page load, browser refresh, and server restart.

---

### US-4.1: Tasks Survive a Browser Refresh
**As a** Marcus Webb (Everyday Task Tracker), **I want to** find my tasks still there after reloading the page, **so that** I never lose my list due to an accidental refresh or browser crash.

**Acceptance Criteria:**
- [ ] After a hard browser refresh (Ctrl+Shift+R / Cmd+Shift+R), all previously created tasks are present in the list
- [ ] Each task's title is restored exactly as entered
- [ ] Each task's completion state (active or complete) is restored accurately
- [ ] Tasks are fetched from the backend API on reload — not reconstructed from localStorage or sessionStorage

**Priority:** P0 | **Feature Ref:** F4

---

### US-4.2: Tasks Survive a Server Restart
**As a** Priya Nolan (Lightweight Project Coordinator), **I want to** trust that tasks are still present after any server downtime or restart, **so that** my team's action items are never silently lost.

**Acceptance Criteria:**
- [ ] After a server process restart, all previously created tasks are returned by `GET /api/tasks`
- [ ] Task data is stored in durable server-side storage (e.g., SQLite file, PostgreSQL) — not in server memory only
- [ ] No tasks are lost, duplicated, or corrupted after a restart
- [ ] The backend initializes the storage connection and schema on startup before serving any requests

**Priority:** P0 | **Feature Ref:** F4

---

### US-4.3: Backend Auto-Initializes Storage on First Run
**As a** Marcus Webb (Everyday Task Tracker), **I want to** open the app for the first time and have it work immediately, **so that** I can add my first task within 10 seconds without any manual setup.

**Acceptance Criteria:**
- [ ] On first startup, the backend automatically creates the database/storage file and tasks table if they do not already exist
- [ ] No manual database setup or migration step is required before the app is usable
- [ ] The app serves a valid (empty) task list on first `GET /api/tasks` after auto-initialization
- [ ] If storage cannot be initialized, the backend logs a fatal error and exits — it does not start in a degraded in-memory mode

**Priority:** P0 | **Feature Ref:** F4

---

### US-4.4: All Task Writes Are Committed Before Success Response
**As a** Priya Nolan (Lightweight Project Coordinator), **I want to** trust that a task is truly saved the moment the UI confirms it, **so that** I can close the browser immediately after adding an item without risking data loss.

**Acceptance Criteria:**
- [ ] The backend only returns a 201/200 success response after the task record is confirmed written to durable storage
- [ ] The backend does not return success if the storage write fails — it returns a 500 `STORAGE_WRITE_ERROR` instead
- [ ] The frontend shows "Unable to save changes. Please try again." if a write failure error is received
- [ ] This behavior applies to task create (POST), task update (PATCH), and task delete (DELETE) operations

**Priority:** P0 | **Feature Ref:** F4

---

## Story Index

| Story ID | Title | Persona(s) | Priority | Feature Ref |
|---|---|---|---|---|
| US-0.1 | Add a Task by Pressing Enter | Marcus Webb | P0 | F0 |
| US-0.2 | Add a Task by Clicking the Add Button | Priya Nolan | P0 | F0 |
| US-0.3 | Prevent Empty Task Submission | Marcus Webb | P0 | F0 |
| US-0.4 | Enforce Task Title Character Limit | Priya Nolan | P0 | F0 |
| US-0.5 | Handle Task Creation Backend Failure | Marcus Webb | P0 | F0 |
| US-1.1 | View All Tasks on Page Load | Marcus Webb | P0 | F1 |
| US-1.2 | See Empty State When No Tasks Exist | Marcus Webb | P0 | F1 |
| US-1.3 | View Completed and Active Tasks Together | Priya Nolan | P0 | F1 |
| US-1.4 | List Reflects Changes Without Page Reload | Priya Nolan | P0 | F1 |
| US-1.5 | Handle Task List Load Failure | Priya Nolan | P0 | F1 |
| US-2.1 | Mark a Task as Complete | Marcus Webb | P0 | F2 |
| US-2.2 | Unmark a Completed Task | Marcus Webb | P0 | F2 |
| US-2.3 | See Visual Distinction Between Active and Completed Tasks | Priya Nolan | P0 | F2 |
| US-2.4 | Handle Task Completion Backend Failure | Marcus Webb | P0 | F2 |
| US-3.1 | Delete a Task | Marcus Webb | P0 | F3 |
| US-3.2 | See Empty State After Deleting Last Task | Priya Nolan | P0 | F3 |
| US-3.3 | Handle Task Deletion Backend Failure | Marcus Webb | P0 | F3 |
| US-4.1 | Tasks Survive a Browser Refresh | Marcus Webb | P0 | F4 |
| US-4.2 | Tasks Survive a Server Restart | Priya Nolan | P0 | F4 |
| US-4.3 | Backend Auto-Initializes Storage on First Run | Marcus Webb | P0 | F4 |
| US-4.4 | All Task Writes Are Committed Before Success Response | Priya Nolan | P0 | F4 |

**Total Stories:** 21 | **Epics:** 5 | **All Priority P0 (MVP)**

---

*Document generated: 2026-05-07*
*Related documents: PRD-TodoApp.md, FRD-TodoApp.md, PERSONAS-TodoApp.md, TechArch-TodoApp.md*

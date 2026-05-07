# Requirements Traceability Matrix
## Basic To-Do App (TodoApp)

**Version:** 1.0
**Date:** 2026-05-07
**Status:** Draft
**Project:** TodoApp — Basic To-Do App

| Field | Value |
|---|---|
| **Document Type** | Requirements Traceability Matrix (RTM) |
| **Related PRD** | PRD-TodoApp.md |
| **Related FRD** | FRD-TodoApp.md |
| **Related TechArch** | TechArch-TodoApp.md |
| **Related UserStories** | UserStories-TodoApp.md |

---

## Table of Contents

1. [Overview](#1-overview)
2. [Requirements Summary](#2-requirements-summary)
3. [Traceability Matrix](#3-traceability-matrix)
4. [Requirements Detail](#4-requirements-detail)
5. [Test Case Coverage](#5-test-case-coverage)
6. [Change Management](#6-change-management)
7. [Approval](#7-approval)

---

## 1. Overview

This Requirements Traceability Matrix (RTM) provides bidirectional traceability between all TodoApp specification documents. It ensures every product requirement is fully elaborated in functional terms, grounded in a technical implementation decision, and covered by at least one user story and test case. The matrix spans four levels of specification: the Product Requirements Document (PRD-TodoApp.md), the Functional Requirements Document (FRD-TodoApp.md), the Technical Architecture Document (TechArch-TodoApp.md), and the User Stories document (UserStories-TodoApp.md).

TodoApp is a lightweight, full-stack to-do application delivering five core product features — Task Creation (F0), Task List View (F1), Task Completion (F2), Task Deletion (F3), and Task Persistence (F4) — all rated P0 (MVP-critical). Every requirement in this matrix derives from a validated, active project goal defined in PROJECT.md and is traceable forward to a user story acceptance criterion and backward to a product capability. No orphaned requirements exist in this baseline.

The RTM serves as the authoritative reference for impact analysis during change control, as a verification checklist during QA, and as an audit trail for stakeholders confirming that the shipped product satisfies the agreed scope. Coverage is reported at the feature level; all 21 user stories and their associated test cases are indexed here with explicit linkage to the PRD feature and FRD functional specification from which they originate.

---

## 2. Requirements Summary

### 2.1 PRD Features (Source: PRD-TodoApp.md)

- **F0 — Task Creation (P0):** Users can add a new task via text input and submit with Enter or button click; task is immediately saved to backend and appears in list without page reload
- **F1 — Task List View (P0):** Users can view all tasks in a single ordered list fetched from backend on page load; list reflects create, complete, and delete actions in real time
- **F2 — Task Completion (P0):** Users can toggle a task's completion state; completed tasks are visually differentiated; state is persisted immediately to backend
- **F3 — Task Deletion (P0):** Users can permanently delete a task with a single click; task is removed from backend and list immediately; no confirmation dialog in v1
- **F4 — Task Persistence (P0):** All tasks are stored server-side in durable storage; full task list (titles + completion states) is restored on every page load, browser refresh, and server restart

### 2.2 Non-Functional Requirements (Source: PRD-TodoApp.md §6)

- **NFR-PERF:** Task list loads within 1 second under normal local development conditions
- **NFR-RELIABILITY:** Task data is not lost on server restart (durable storage required)
- **NFR-SIMPLICITY:** No authentication, no multi-user isolation, no real-time sync for v1
- **NFR-USABILITY:** All core actions (create, complete, delete) accessible in ≤ 2 clicks/interactions
- **NFR-MAINTAINABILITY:** Codebase kept minimal; avoid unnecessary abstractions or dependencies
- **NFR-COMPATIBILITY:** Works in modern desktop browsers (Chrome, Firefox, Safari, Edge)
- **NFR-SCALABILITY:** Not a v1 concern; single-user or shared state is acceptable

### 2.3 Functional Requirements (Source: FRD-TodoApp.md)

- **F0 Functional Specs:** Input validation (non-empty, ≤ 500 chars), optimistic UI append, POST /api/tasks, 201 response with task object, error revert and inline messaging
- **F1 Functional Specs:** GET /api/tasks on page load, ordered by created_at ASC, empty-state message, in-memory list state updates after all CRUD actions
- **F2 Functional Specs:** PATCH /api/tasks/:id with boolean completed, optimistic visual toggle, revert on failure, task order unchanged
- **F3 Functional Specs:** DELETE /api/tasks/:id, optimistic removal, revert on failure, empty-state shown when last task deleted
- **F4 Functional Specs:** Durable server-side storage (SQLite), CREATE TABLE IF NOT EXISTS on startup, write committed before success response, fatal exit on storage init failure

### 2.4 Technical Architecture Specifications (Source: TechArch-TodoApp.md)

- **SPEC-ARCH:** Three-tier architecture — vanilla JS frontend, Express.js REST API backend, SQLite database; single-process deployment
- **SPEC-STACK:** Node.js 20 LTS, Express.js 4.x, SQLite 3.x via better-sqlite3 9.x, helmet 7.x, vanilla HTML/CSS/JS frontend
- **SPEC-DATA:** Single `tasks` table with columns `id` (INTEGER PK AUTOINCREMENT), `title` (VARCHAR 500 NOT NULL), `completed` (BOOLEAN DEFAULT FALSE), `created_at` (DATETIME DEFAULT CURRENT_TIMESTAMP); index `idx_tasks_created_at` on `created_at ASC`
- **SPEC-API:** Four REST endpoints: GET /api/tasks, POST /api/tasks, PATCH /api/tasks/:id, DELETE /api/tasks/:id; JSON content type; no auth; error envelope `{ error: { code, message } }`
- **SPEC-SECURITY:** No auth layer (by design, v1); parameterized SQL queries only; helmet HTTP security headers (X-Content-Type-Options, X-Frame-Options, CSP, Referrer-Policy); CORS not required (same-origin)
- **SPEC-COMPONENTS:** server.js (bootstrap), db.js (SQLite singleton + schema init), routes/tasks.js (route definitions), controllers/taskController.js (business logic), public/ (static frontend assets)
- **SPEC-STARTUP:** DB connection opened → schema created → middleware configured → routes mounted → HTTP server bound; fatal exit (process.exit(1)) if DB init fails

### 2.5 User Stories (Source: UserStories-TodoApp.md)

- **Epic 0 (F0):** 5 stories — US-0.1 through US-0.5 (21 acceptance criteria total)
- **Epic 1 (F1):** 5 stories — US-1.1 through US-1.5 (21 acceptance criteria total)
- **Epic 2 (F2):** 4 stories — US-2.1 through US-2.4 (18 acceptance criteria total)
- **Epic 3 (F3):** 3 stories — US-3.1 through US-3.3 (14 acceptance criteria total)
- **Epic 4 (F4):** 4 stories — US-4.1 through US-4.4 (16 acceptance criteria total)
- **Total:** 21 stories across 5 epics; all P0 (MVP-critical)

---

## 3. Traceability Matrix

### 3.1 Full Bidirectional Traceability Table

| PRD Feature | FRD Specification | TechArch Spec | User Stories | Test Cases |
|---|---|---|---|---|
| **F0: Task Creation** | POST /api/tasks; title validation (non-empty, ≤500 chars); optimistic append; 201 response; error revert | SPEC-API (POST /api/tasks); SPEC-COMPONENTS (taskController.js validation + INSERT SQL); SPEC-DATA (tasks table, title VARCHAR 500); SPEC-STACK (Express.js, better-sqlite3) | US-0.1, US-0.2, US-0.3, US-0.4, US-0.5 | TEST-0.1 through TEST-0.10 |
| **F1: Task List View** | GET /api/tasks on page load; ORDER BY created_at ASC; render task items; empty-state message; in-memory state updates | SPEC-API (GET /api/tasks); SPEC-DATA (idx_tasks_created_at); SPEC-COMPONENTS (app.js render logic); SPEC-ARCH (three-tier; static file serving) | US-1.1, US-1.2, US-1.3, US-1.4, US-1.5 | TEST-1.1 through TEST-1.9 |
| **F2: Task Completion** | PATCH /api/tasks/:id; completed boolean toggle; optimistic visual update; revert on failure; order unchanged | SPEC-API (PATCH /api/tasks/:id); SPEC-COMPONENTS (taskController.js UPDATE SQL); SPEC-DATA (completed BOOLEAN column); SPEC-STACK (better-sqlite3 sync writes) | US-2.1, US-2.2, US-2.3, US-2.4 | TEST-2.1 through TEST-2.8 |
| **F3: Task Deletion** | DELETE /api/tasks/:id; optimistic removal; 200/204 response; revert on failure; empty-state on last delete | SPEC-API (DELETE /api/tasks/:id); SPEC-COMPONENTS (taskController.js DELETE SQL); SPEC-ARCH (no soft-delete, hard delete only) | US-3.1, US-3.2, US-3.3 | TEST-3.1 through TEST-3.7 |
| **F4: Task Persistence** | Durable SQLite storage; CREATE TABLE IF NOT EXISTS on startup; write-before-response; fatal exit on init failure; session restoration via GET /api/tasks | SPEC-DATA (DDL, SQLite file); SPEC-COMPONENTS (db.js singleton + schema init); SPEC-STARTUP (startup sequence, process.exit(1)); SPEC-STACK (better-sqlite3 sync I/O) | US-4.1, US-4.2, US-4.3, US-4.4 | TEST-4.1 through TEST-4.7 |

### 3.2 User Story to PRD Feature Reverse Traceability

| User Story | Story Title | PRD Feature | FRD Area |
|---|---|---|---|
| US-0.1 | Add a Task by Pressing Enter | F0 | POST /api/tasks; Enter key submission |
| US-0.2 | Add a Task by Clicking the Add Button | F0 | POST /api/tasks; button submission |
| US-0.3 | Prevent Empty Task Submission | F0 | title validation (TITLE_EMPTY) |
| US-0.4 | Enforce Task Title Character Limit | F0 | title validation (TITLE_TOO_LONG) |
| US-0.5 | Handle Task Creation Backend Failure | F0 | Error states: 500/503; error revert |
| US-1.1 | View All Tasks on Page Load | F1 | GET /api/tasks; render task list |
| US-1.2 | See Empty State When No Tasks Exist | F1 | Empty array → placeholder message |
| US-1.3 | View Completed and Active Tasks Together | F1 | Unified list; visual differentiation |
| US-1.4 | List Reflects Changes Without Page Reload | F1 | In-memory list state updates |
| US-1.5 | Handle Task List Load Failure | F1 | Error states: 500/503/PARSE_ERROR |
| US-2.1 | Mark a Task as Complete | F2 | PATCH /api/tasks/:id; completed: true |
| US-2.2 | Unmark a Completed Task | F2 | PATCH /api/tasks/:id; completed: false |
| US-2.3 | See Visual Distinction Between Active and Completed Tasks | F2 | Visual differentiator (strikethrough/muted) |
| US-2.4 | Handle Task Completion Backend Failure | F2 | Error states: 404/500/503; revert |
| US-3.1 | Delete a Task | F3 | DELETE /api/tasks/:id; optimistic removal |
| US-3.2 | See Empty State After Deleting Last Task | F3 | Empty-state after last task deleted |
| US-3.3 | Handle Task Deletion Backend Failure | F3 | Error states: 404/500/503; revert |
| US-4.1 | Tasks Survive a Browser Refresh | F4 | GET /api/tasks on reload; no localStorage |
| US-4.2 | Tasks Survive a Server Restart | F4 | Durable storage; SQLite file persistence |
| US-4.3 | Backend Auto-Initializes Storage on First Run | F4 | CREATE TABLE IF NOT EXISTS on startup |
| US-4.4 | All Task Writes Are Committed Before Success Response | F4 | Write-before-response contract |

### 3.3 API Endpoint to Feature Traceability

| API Endpoint | HTTP Method | PRD Feature | FRD Section | TechArch Section |
|---|---|---|---|---|
| `/api/tasks` | GET | F1, F4 | Task List View; Task Persistence | §4.4 GET /api/tasks; §3.2 DDL |
| `/api/tasks` | POST | F0 | Task Creation | §4.4 POST /api/tasks |
| `/api/tasks/:id` | PATCH | F2 | Task Completion | §4.4 PATCH /api/tasks/:id |
| `/api/tasks/:id` | DELETE | F3 | Task Deletion | §4.4 DELETE /api/tasks/:id |

---

## 4. Requirements Detail

### 4.1 F0: Task Creation

**PRD Capabilities (PRD-TodoApp.md §5 F0):**
- Text input field for task title/description
- Submit action via button click or Enter key
- Task saved to backend on submission
- New task appears in list immediately after creation

**FRD Functional Requirements (FRD-TodoApp.md §F0):**
- `title` must be non-empty string, not whitespace-only (trim before checking), ≤ 500 characters
- Frontend validates before any API call; submission blocked and no API call made on validation failure
- Optimistic update: pending task appended to bottom of list immediately; input cleared; focus returned to input
- `POST /api/tasks` sent with `{ "title": "<user input>" }`
- Backend returns `201 Created` with task object (`id`, `title`, `completed: false`, `created_at`)
- On failure: pending task removed, user's text restored to input, inline error shown
- Error codes: `TITLE_EMPTY` (400), `TITLE_TOO_LONG` (400), `SERVER_UNAVAILABLE` (503), `INTERNAL_ERROR` (500)

**TechArch Implementation (TechArch-TodoApp.md):**
- `controllers/taskController.js`: validates `title` (trim + length check); executes `INSERT INTO tasks (title) VALUES (?)` via parameterized query; returns `201` with created record
- `routes/tasks.js`: maps `POST /api/tasks` to controller
- `db.js`: `better-sqlite3` synchronous write guarantees commit before function returns
- `public/app.js`: client-side validation; optimistic DOM append; `fetch()` to POST endpoint; revert on failure

**User Stories:**
- US-0.1 — Add a Task by Pressing Enter
- US-0.2 — Add a Task by Clicking the Add Button
- US-0.3 — Prevent Empty Task Submission
- US-0.4 — Enforce Task Title Character Limit
- US-0.5 — Handle Task Creation Backend Failure

---

### 4.2 F1: Task List View

**PRD Capabilities (PRD-TodoApp.md §5 F1):**
- Display all tasks fetched from the backend on page load
- Show task title and completion status for each item
- List updates in real-time after create, complete, or delete actions

**FRD Functional Requirements (FRD-TodoApp.md §F1):**
- `GET /api/tasks` sent automatically on page load; no user action required
- Backend returns tasks ordered by `created_at` ascending (oldest first); positions unchanged on completion toggle
- Each task item renders: title, completion indicator, delete button
- Empty-state message shown (e.g., "No tasks yet. Add one above!") when array is empty
- In-memory list state updated after every CRUD action; no full page reload
- Response must be valid JSON array; each task object must contain `id`, `title`, `completed`
- Error codes: `SERVER_UNAVAILABLE` (503), `PARSE_ERROR` (client-side), `INTERNAL_ERROR` (500)

**TechArch Implementation (TechArch-TodoApp.md):**
- `controllers/taskController.js`: executes `SELECT id, title, completed, created_at FROM tasks ORDER BY created_at ASC`
- `db.js`: `idx_tasks_created_at` index optimizes ORDER BY performance
- `public/app.js`: on load, calls `GET /api/tasks`; iterates response array; renders DOM list items; shows empty-state if array length is 0; updates in-memory list state after each action

**User Stories:**
- US-1.1 — View All Tasks on Page Load
- US-1.2 — See Empty State When No Tasks Exist
- US-1.3 — View Completed and Active Tasks Together
- US-1.4 — List Reflects Changes Without Page Reload
- US-1.5 — Handle Task List Load Failure

---

### 4.3 F2: Task Completion

**PRD Capabilities (PRD-TodoApp.md §5 F2):**
- Toggle or checkbox to mark a task complete/incomplete
- Visual indicator distinguishing completed tasks (strikethrough, muted style)
- Completion state persisted to backend immediately on toggle
- Completed tasks remain visible in list (not auto-deleted)

**FRD Functional Requirements (FRD-TodoApp.md §F2):**
- `PATCH /api/tasks/:id` sent with `{ "completed": <new boolean> }`
- Frontend determines new state by inverting current `completed` value
- Optimistic UI: visual state updated immediately before API response
- On success: `200 OK` with updated task object; frontend confirms rendered state matches
- On failure: visual state reverted; inline error shown near task item
- `id` must correspond to existing task; `completed` must be boolean
- Error codes: `TASK_NOT_FOUND` (404), `INVALID_VALUE` (400), `SERVER_UNAVAILABLE` (503), `INTERNAL_ERROR` (500)
- Task order unchanged on completion toggle (position determined by `created_at`)

**TechArch Implementation (TechArch-TodoApp.md):**
- `controllers/taskController.js`: type-checks `completed` (`typeof completed === 'boolean'`); executes `UPDATE tasks SET completed = ? WHERE id = ?`; returns updated record
- `routes/tasks.js`: maps `PATCH /api/tasks/:id` to controller
- `public/app.js`: inverts current `completed` flag; optimistic CSS class toggle; `fetch()` PATCH; reverts class on failure

**User Stories:**
- US-2.1 — Mark a Task as Complete
- US-2.2 — Unmark a Completed Task
- US-2.3 — See Visual Distinction Between Active and Completed Tasks
- US-2.4 — Handle Task Completion Backend Failure

---

### 4.4 F3: Task Deletion

**PRD Capabilities (PRD-TodoApp.md §5 F3):**
- Delete action available per task (button or icon)
- Task removed from backend on action
- Task removed from list view immediately after deletion
- No confirmation dialog in v1

**FRD Functional Requirements (FRD-TodoApp.md §F3):**
- `DELETE /api/tasks/:id` sent on single click; no confirmation dialog
- Optimistic removal: task item removed from rendered list immediately
- Backend returns `200 OK` or `204 No Content` on success; permanently removes record (no soft-delete)
- On failure: task item restored to list; inline error shown near task item
- Empty-state message shown if deleted task was the last one
- Error codes: `TASK_NOT_FOUND` (404), `SERVER_UNAVAILABLE` (503), `INTERNAL_ERROR` (500)

**TechArch Implementation (TechArch-TodoApp.md):**
- `controllers/taskController.js`: validates `:id` exists; executes `DELETE FROM tasks WHERE id = ?`; returns `204 No Content`
- `routes/tasks.js`: maps `DELETE /api/tasks/:id` to controller
- `public/app.js`: optimistic DOM removal; `fetch()` DELETE; restores DOM on failure; checks list length for empty-state display

**User Stories:**
- US-3.1 — Delete a Task
- US-3.2 — See Empty State After Deleting Last Task
- US-3.3 — Handle Task Deletion Backend Failure

---

### 4.5 F4: Task Persistence

**PRD Capabilities (PRD-TodoApp.md §5 F4):**
- All tasks stored server-side (not localStorage/sessionStorage alone)
- Full task list restored on page load via API fetch
- Task state (title, completion) preserved accurately across sessions

**FRD Functional Requirements (FRD-TodoApp.md §F4):**
- Durable server-side storage (SQLite, PostgreSQL, file-based JSON, etc.) — in-memory state explicitly disallowed
- Backend initializes storage on startup (`CREATE TABLE IF NOT EXISTS`); if init fails, log fatal error and exit — no in-memory fallback
- All writes committed synchronously before success response returned
- Frontend calls `GET /api/tasks` on every page load; does not use localStorage/sessionStorage as source of truth
- All fields (`id`, `title`, `completed`, `created_at`) must be present and correctly typed after round-trip read
- Error codes: `STORAGE_INIT_FAILED` (startup fatal), `STORAGE_WRITE_ERROR` (500), `STORAGE_READ_ERROR` (500)

**TechArch Implementation (TechArch-TodoApp.md):**
- `db.js`: opens SQLite connection to `$DATABASE_URL` (default `./data/todo.db`); runs `CREATE TABLE IF NOT EXISTS tasks (...)` synchronously; calls `process.exit(1)` on failure
- `better-sqlite3` synchronous I/O: write committed before function returns; no async flush needed
- `server.js`: startup sequence — DB init → middleware → routes → `app.listen(PORT)`; fatal exit if DB init fails
- `data/todo.db`: SQLite file persisted on host filesystem; gitignored; survives process restarts

**User Stories:**
- US-4.1 — Tasks Survive a Browser Refresh
- US-4.2 — Tasks Survive a Server Restart
- US-4.3 — Backend Auto-Initializes Storage on First Run
- US-4.4 — All Task Writes Are Committed Before Success Response

---

## 5. Test Case Coverage

### 5.1 Test Case Matrix

> Test cases are derived directly from User Story acceptance criteria. Each test case maps to one or more acceptance criteria checkboxes in UserStories-TodoApp.md.

| Test ID | Test Description | User Story | PRD Feature | Type | Expected Result |
|---|---|---|---|---|---|
| TEST-0.1 | Submit task via Enter key — task appears in list | US-0.1 | F0 | E2E | Task appended to list bottom; input cleared; no page reload |
| TEST-0.2 | Submit task via Add button — task appears in list | US-0.2 | F0 | E2E | Task appended to list bottom; input cleared; button accessible ≤2 clicks |
| TEST-0.3 | Submit empty input — no task created, error shown | US-0.3 | F0 | Unit/E2E | No API call; "Task title cannot be empty." shown inline; input remains focused |
| TEST-0.4 | Submit whitespace-only input — treated as empty | US-0.3 | F0 | Unit | No API call; same error as empty |
| TEST-0.5 | Submit title >500 characters — rejected with error | US-0.4 | F0 | Unit/E2E | Frontend blocks submission; "Task title must be 500 characters or fewer." shown |
| TEST-0.6 | Backend returns 400 TITLE_TOO_LONG — error shown | US-0.4 | F0 | Integration | Same error message displayed; user text preserved in input |
| TEST-0.7 | Backend returns 503 on POST — error shown, input preserved | US-0.5 | F0 | Integration | "Unable to save task. Please try again." shown; task not added to list |
| TEST-0.8 | Backend returns 500 on POST — error shown, input preserved | US-0.5 | F0 | Integration | "Something went wrong. Please try again." shown; task not added to list |
| TEST-0.9 | Error message auto-dismisses after 5 seconds | US-0.3, US-0.5 | F0 | E2E | Error message disappears after ~5s without user action |
| TEST-0.10 | POST /api/tasks — 201 with valid task object returned | US-0.1, US-0.2 | F0 | API | Response contains `id`, `title`, `completed: false`, `created_at` |
| TEST-1.1 | Page load triggers GET /api/tasks automatically | US-1.1 | F1 | E2E | API called on load; no user action required |
| TEST-1.2 | Tasks rendered in created_at ASC order | US-1.1 | F1 | E2E | Oldest task first; task title and completion indicator visible per item |
| TEST-1.3 | Each task item shows title, completion indicator, delete button | US-1.1 | F1 | E2E | All three elements visible per task row |
| TEST-1.4 | Empty array response → empty-state placeholder shown | US-1.2 | F1 | E2E | Placeholder message displayed in task list area; not shown when tasks exist |
| TEST-1.5 | Completed and active tasks visible in same unified list | US-1.3 | F1 | E2E | Both states present; completed tasks visually differentiated |
| TEST-1.6 | New task appears in list without page reload | US-1.4 | F1 | E2E | Task appears immediately after creation action |
| TEST-1.7 | List loads within 1 second (local dev environment) | US-1.1 | F1 | Performance | Task list rendered within 1s baseline on localhost |
| TEST-1.8 | Backend 503 on load → "Unable to load tasks..." shown | US-1.5 | F1 | Integration | Error message in list area; no empty list rendered |
| TEST-1.9 | Backend 500 on load → "Something went wrong loading your tasks." shown | US-1.5 | F1 | Integration | Error message displayed correctly |
| TEST-2.1 | Click completion toggle on active task — visual state changes | US-2.1 | F2 | E2E | Strikethrough/muted style applied immediately; PATCH sent |
| TEST-2.2 | PATCH /api/tasks/:id with completed:true — 200 + updated object | US-2.1 | F2 | API | completed field is true in response |
| TEST-2.3 | Click completion toggle on completed task — visual state reverts | US-2.2 | F2 | E2E | Normal style restored; PATCH sent with completed:false |
| TEST-2.4 | Toggle same task twice — returns to original state | US-2.2 | F2 | E2E | Idempotent round-trip; final state matches initial state |
| TEST-2.5 | Completed task does not move in list after toggle | US-2.1 | F2 | E2E | Task remains at its original created_at position |
| TEST-2.6 | PATCH returns 404 — visual state reverts, "Task not found..." shown | US-2.4 | F2 | Integration | Pre-toggle state restored; error message near task item |
| TEST-2.7 | PATCH returns 503 — visual state reverts, "Unable to update task..." shown | US-2.4 | F2 | Integration | Revert + error message displayed |
| TEST-2.8 | PATCH returns 500 — visual state reverts, "Something went wrong..." shown | US-2.4 | F2 | Integration | Revert + error message displayed |
| TEST-3.1 | Click delete button — task removed from list immediately (no dialog) | US-3.1 | F3 | E2E | Task disappears from DOM on single click; no confirmation dialog |
| TEST-3.2 | DELETE /api/tasks/:id — 204 No Content returned | US-3.1 | F3 | API | Response is 204; task not present in subsequent GET |
| TEST-3.3 | Delete last task — empty-state placeholder shown immediately | US-3.2 | F3 | E2E | Empty-state message appears without page reload |
| TEST-3.4 | Add task after deleting last — empty-state disappears | US-3.2 | F3 | E2E | Placeholder hidden when at least one task exists |
| TEST-3.5 | DELETE returns 503 — task restored, "Unable to delete task..." shown | US-3.3 | F3 | Integration | Task reappears in list; error message near item |
| TEST-3.6 | DELETE returns 500 — task restored, "Something went wrong..." shown | US-3.3 | F3 | Integration | Task reappears; error shown |
| TEST-3.7 | DELETE returns 404 — "Task not found. It may have already been deleted." shown | US-3.3 | F3 | Integration | Error shown; no additional state change needed |
| TEST-4.1 | Tasks present after browser hard-refresh | US-4.1 | F4 | E2E | All tasks with correct titles and completion states present post-reload |
| TEST-4.2 | Tasks fetched from API on reload, not from localStorage | US-4.1 | F4 | E2E | localStorage not used as source of truth; API is called |
| TEST-4.3 | Tasks present after server process restart | US-4.2 | F4 | E2E | GET /api/tasks returns all tasks after restart |
| TEST-4.4 | Backend auto-creates DB/table on first startup | US-4.3 | F4 | Integration | First run succeeds; empty GET /api/tasks returns [] |
| TEST-4.5 | Storage init failure causes fatal exit (no degraded start) | US-4.3 | F4 | Integration | server.js logs fatal error and exits; does not serve requests |
| TEST-4.6 | POST success only returned after write committed | US-4.4 | F4 | Integration | 201 not returned until INSERT committed; process restart confirms data present |
| TEST-4.7 | Storage write failure returns 500 STORAGE_WRITE_ERROR | US-4.4 | F4 | Integration | 500 returned; "Unable to save changes. Please try again." shown |

### 5.2 Coverage Summary by Feature

| PRD Feature | User Stories | Test Cases | Story Coverage | Test Coverage |
|---|---|---|---|---|
| F0: Task Creation | US-0.1, US-0.2, US-0.3, US-0.4, US-0.5 | TEST-0.1 – TEST-0.10 | 5 / 5 (100%) | 10 cases |
| F1: Task List View | US-1.1, US-1.2, US-1.3, US-1.4, US-1.5 | TEST-1.1 – TEST-1.9 | 5 / 5 (100%) | 9 cases |
| F2: Task Completion | US-2.1, US-2.2, US-2.3, US-2.4 | TEST-2.1 – TEST-2.8 | 4 / 4 (100%) | 8 cases |
| F3: Task Deletion | US-3.1, US-3.2, US-3.3 | TEST-3.1 – TEST-3.7 | 3 / 3 (100%) | 7 cases |
| F4: Task Persistence | US-4.1, US-4.2, US-4.3, US-4.4 | TEST-4.1 – TEST-4.7 | 4 / 4 (100%) | 7 cases |
| **TOTAL** | **21 stories** | **41 test cases** | **21 / 21 (100%)** | **41 cases** |

### 5.3 Test Type Distribution

| Test Type | Count | Features Covered |
|---|---|---|
| End-to-End (E2E) | 23 | F0, F1, F2, F3, F4 |
| Integration | 14 | F0, F1, F2, F3, F4 |
| API | 3 | F0, F2, F3 |
| Performance | 1 | F1 |
| Unit | 2 | F0 |
| **Total** | **41** | All features |

### 5.4 Error Code Coverage

| Error Code | HTTP Status | Triggered By | Test Case |
|---|---|---|---|
| `TITLE_EMPTY` | 400 / client | POST /api/tasks (empty title) | TEST-0.3, TEST-0.4 |
| `TITLE_TOO_LONG` | 400 | POST /api/tasks (title >500 chars) | TEST-0.5, TEST-0.6 |
| `TASK_NOT_FOUND` | 404 | PATCH or DELETE (non-existent ID) | TEST-2.6, TEST-3.7 |
| `INVALID_VALUE` | 400 | PATCH (non-boolean completed) | TEST-2.2 (implicit) |
| `STORAGE_WRITE_ERROR` | 500 | POST, PATCH, DELETE write failure | TEST-4.7 |
| `STORAGE_READ_ERROR` | 500 | GET /api/tasks read failure | TEST-1.9 (implicit) |
| `INTERNAL_ERROR` | 500 | Unhandled exception | TEST-0.8, TEST-1.9, TEST-2.8, TEST-3.6 |
| `SERVER_UNAVAILABLE` | 503 | Backend unreachable | TEST-0.7, TEST-1.8, TEST-2.7, TEST-3.5 |
| `STORAGE_INIT_FAILED` | — (startup) | DB init failure on startup | TEST-4.5 |

---

## 6. Change Management

### 6.1 Baseline Statement

This RTM establishes the v1.0 baseline for TodoApp as of 2026-05-07. All five PRD features (F0–F4) are included in scope. All requirements are P0 (MVP-critical). No deferred or conditional requirements exist in this baseline.

### 6.2 Out-of-Scope Items (Confirmed for v1)

The following items are explicitly excluded from this RTM and will not appear in the traceability matrix. Any future inclusion requires a formal change request and PRD update:

- User accounts and authentication
- Multi-user support or collaboration features
- Real-time sync (WebSockets, live updates across clients)
- Mobile native application
- Task categories, tags, or priority levels
- Due dates, reminders, or notifications
- Soft-delete or task archiving
- PostgreSQL migration (path defined in TechArch §3.5 but not implemented in v1)

### 6.3 Change Log

| Version | Date | Author | Description | Affected Sections |
|---|---|---|---|---|
| 1.0 | 2026-05-07 | — | Initial RTM baseline established from PRD v1.0, FRD v1.0, TechArch v1.0, UserStories v1.0 | All |

### 6.4 Change Request Process

Any modification to a traced requirement must follow this process:
1. Identify the originating document (PRD/FRD/TechArch/UserStories) and the affected requirement ID
2. Assess downstream impact using Section 3 traceability links
3. Update all affected spec documents to maintain consistency
4. Update this RTM to reflect the change
5. Log the change in Section 6.3 with version, date, author, and description
6. Obtain approval from stakeholders listed in Section 7

---

## 7. Approval

### 7.1 Sign-Off Table

| Role | Name | Signature | Date | Status |
|---|---|---|---|---|
| Product Owner | — | | | Pending |
| Engineering Lead | — | | | Pending |
| QA Lead | — | | | Pending |
| Project Sponsor | — | | | Pending |

### 7.2 Approval Notes

- All approvers confirm that the requirements listed in this RTM are complete, correct, and traceable to the agreed project scope defined in PROJECT.md
- Approval of this RTM authorizes development to proceed against the F0–F4 feature set at v1.0 scope
- Any change to a traced requirement after approval requires a new RTM version and re-approval by affected roles

---

## Appendix A: Document Reference Index

| Document | File | Version | Status |
|---|---|---|---|
| Project Description | `.planning/PROJECT.md` | — | Active |
| Product Requirements Document | `project_specs/PRD-TodoApp.md` | 1.0 | Draft |
| Functional Requirements Document | `project_specs/FRD-TodoApp.md` | 1.0 | Draft |
| Technical Architecture Document | `project_specs/TechArch-TodoApp.md` | 1.0 | Draft |
| User Stories | `project_specs/UserStories-TodoApp.md` | 1.0 | Draft |
| Requirements Traceability Matrix | `project_specs/RTM-TodoApp.md` | 1.0 | Draft |

## Appendix B: ID Convention Reference

| Prefix | Level | Format | Example | Source Document |
|---|---|---|---|---|
| F | PRD Feature | F{n} | F0, F1, F2 | PRD-TodoApp.md §5, §9 |
| US | User Story | US-{epic}.{n} | US-0.1, US-1.3 | UserStories-TodoApp.md |
| TEST | Test Case | TEST-{epic}.{n} | TEST-0.1, TEST-3.5 | RTM (this document) |
| NFR | Non-Functional Req | NFR-{CATEGORY} | NFR-PERF, NFR-RELIABILITY | PRD-TodoApp.md §6 |
| SPEC | TechArch Spec Area | SPEC-{NAME} | SPEC-API, SPEC-DATA | TechArch-TodoApp.md |
| Error Codes | API Error | ALL_CAPS | TITLE_EMPTY, TASK_NOT_FOUND | FRD-TodoApp.md Error Handling; TechArch-TodoApp.md §4.6 |

---

*Document generated: 2026-05-07*
*Related documents: PRD-TodoApp.md, FRD-TodoApp.md, TechArch-TodoApp.md, UserStories-TodoApp.md*

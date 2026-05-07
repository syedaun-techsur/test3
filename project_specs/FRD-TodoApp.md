# Functional Requirements Document
## Basic To-Do App (TodoApp)

**Version:** 1.0
**Date:** 2026-05-07
**Status:** Draft
**Related:** PRD-TodoApp.md, PROJECT.md

---

## Table of Contents

1. [Feature Specifications](#feature-specifications)
   - [F0: Task Creation](#f0-task-creation)
   - [F1: Task List View](#f1-task-list-view)
   - [F2: Task Completion](#f2-task-completion)
   - [F3: Task Deletion](#f3-task-deletion)
   - [F4: Task Persistence](#f4-task-persistence)
2. [API Endpoints](#api-endpoints)
3. [Database Schema](#database-schema)
4. [Error Handling](#error-handling)
5. [Integration Points](#integration-points)

---

## Feature Specifications

---

### F0: Task Creation

**Description:** This feature allows a user to add a new task by entering a text title and submitting it. On submission, the task is sent to the backend via a REST API call, persisted to the database, and the new task immediately appears in the task list — all without requiring a page reload. This is the entry point for all task data in the system.

**Terminology:**
- **Task Title:** The text string entered by the user that describes the task to be done
- **Submission:** The action of confirming task creation, triggered by pressing Enter or clicking the Add button
- **Optimistic Update:** Appending the new task to the visible list immediately before the API response returns (required; consistent with F2 and F3 behavior)

**Sub-features:**
- Text input field for entering a task title
- Submit via button click (Add / "+" button)
- Submit via Enter key on the keyboard
- POST request to backend on submission
- Newly created task appended to the bottom of the task list immediately (optimistic update — displayed before API response returns)
- Input field cleared after successful submission

**Process:**
1. User focuses on the task input field
2. User types a task title (non-empty string)
3. User submits via Enter key or Add button click
4. Frontend validates that input is not empty or whitespace-only
5. Frontend immediately appends a pending task item to the task list (optimistic update) and clears the input field and returns focus to it
6. Frontend sends `POST /api/tasks` with `{ "title": "<user input>" }` to the backend
7. Backend validates the request body
8. Backend creates a new task record with `id`, `title`, `completed: false`, and `created_at` timestamp
9. Backend returns `201 Created` with the created task object
10. Frontend replaces the pending task item with the confirmed task object (updating with the server-assigned `id` and `created_at`)
11. If the API call fails, frontend removes the pending task item and shows an inline error; the typed text is restored to the input field

**Inputs:**
- `title` (string, required): The task description text entered by the user; sourced from the text input field

**Outputs:**
- New task object added to the backend store
- Task rendered in the list view with title and an unchecked completion indicator
- Input field reset to empty

**Validation:**
- `title` must be a non-empty string
- `title` must not be whitespace-only (trim before checking)
- `title` must not exceed 500 characters
- Submission is blocked and no API call is made if validation fails on the frontend

**Error States:**

| Scenario | HTTP Status | Error Code | User-Facing Message |
|---|---|---|---|
| Empty or whitespace title submitted | — (client-side) | TITLE_EMPTY | "Task title cannot be empty." |
| Title exceeds 500 characters | 400 | TITLE_TOO_LONG | "Task title must be 500 characters or fewer." |
| Backend unavailable | 503 | SERVER_UNAVAILABLE | "Unable to save task. Please try again." |
| Unexpected server error | 500 | INTERNAL_ERROR | "Something went wrong. Please try again." |

---

### F1: Task List View

**Description:** This feature displays all existing tasks to the user in a single, ordered list. When the page loads, the frontend fetches all tasks from the backend and renders them in the order they were created. The list automatically reflects the current state after any create, complete, or delete action without requiring a full page reload.

**Terminology:**
- **Task Item:** A single rendered row in the list representing one task record, showing its title and completion status
- **Active Task:** A task whose `completed` field is `false`
- **Completed Task:** A task whose `completed` field is `true`, rendered with a distinct visual style
- **List State:** The in-memory representation of all tasks currently displayed on the frontend
- **Sort Order:** Tasks are ordered by `created_at` ascending (oldest first) and maintain their position in the list regardless of completion state changes — toggling a task's completion does not reorder the list

**Sub-features:**
- Fetch all tasks from the backend on initial page load
- Render each task as a list item with title and completion indicator
- Display active and completed tasks in the same unified list
- Reflect create, complete, and delete changes immediately in the list without reload
- Display an empty-state message when no tasks exist

**Process:**
1. User opens or refreshes the application in a browser
2. Frontend sends `GET /api/tasks` to the backend
3. Backend retrieves all task records from the database, ordered by `created_at` ascending
4. Backend returns `200 OK` with an array of task objects
5. Frontend iterates over the array and renders each task as a list item
6. Each list item displays: task title, completion checkbox/toggle, and delete button
7. Completed tasks are rendered with a visual differentiator (e.g., strikethrough text, muted color)
8. If the array is empty, a placeholder message is shown (e.g., "No tasks yet. Add one above!")
9. List state is updated in-memory after each subsequent create, complete, or delete action; task positions in the list do not change when completion state is toggled — tasks remain at their original `created_at` position

**Inputs:**
- No user input required for initial load; triggered automatically on page open
- Implicit input: existing task records stored in the backend database

**Outputs:**
- Rendered list of all task items in the browser
- Empty-state message if no tasks exist
- Each task item shows: title, completion status indicator, delete action

**Validation:**
- The API response must be a valid JSON array (may be empty)
- Each task object must contain at minimum: `id`, `title`, `completed`
- Frontend handles missing optional fields gracefully (no crash on unexpected shape)

**Error States:**

| Scenario | HTTP Status | Error Code | User-Facing Message |
|---|---|---|---|
| Backend unavailable on load | 503 | SERVER_UNAVAILABLE | "Unable to load tasks. Please refresh the page." |
| Malformed response from server | — (client-side parse error) | PARSE_ERROR | "Unable to display tasks. Please refresh the page." |
| Unexpected server error | 500 | INTERNAL_ERROR | "Something went wrong loading your tasks." |

---

### F2: Task Completion

**Description:** This feature allows a user to toggle the completion state of any task. When a task is marked complete, it is visually differentiated from active tasks (e.g., strikethrough title, muted styling) and its updated status is immediately persisted to the backend. The toggle is reversible — a completed task can be marked incomplete again.

**Terminology:**
- **Completion Toggle:** The interactive element (checkbox or button) that switches a task's `completed` state
- **Completed State:** `completed: true` — task is done; rendered with visual differentiation
- **Active State:** `completed: false` — task is pending; rendered normally
- **Idempotent Toggle:** Toggling the same state twice returns the task to its original state

**Sub-features:**
- Checkbox or toggle button per task item
- Click/tap toggles `completed` between `true` and `false`
- Immediate visual update on toggle (before or upon API response)
- PATCH request sent to backend to persist new completion state
- Visual style change: completed tasks show strikethrough text and/or muted appearance
- Completed tasks remain visible in the list (not hidden or auto-deleted)

**Process:**
1. User clicks the checkbox/toggle on a task item
2. Frontend determines the new state: if `completed` was `false`, new state is `true`; and vice versa
3. Frontend immediately updates the visual state of the task item (optimistic UI)
4. Frontend sends `PATCH /api/tasks/:id` with `{ "completed": <new boolean> }` to the backend
5. Backend validates the request and updates the task record in the database
6. Backend returns `200 OK` with the updated task object
7. Frontend confirms the rendered state matches the returned object
8. If the API call fails, frontend reverts the visual change and shows an error message

**Inputs:**
- `id` (string/integer, required): The unique identifier of the task being toggled; sourced from the task item in the list
- `completed` (boolean, required): The new completion state derived by inverting the current value

**Outputs:**
- Task record updated in the backend database
- Task item re-rendered with appropriate visual style (strikethrough for completed, normal for active)

**Validation:**
- `id` must correspond to an existing task record
- `completed` must be a boolean (`true` or `false`)
- If `id` does not exist, return `404` — do not create a new record

**Error States:**

| Scenario | HTTP Status | Error Code | User-Facing Message |
|---|---|---|---|
| Task ID not found | 404 | TASK_NOT_FOUND | "Task not found. Please refresh the page." |
| Invalid `completed` value (non-boolean) | 400 | INVALID_VALUE | "Invalid request. Please try again." |
| Backend unavailable | 503 | SERVER_UNAVAILABLE | "Unable to update task. Please try again." |
| Unexpected server error | 500 | INTERNAL_ERROR | "Something went wrong. Please try again." |

---

### F3: Task Deletion

**Description:** This feature allows a user to permanently delete a task from the system. When the delete action is triggered on a task item, the task is immediately removed from the list view and a DELETE request is sent to the backend to purge the record from the database. There is no confirmation dialog in v1 — deletion is immediate and irreversible.

**Terminology:**
- **Delete Action:** The interactive element (button or icon) per task item that triggers permanent removal
- **Hard Delete:** The task record is permanently removed from the database; no soft-delete or archive in v1
- **Immediate Removal:** The task disappears from the list as soon as the delete action is triggered

**Sub-features:**
- Delete button or icon rendered per task item
- Single click/tap triggers deletion with no confirmation dialog
- Task removed from the list view immediately on action
- DELETE request sent to backend to remove the record
- If deletion fails, task is restored to the list and an error is shown

**Process:**
1. User clicks the delete button/icon on a task item
2. Frontend immediately removes the task item from the rendered list (optimistic removal)
3. Frontend sends `DELETE /api/tasks/:id` to the backend
4. Backend validates the `id` and deletes the corresponding record from the database
5. Backend returns `200 OK` or `204 No Content`
6. Frontend confirms deletion is complete (no further action needed on success)
7. If the API call fails, frontend restores the task item in the list and shows an error message

**Inputs:**
- `id` (string/integer, required): The unique identifier of the task to delete; sourced from the task item

**Outputs:**
- Task record permanently removed from the backend database
- Task item removed from the rendered list view
- List count decremented by one
- Empty-state message shown if the deleted task was the last one

**Validation:**
- `id` must correspond to an existing task record
- If `id` does not exist, backend returns `404`; frontend shows an error but no state change needed (item was already removed visually)

**Error States:**

| Scenario | HTTP Status | Error Code | User-Facing Message |
|---|---|---|---|
| Task ID not found | 404 | TASK_NOT_FOUND | "Task not found. It may have already been deleted." |
| Backend unavailable | 503 | SERVER_UNAVAILABLE | "Unable to delete task. Please try again." |
| Unexpected server error | 500 | INTERNAL_ERROR | "Something went wrong. Please try again." |

---

### F4: Task Persistence

**Description:** This feature ensures that all task data is durably stored on the server side so that the full task list — including titles and completion states — is fully restored whenever the user opens, refreshes, or revisits the application. Tasks must survive both browser refreshes and server restarts, meaning in-memory-only storage is not acceptable.

**Terminology:**
- **Durable Storage:** A persistence mechanism that retains data across process restarts (e.g., a file-based database like SQLite or a full RDBMS)
- **Session Restoration:** The act of re-fetching and re-rendering the complete task list from the backend on page load
- **In-memory State:** Temporary data held in runtime memory only; lost on server restart — explicitly disallowed for task data

**Sub-features:**
- All task records stored in a server-side durable store (SQLite, PostgreSQL, file-based JSON, etc.)
- Complete task list fetched from backend on every page load (no reliance on browser localStorage or sessionStorage as the source of truth)
- Task title and completion state both preserved accurately across restarts
- Backend initializes storage on startup if it does not yet exist (e.g., creates DB file / table on first run)

**Process:**
1. On first run, backend initializes the database/storage file and creates the tasks table/collection if it does not exist
2. All create, update, and delete operations write synchronously (or with immediate commit) to durable storage
3. On page load, frontend calls `GET /api/tasks` to retrieve the full persisted task list
4. Backend reads all records from durable storage and returns them
5. Frontend renders the list from the API response — not from any browser-side cache
6. On server restart, backend reconnects to the same durable storage and serves the existing data unchanged

**Inputs:**
- No direct user input — this is an infrastructural feature underpinning F0–F3
- Implicit dependency: every write from F0, F2, F3 must route through durable storage

**Outputs:**
- All task data restored accurately on page load after any kind of session interruption
- Backend startup log confirms storage is initialized and connected

**Validation:**
- Storage layer must confirm successful write before returning a success response to the frontend
- Backend must handle storage initialization failure at startup with a clear error log (do not silently continue with in-memory fallback)
- All task fields (`id`, `title`, `completed`, `created_at`) must be present and correctly typed after a round-trip read

**Error States:**

| Scenario | HTTP Status | Error Code | User-Facing / Log Message |
|---|---|---|---|
| Storage unavailable on startup | — (startup failure) | STORAGE_INIT_FAILED | Log: "FATAL: Cannot connect to storage. Exiting." |
| Write fails during task create/update/delete | 500 | STORAGE_WRITE_ERROR | "Unable to save changes. Please try again." |
| Read fails during task list fetch | 500 | STORAGE_READ_ERROR | "Unable to load tasks. Please refresh the page." |

---

## API Endpoints

All endpoints are prefixed with `/api`. Requests and responses use `Content-Type: application/json`. No authentication is required.

| Method | Endpoint | Description | Request Body | Success Response |
|---|---|---|---|---|
| `GET` | `/api/tasks` | Retrieve all tasks | None | `200 OK` — array of task objects |
| `POST` | `/api/tasks` | Create a new task | `{ "title": string }` | `201 Created` — created task object |
| `PATCH` | `/api/tasks/:id` | Update task completion state | `{ "completed": boolean }` | `200 OK` — updated task object |
| `DELETE` | `/api/tasks/:id` | Permanently delete a task | None | `200 OK` or `204 No Content` |

---

### Request & Response Schemas

**Task Object (returned by all endpoints):**
```json
{
  "id": 1,
  "title": "Buy groceries",
  "completed": false,
  "created_at": "2026-05-07T10:00:00.000Z"
}
```

**`GET /api/tasks` — Response:**
```json
[
  { "id": 1, "title": "Buy groceries", "completed": false, "created_at": "2026-05-07T10:00:00.000Z" },
  { "id": 2, "title": "Write report", "completed": true, "created_at": "2026-05-07T10:05:00.000Z" }
]
```
Returns an empty array `[]` when no tasks exist. Never returns `null`.

**`POST /api/tasks` — Request Body:**
```json
{ "title": "Buy groceries" }
```

**`PATCH /api/tasks/:id` — Request Body:**
```json
{ "completed": true }
```

**Error Response (all endpoints):**
```json
{
  "error": {
    "code": "TASK_NOT_FOUND",
    "message": "Task not found."
  }
}
```

---

## Database Schema

The application uses a single `tasks` table. SQLite is the recommended default for simplicity; the schema is compatible with PostgreSQL and MySQL with no modification.

```sql
CREATE TABLE IF NOT EXISTS tasks (
    id          INTEGER       PRIMARY KEY AUTOINCREMENT,
    title       VARCHAR(500)  NOT NULL,
    completed   BOOLEAN       NOT NULL DEFAULT FALSE,
    created_at  DATETIME      NOT NULL DEFAULT CURRENT_TIMESTAMP
);
```

**Column Definitions:**

| Column | Type | Constraints | Description |
|---|---|---|---|
| `id` | INTEGER | PRIMARY KEY, AUTOINCREMENT | Unique task identifier; auto-assigned on insert |
| `title` | VARCHAR(500) | NOT NULL | The task text entered by the user; max 500 characters |
| `completed` | BOOLEAN | NOT NULL, DEFAULT FALSE | Completion state; `false` = active, `true` = done |
| `created_at` | DATETIME | NOT NULL, DEFAULT CURRENT_TIMESTAMP | Timestamp of task creation; used for default sort order |

**Indexes:**

```sql
-- Default primary key index covers lookups by id (GET by id, PATCH, DELETE).
-- created_at index recommended if task list grows large.
CREATE INDEX IF NOT EXISTS idx_tasks_created_at ON tasks (created_at ASC);
```

**Notes:**
- No foreign keys, joins, or multi-table relationships required for v1
- Database file (SQLite) or connection string (Postgres) should be configurable via environment variable (e.g., `DATABASE_URL`)
- Backend must execute `CREATE TABLE IF NOT EXISTS` on startup to auto-initialize the schema

---

## Error Handling

### Global Error Behavior

- All API errors return a JSON body in the format `{ "error": { "code": string, "message": string } }`
- The frontend always displays a user-facing inline message on error — never silently swallows failures
- On optimistic UI updates (task creation, completion toggle, task deletion), if the API call fails the frontend must revert the visual change (remove the pending task for creation failures; restore the prior state for completion and deletion failures)

### Error Code Reference

| Error Code | HTTP Status | Trigger | User-Facing Message |
|---|---|---|---|
| `TITLE_EMPTY` | 400 | `POST /api/tasks` with empty/whitespace title | "Task title cannot be empty." |
| `TITLE_TOO_LONG` | 400 | `POST /api/tasks` with title > 500 chars | "Task title must be 500 characters or fewer." |
| `TASK_NOT_FOUND` | 404 | `PATCH` or `DELETE` with non-existent ID | "Task not found. Please refresh the page." |
| `INVALID_VALUE` | 400 | `PATCH` with non-boolean `completed` value | "Invalid request. Please try again." |
| `STORAGE_WRITE_ERROR` | 500 | Database write failure | "Unable to save changes. Please try again." |
| `STORAGE_READ_ERROR` | 500 | Database read failure on `GET /api/tasks` | "Unable to load tasks. Please refresh the page." |
| `INTERNAL_ERROR` | 500 | Unhandled server exception | "Something went wrong. Please try again." |
| `SERVER_UNAVAILABLE` | 503 | Backend unreachable from frontend | "Server unavailable. Please try again later." |

### Frontend Error Display Rules
- Inline error messages appear near the relevant UI element (e.g., below the input field for creation errors, near the task item for update/delete errors)
- Each task item manages its own error state independently — if multiple task operations fail simultaneously, each failing task item displays its own inline error message near its own row; messages do not aggregate or replace each other
- Error messages auto-dismiss after 5 seconds or on next user interaction
- Failed optimistic updates revert the UI state before showing the error message

---

## Integration Points

### Frontend ↔ Backend
- The frontend communicates with the backend exclusively via the REST API defined above
- No direct database access from the frontend
- The frontend must handle all defined error responses gracefully without crashing

### Backend ↔ Database
- The backend is the sole writer and reader of the database
- All task mutations (create, update, delete) must be committed to durable storage before the backend returns a success response to the frontend
- Connection pooling is not required for v1 (single-user / low concurrency)

### Backend Startup Dependencies
- Backend initializes the database connection and runs `CREATE TABLE IF NOT EXISTS` on startup
- If the database cannot be reached or initialized, the backend must log a fatal error and exit — it must not start in a degraded state

### Frontend Page Load Dependency
- On every page load, the frontend must call `GET /api/tasks` before rendering the task list
- The frontend must not cache task data in `localStorage` or `sessionStorage` as the primary source of truth — the backend API is always authoritative

---

*Document generated: 2026-05-07*
*Related documents: PRD-TodoApp.md, TechArch-TodoApp.md, UserStories-TodoApp.md*

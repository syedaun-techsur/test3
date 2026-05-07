# Technical Architecture Document
## Basic To-Do App (TodoApp)

**Version:** 1.0
**Date:** 2026-05-07
**Status:** Draft
**Related:** PRD-TodoApp.md, FRD-TodoApp.md

---

## Table of Contents

1. [Architectural Overview](#1-architectural-overview)
2. [Component Architecture](#2-component-architecture)
3. [Data Model](#3-data-model)
4. [API Design](#4-api-design)
5. [Security Architecture](#5-security-architecture)
6. [Technology Stack](#6-technology-stack)
7. [Integration Points](#7-integration-points)

---

## 1. Architectural Overview

### 1.1 Architecture Pattern

TodoApp follows a **classic three-tier web architecture**: a static frontend served by the backend (or a CDN), a RESTful API layer, and a durable SQLite database. This pattern is chosen deliberately for its simplicity, low operational overhead, and zero external service dependencies — all aligned with the project's core constraint of "no over-engineering."

There is no build pipeline, no microservices, no message queue. The backend is a single Node.js/Express process that owns both API routing and database access. The frontend is vanilla HTML/CSS/JavaScript served as static files.

### 1.2 System Architecture Diagram

```
  Browser
  ┌──────────────────────────────────────────┐
  │              Frontend (SPA)              │
  │  ┌────────────┐   ┌────────────────────┐ │
  │  │  Task UI   │   │   API Client       │ │
  │  │  (HTML/CSS)│◄──│   (fetch / XHR)   │ │
  │  └────────────┘   └────────┬───────────┘ │
  └───────────────────────────┼──────────────┘
                               │  HTTP/REST
                               │  JSON
  Server                       ▼
  ┌──────────────────────────────────────────┐
  │           Backend (Node.js/Express)      │
  │  ┌─────────────┐   ┌──────────────────┐ │
  │  │  API Router │   │  Task Controller │ │
  │  │  /api/tasks │──►│  (CRUD logic)    │ │
  │  └─────────────┘   └────────┬─────────┘ │
  │                             │            │
  │  ┌──────────────────────────▼──────────┐ │
  │  │         Database Layer (SQLite)     │ │
  │  │         better-sqlite3 driver       │ │
  │  └──────────────────────────┬──────────┘ │
  └─────────────────────────────┼────────────┘
                                │
  Filesystem                    ▼
  ┌──────────────────────────────────────────┐
  │           todo.db  (SQLite file)         │
  │           Persisted on host filesystem   │
  └──────────────────────────────────────────┘
```

### 1.3 Deployment Topology

TodoApp is deployed as a single process on a single host (VPS, container, or local machine). There is no separate frontend server — Express serves the static HTML/CSS/JS files directly from a `public/` directory alongside the API routes.

```
  Host / Container
  ┌─────────────────────────────────────────┐
  │  Process: node server.js                │
  │  Port: 3000 (configurable via PORT env) │
  │                                         │
  │  /public  → static frontend assets     │
  │  /api/*   → REST API routes            │
  │  /data/todo.db → SQLite database file  │
  └─────────────────────────────────────────┘
```

**Environment variables:**

| Variable | Default | Purpose |
|---|---|---|
| `PORT` | `3000` | HTTP server port |
| `DATABASE_URL` | `./data/todo.db` | Path to SQLite database file |
| `NODE_ENV` | `development` | Runtime environment flag |

### 1.4 Key Architectural Decisions

| Decision | Choice | Rationale |
|---|---|---|
| Single-process deployment | Express monolith | Minimal ops overhead; no auth or concurrency concerns for v1 |
| Database engine | SQLite | Zero infrastructure; durable file-based persistence; easy to migrate to Postgres later |
| Frontend delivery | Static files via Express | No separate build step or CDN needed; keeps the stack unified |
| Frontend framework | Vanilla JS | No build toolchain required; fast startup; matches "no over-engineering" constraint |
| ORM / query style | Raw SQL via `better-sqlite3` | Simple schema; avoids ORM abstraction overhead for 1-table app |
| API style | REST (JSON) | Well-understood, tooling-friendly, no special client libraries needed |

---

## 2. Component Architecture

### 2.1 Backend Components

```
  server/
  ├── server.js           ← Entry point: initializes DB, mounts routes, starts HTTP server
  ├── db.js               ← Database connection singleton; runs CREATE TABLE IF NOT EXISTS on load
  ├── routes/
  │   └── tasks.js        ← Express router: maps HTTP methods + paths to controller functions
  ├── controllers/
  │   └── taskController.js  ← Business logic: validates inputs, calls DB, formats responses
  └── public/             ← Static frontend assets served by Express
      ├── index.html
      ├── style.css
      └── app.js
```

**Component responsibilities:**

| Component | Responsibility |
|---|---|
| `server.js` | Bootstrap: connect DB, register middleware, mount routes, bind port |
| `db.js` | Open SQLite connection; run schema migration on startup; export query interface |
| `routes/tasks.js` | Define route handlers for `GET /api/tasks`, `POST /api/tasks`, `PATCH /api/tasks/:id`, `DELETE /api/tasks/:id` |
| `controllers/taskController.js` | Validate request inputs; execute DB queries; format success/error JSON responses |

### 2.2 Frontend Components

```
  public/
  ├── index.html     ← Shell: task input form + task list container
  ├── style.css      ← Minimal styles: list layout, completed state (strikethrough), empty state
  └── app.js         ← All frontend logic: API calls, DOM rendering, event handling
```

**Frontend module responsibilities:**

| Module | Responsibility |
|---|---|
| `index.html` | Static structure: input field, Add button, `<ul>` task list container, error message slot |
| `style.css` | Visual styles for active vs. completed tasks, empty state, error messages |
| `app.js` | On load: `GET /api/tasks` and render list. Event handlers for add, toggle, delete. Optimistic UI updates with revert on failure. |

### 2.3 Request Lifecycle

```
  User Action (e.g., click "Add")
        │
        ▼
  app.js — validate input client-side
        │
        ▼
  app.js — optimistic DOM update (optional)
        │
        ▼
  fetch() → POST /api/tasks   ──────────────►  routes/tasks.js
                                               │
                                               ▼
                                         taskController.js
                                               │  validates body
                                               │  runs INSERT SQL
                                               ▼
                                           db.js → SQLite
                                               │
                                               ▼
                                         201 + task object
        ◄──────────────────────────────────────┘
        │
  app.js — append task to DOM / clear input
```

---

## 3. Data Model

### 3.1 Entity Overview

TodoApp has a single entity: **Task**. There are no relationships, foreign keys, or multi-table joins in v1.

```
  ┌──────────────────────────────┐
  │            tasks             │
  ├──────────────────────────────┤
  │  id          INTEGER (PK)    │
  │  title       VARCHAR(500)    │
  │  completed   BOOLEAN         │
  │  created_at  DATETIME        │
  └──────────────────────────────┘
```

### 3.2 Complete DDL

```sql
-- ============================================================
-- TodoApp Database Schema
-- Target: SQLite (compatible with PostgreSQL / MySQL)
-- Run on every backend startup via CREATE TABLE IF NOT EXISTS
-- ============================================================

CREATE TABLE IF NOT EXISTS tasks (
    id          INTEGER       PRIMARY KEY AUTOINCREMENT,
    title       VARCHAR(500)  NOT NULL,
    completed   BOOLEAN       NOT NULL DEFAULT FALSE,
    created_at  DATETIME      NOT NULL DEFAULT CURRENT_TIMESTAMP
);

-- Index: default sort order for GET /api/tasks (ORDER BY created_at ASC)
CREATE INDEX IF NOT EXISTS idx_tasks_created_at
    ON tasks (created_at ASC);
```

### 3.3 Column Definitions

| Column | Type | Constraints | Description |
|---|---|---|---|
| `id` | `INTEGER` | `PRIMARY KEY AUTOINCREMENT` | Unique auto-incrementing task identifier; used in all URL path params (`:id`) |
| `title` | `VARCHAR(500)` | `NOT NULL` | Task description text; max 500 characters enforced at API layer and DB layer |
| `completed` | `BOOLEAN` | `NOT NULL DEFAULT FALSE` | Task completion state; `false` = active, `true` = done |
| `created_at` | `DATETIME` | `NOT NULL DEFAULT CURRENT_TIMESTAMP` | UTC timestamp of task creation; used for ascending sort order in list view |

### 3.4 Index Strategy

| Index Name | Columns | Type | Purpose |
|---|---|---|---|
| *(implicit)* | `id` | PRIMARY KEY | Covers all lookups by `id`: `PATCH /api/tasks/:id`, `DELETE /api/tasks/:id` |
| `idx_tasks_created_at` | `created_at ASC` | Standard | Optimizes `ORDER BY created_at ASC` on `GET /api/tasks`; important as task count grows |

### 3.5 PostgreSQL-Compatible DDL (optional migration path)

```sql
-- Drop AUTOINCREMENT syntax for PostgreSQL; use SERIAL or GENERATED ALWAYS AS IDENTITY

CREATE TABLE IF NOT EXISTS tasks (
    id          SERIAL        PRIMARY KEY,
    title       VARCHAR(500)  NOT NULL,
    completed   BOOLEAN       NOT NULL DEFAULT FALSE,
    created_at  TIMESTAMPTZ   NOT NULL DEFAULT NOW()
);

CREATE INDEX IF NOT EXISTS idx_tasks_created_at
    ON tasks (created_at ASC);
```

---

## 4. API Design

### 4.1 API Conventions

- **Base path:** `/api`
- **Content-Type:** `application/json` (all requests and responses)
- **Authentication:** None (v1 is auth-free)
- **ID type:** Integer (matches SQLite `AUTOINCREMENT`)
- **Timestamps:** ISO 8601 UTC strings (e.g., `"2026-05-07T10:00:00.000Z"`)
- **Empty list:** `GET /api/tasks` returns `[]`, never `null`
- **Error shape:** All errors return `{ "error": { "code": string, "message": string } }`

### 4.2 Endpoint Summary

| Method | Endpoint | Description | Auth | Body | Success |
|---|---|---|---|---|---|
| `GET` | `/api/tasks` | Retrieve all tasks ordered by `created_at` ASC | None | — | `200` array |
| `POST` | `/api/tasks` | Create a new task | None | `{ title }` | `201` task object |
| `PATCH` | `/api/tasks/:id` | Toggle task completion state | None | `{ completed }` | `200` task object |
| `DELETE` | `/api/tasks/:id` | Permanently delete a task | None | — | `204` |

### 4.3 TypeScript Interfaces

```typescript
// ============================================================
// Core Domain Types
// ============================================================

/** A task as stored in the database and returned by all API endpoints */
interface Task {
  id: number;
  title: string;
  completed: boolean;
  created_at: string; // ISO 8601 UTC, e.g. "2026-05-07T10:00:00.000Z"
}

// ============================================================
// Request Bodies
// ============================================================

/** POST /api/tasks — request body */
interface CreateTaskRequest {
  title: string; // Required; 1–500 chars; whitespace-only not allowed
}

/** PATCH /api/tasks/:id — request body */
interface UpdateTaskRequest {
  completed: boolean; // Required; must be a boolean
}

// ============================================================
// Response Shapes
// ============================================================

/** GET /api/tasks — 200 OK */
type GetTasksResponse = Task[];

/** POST /api/tasks — 201 Created */
type CreateTaskResponse = Task;

/** PATCH /api/tasks/:id — 200 OK */
type UpdateTaskResponse = Task;

/** DELETE /api/tasks/:id — 204 No Content (empty body) */
type DeleteTaskResponse = void;

// ============================================================
// Error Response
// ============================================================

/** Returned by all endpoints on failure */
interface ApiErrorResponse {
  error: {
    code: ErrorCode;
    message: string;
  };
}

type ErrorCode =
  | "TITLE_EMPTY"
  | "TITLE_TOO_LONG"
  | "TASK_NOT_FOUND"
  | "INVALID_VALUE"
  | "STORAGE_WRITE_ERROR"
  | "STORAGE_READ_ERROR"
  | "INTERNAL_ERROR"
  | "SERVER_UNAVAILABLE";
```

### 4.4 Endpoint Specifications

---

#### `GET /api/tasks`

Retrieves all task records from the database ordered by `created_at` ascending (oldest first).

**Request:** No body, no query parameters.

**Response `200 OK`:**
```json
[
  { "id": 1, "title": "Buy groceries", "completed": false, "created_at": "2026-05-07T10:00:00.000Z" },
  { "id": 2, "title": "Write report",  "completed": true,  "created_at": "2026-05-07T10:05:00.000Z" }
]
```
Returns `[]` when no tasks exist.

**SQL executed:**
```sql
SELECT id, title, completed, created_at
FROM tasks
ORDER BY created_at ASC;
```

**Error responses:**

| Condition | Status | Code |
|---|---|---|
| Database read failure | `500` | `STORAGE_READ_ERROR` |
| Unhandled exception | `500` | `INTERNAL_ERROR` |

---

#### `POST /api/tasks`

Creates a new task. The `completed` field is always set to `false` on creation; the `created_at` timestamp is set by the database.

**Request body:**
```json
{ "title": "Buy groceries" }
```

**Validation (backend):**
- `title` must be present and a non-empty string after trimming
- `title` must be ≤ 500 characters

**Response `201 Created`:**
```json
{ "id": 3, "title": "Buy groceries", "completed": false, "created_at": "2026-05-07T11:00:00.000Z" }
```

**SQL executed:**
```sql
INSERT INTO tasks (title) VALUES (?);
-- then:
SELECT id, title, completed, created_at FROM tasks WHERE id = last_insert_rowid();
```

**Error responses:**

| Condition | Status | Code |
|---|---|---|
| `title` missing / empty / whitespace | `400` | `TITLE_EMPTY` |
| `title` > 500 characters | `400` | `TITLE_TOO_LONG` |
| Database write failure | `500` | `STORAGE_WRITE_ERROR` |
| Unhandled exception | `500` | `INTERNAL_ERROR` |

---

#### `PATCH /api/tasks/:id`

Updates the `completed` state of an existing task. Only the `completed` field is writable via this endpoint.

**Path parameter:** `id` — integer task ID

**Request body:**
```json
{ "completed": true }
```

**Validation (backend):**
- `:id` must correspond to an existing task (returns `404` if not found)
- `completed` must be a boolean value

**Response `200 OK`:**
```json
{ "id": 1, "title": "Buy groceries", "completed": true, "created_at": "2026-05-07T10:00:00.000Z" }
```

**SQL executed:**
```sql
UPDATE tasks SET completed = ? WHERE id = ?;
-- then:
SELECT id, title, completed, created_at FROM tasks WHERE id = ?;
```

**Error responses:**

| Condition | Status | Code |
|---|---|---|
| Task ID not found | `404` | `TASK_NOT_FOUND` |
| `completed` missing or not boolean | `400` | `INVALID_VALUE` |
| Database write failure | `500` | `STORAGE_WRITE_ERROR` |
| Unhandled exception | `500` | `INTERNAL_ERROR` |

---

#### `DELETE /api/tasks/:id`

Permanently removes a task record from the database. No soft delete.

**Path parameter:** `id` — integer task ID

**Request:** No body.

**Response `204 No Content`:** Empty body on success.

**SQL executed:**
```sql
DELETE FROM tasks WHERE id = ?;
```

**Error responses:**

| Condition | Status | Code |
|---|---|---|
| Task ID not found | `404` | `TASK_NOT_FOUND` |
| Database write failure | `500` | `STORAGE_WRITE_ERROR` |
| Unhandled exception | `500` | `INTERNAL_ERROR` |

---

### 4.5 Error Response Format

All error responses share this envelope regardless of status code:

```json
{
  "error": {
    "code": "TASK_NOT_FOUND",
    "message": "Task not found."
  }
}
```

### 4.6 Full Error Code Reference

| Error Code | HTTP Status | Endpoint(s) | Trigger |
|---|---|---|---|
| `TITLE_EMPTY` | `400` | `POST /api/tasks` | `title` is missing, empty, or whitespace-only |
| `TITLE_TOO_LONG` | `400` | `POST /api/tasks` | `title` exceeds 500 characters |
| `TASK_NOT_FOUND` | `404` | `PATCH`, `DELETE` | `:id` does not match any task record |
| `INVALID_VALUE` | `400` | `PATCH /api/tasks/:id` | `completed` is not a boolean |
| `STORAGE_WRITE_ERROR` | `500` | `POST`, `PATCH`, `DELETE` | SQLite write/commit failure |
| `STORAGE_READ_ERROR` | `500` | `GET /api/tasks` | SQLite read failure |
| `INTERNAL_ERROR` | `500` | All | Unhandled exception caught by global error handler |
| `SERVER_UNAVAILABLE` | `503` | All | Backend process unreachable (network / crash) |

---

## 5. Security Architecture

### 5.1 Authentication & Authorization

TodoApp v1 has **no authentication layer by design**. This is an explicit product decision documented in the PRD: the application is single-user or shared-state with zero auth friction. There are no user accounts, sessions, JWTs, or API keys.

This is acceptable for v1 because:
- The application is intended for single-user or trusted local/private deployment
- No sensitive personal data is stored beyond task text
- Complexity of auth is explicitly out of scope

**v2 consideration:** If multi-user or public deployment is required, add a session-based or JWT auth layer at the Express middleware level before the task routes.

### 5.2 Input Validation & Injection Prevention

All user input is validated at two layers:

**Frontend (client-side — UX only):**
- `title` must not be empty or whitespace-only before `POST` is sent
- Prevents unnecessary API calls; does not substitute for backend validation

**Backend (authoritative):**
- `title` is trimmed and length-checked before any SQL execution
- `completed` is type-checked (`typeof completed === 'boolean'`) before any SQL execution
- All SQL is executed via **parameterized queries** (prepared statements) — no string interpolation
- SQLite's `better-sqlite3` driver enforces parameterized queries by API design

```typescript
// Safe — parameterized:
db.prepare("INSERT INTO tasks (title) VALUES (?)").run(title);

// Never done — unsafe:
db.exec(`INSERT INTO tasks (title) VALUES ('${title}')`); // ❌
```

### 5.3 HTTP Security Headers

Express middleware should apply baseline security headers on all responses:

| Header | Value | Purpose |
|---|---|---|
| `X-Content-Type-Options` | `nosniff` | Prevent MIME-type sniffing |
| `X-Frame-Options` | `DENY` | Prevent clickjacking via iframes |
| `Content-Security-Policy` | `default-src 'self'` | Restrict resource loading to same origin |
| `Referrer-Policy` | `no-referrer` | No referrer header on cross-origin requests |

These are applied via the `helmet` npm package (one-liner middleware).

### 5.4 CORS Policy

For v1 (frontend and backend served from the same Express process on the same origin), no CORS configuration is needed. If the frontend is ever separated to a different origin, a permissive same-origin CORS policy should be added explicitly rather than using a wildcard.

### 5.5 Data Protection

- **At rest:** Task data is stored in a local SQLite file. No encryption at rest in v1. File-level permissions on the host OS should restrict access to the server process user.
- **In transit:** HTTPS/TLS should be terminated at a reverse proxy (e.g., nginx) in any public deployment. The Express app itself speaks plain HTTP on the loopback interface.
- **Data sensitivity:** Task titles are user-generated text. No personally identifiable information (PII) is explicitly collected or required.

### 5.6 Error Information Disclosure

- Backend error responses return structured `{ error: { code, message } }` objects with user-safe messages
- Raw stack traces, SQL errors, and file paths are **never** included in API responses
- Full error details are logged server-side only (to stdout/stderr or a log file)

---

## 6. Technology Stack

### 6.1 Full Stack Summary

| Layer | Technology | Version | Purpose |
|---|---|---|---|
| **Runtime** | Node.js | 20 LTS | Server-side JavaScript runtime |
| **API Framework** | Express.js | 4.x | HTTP server, routing, middleware |
| **Database** | SQLite | 3.x | Durable file-based relational database |
| **DB Driver** | better-sqlite3 | 9.x | Synchronous SQLite bindings for Node.js |
| **Security Headers** | helmet | 7.x | Express middleware for HTTP security headers |
| **Frontend** | Vanilla HTML/CSS/JS | — | No framework; no build step required |
| **Package Manager** | npm | 10.x | Dependency management |

### 6.2 Why These Choices

**Node.js + Express:** Minimal setup, broad ecosystem, aligns with "standard web technologies." Express adds only the routing and middleware primitives needed — no magic.

**SQLite + better-sqlite3:** SQLite is a file-based database with zero server process overhead. `better-sqlite3` uses synchronous I/O, which simplifies error handling in a single-process app with no concurrency concerns. The database file is created automatically if it doesn't exist, satisfying the F4 auto-initialization requirement.

**Vanilla JS frontend:** No build toolchain (no webpack, no Babel, no TypeScript compilation step at runtime). The frontend is a single `app.js` file using the native `fetch` API. This is the simplest possible frontend that satisfies all feature requirements.

**helmet:** One-line middleware that sets a well-known set of defensive HTTP headers. Zero configuration needed for v1.

### 6.3 Development Dependencies

| Tool | Purpose |
|---|---|
| `nodemon` | Auto-restart server on file change during development |
| `jest` + `supertest` | Unit and integration testing for API routes |

### 6.4 Project Structure

```
todoapp/
├── package.json
├── package-lock.json
├── .env.example          ← Sample environment variables
├── .gitignore            ← Excludes node_modules/, data/, .env
├── server.js             ← Entry point
├── db.js                 ← SQLite connection + schema init
├── routes/
│   └── tasks.js          ← /api/tasks route definitions
├── controllers/
│   └── taskController.js ← CRUD business logic
├── public/
│   ├── index.html        ← Frontend shell
│   ├── style.css         ← UI styles
│   └── app.js            ← Frontend logic
└── data/
    └── todo.db           ← SQLite database file (gitignored)
```

---

## 7. Integration Points

### 7.1 Frontend ↔ Backend (REST API)

The frontend communicates with the backend exclusively via the four REST endpoints defined in Section 4. All communication is:
- HTTP/JSON over the same origin (no cross-origin requests in default deployment)
- Initiated by user actions or the page load event
- Handled with the native browser `fetch()` API
- No WebSockets, no polling, no server-sent events in v1

**Frontend API client pattern:**
```javascript
async function apiFetch(method, path, body = null) {
  const options = {
    method,
    headers: { "Content-Type": "application/json" },
  };
  if (body) options.body = JSON.stringify(body);
  const res = await fetch(`/api${path}`, options);
  if (res.status === 204) return null;
  return res.json(); // Always returns { data } or { error }
}
```

### 7.2 Backend ↔ SQLite Database

The backend is the sole reader and writer of the SQLite database. The integration contract:

- **Connection:** Opened once at startup via `better-sqlite3`; reused for all requests (no connection pooling needed)
- **Schema init:** `CREATE TABLE IF NOT EXISTS tasks (...)` runs synchronously on startup before the HTTP server binds to its port
- **Write confirmation:** `better-sqlite3` synchronous API guarantees the write is committed before the function returns — no async flush needed
- **Startup failure:** If the DB file cannot be opened or the schema cannot be created, `server.js` logs a fatal error and calls `process.exit(1)` — the server never starts in a degraded state

### 7.3 Static File Serving

Express serves the `public/` directory as static files on the root path (`/`). This means:
- `GET /` → `public/index.html`
- `GET /style.css` → `public/style.css`
- `GET /app.js` → `public/app.js`
- `GET /api/*` → handled by API router (takes precedence)

```javascript
// server.js
app.use(express.static(path.join(__dirname, "public")));
app.use("/api/tasks", taskRoutes);
```

### 7.4 External Services

TodoApp v1 has **no external service dependencies**. There are no:
- Third-party APIs
- Cloud storage providers
- Email/SMS services
- Analytics or monitoring services
- CDN or external asset hosts

All runtime dependencies are resolved at `npm install` and bundled locally.

### 7.5 Future Integration Considerations

The following integration points are designed to be straightforward to add in v2 without requiring significant rearchitecting:

| Future Need | Integration Path |
|---|---|
| User authentication | Add `express-session` + `passport.js` middleware; add `users` table; scope task queries by `user_id` |
| PostgreSQL migration | Swap `better-sqlite3` for `pg` driver; update DDL to PostgreSQL dialect (see Section 3.5) |
| Deployment behind nginx | Add nginx reverse proxy config; nginx handles TLS termination and static file caching |
| API rate limiting | Add `express-rate-limit` middleware to `/api` routes |
| Structured logging | Replace `console.log` with `pino` or `winston` |

---

## Appendix A: Startup Sequence

```
  node server.js
       │
       ▼
  Load environment variables (.env)
       │
       ▼
  db.js: open SQLite connection to $DATABASE_URL
       │
       ├─ failure → log "FATAL: Cannot connect to storage. Exiting." → process.exit(1)
       │
       ▼
  db.js: run CREATE TABLE IF NOT EXISTS tasks (...)
       │
       ├─ failure → log "FATAL: Schema init failed. Exiting." → process.exit(1)
       │
       ▼
  server.js: configure Express middleware (helmet, json parser, static files)
       │
       ▼
  server.js: mount /api/tasks router
       │
       ▼
  server.js: app.listen(PORT)
       │
       ▼
  Log: "TodoApp listening on http://localhost:3000"
```

---

## Appendix B: Data Flow — All CRUD Operations

| Operation | Trigger | Frontend Action | API Call | SQL | Response |
|---|---|---|---|---|---|
| **List** | Page load | Render task list | `GET /api/tasks` | `SELECT ... ORDER BY created_at ASC` | `200 []` |
| **Create** | Submit input | Append to DOM | `POST /api/tasks` | `INSERT INTO tasks (title)` | `201 task` |
| **Complete** | Click checkbox | Toggle style | `PATCH /api/tasks/:id` | `UPDATE tasks SET completed=?` | `200 task` |
| **Delete** | Click delete | Remove from DOM | `DELETE /api/tasks/:id` | `DELETE FROM tasks WHERE id=?` | `204` |

---

*Document generated: 2026-05-07*
*Related documents: PRD-TodoApp.md, FRD-TodoApp.md, UserStories-TodoApp.md*

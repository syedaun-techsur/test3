# Phase 1: Backend & Storage - Context

**Gathered:** 2026-05-07
**Status:** Ready for planning

<domain>
## Phase Boundary

REST API server with SQLite persistence — four CRUD endpoints (`GET /api/tasks`, `POST /api/tasks`, `PATCH /api/tasks/:id`, `DELETE /api/tasks/:id`) that handle tasks durably and correctly. No UI in this phase. Frontend is Phase 2.

</domain>

<decisions>
## Implementation Decisions

### Error Response Shape
- Error body format: `{ "error": "message string" }` — simple, flat, no nested envelope
- Validation errors (e.g., empty title): HTTP 400 with a specific message e.g. `{ "error": "Title is required" }`
- Missing resource (DELETE or PATCH on unknown ID): HTTP 404 Not Found
- Server errors (DB write failure, unexpected exception): HTTP 500 with generic `{ "error": "Internal server error" }` — never leak DB internals or stack traces to the client
- All server-side errors are logged to `console.error` for debugging; client always gets the safe generic message

### CORS
- Allow all origins in development (`Access-Control-Allow-Origin: *`) — frontend and backend may run on different ports
- CORS headers required on all API routes so the frontend can call from any dev port without errors

### Claude's Discretion
- SQLite file location and naming (e.g., `./tasks.db` or `./data/tasks.db`)
- Port default and `PORT` env var handling
- Startup behavior if DB cannot be created (error + exit vs retry)
- Table initialization on startup (`CREATE TABLE IF NOT EXISTS`)
- Exact error message wording beyond the validation case above

</decisions>

<specifics>
## Specific Ideas

No specific requirements beyond what was decided above — open to standard approaches for Express + SQLite setup.

</specifics>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Requirements & Roadmap
- `.planning/REQUIREMENTS.md` — PERS-01 (server-side persistence requirement), full v1 requirements list
- `.planning/ROADMAP.md` §Phase 1 — Goal, success criteria, and dependency notes for this phase

### Feature Specifications
- `project_specs/FRD-TodoApp.md` — Detailed functional specs for all CRUD operations (F0–F4), API endpoint contracts, request/response schemas, error codes, database schema DDL
- `project_specs/PRD-TodoApp.md` — Product requirements, feature IDs (F0–F4), NFRs (performance baseline)
- `project_specs/TechArch-TodoApp.md` — Technology stack decisions (Node.js 20 LTS, Express 4.x, SQLite 3.x, better-sqlite3), API TypeScript interfaces, DB DDL, security architecture (parameterized queries, helmet)

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets
- None — greenfield project, no existing code

### Established Patterns
- None established yet — this is Phase 1; patterns set here carry forward to Phase 2

### Integration Points
- Phase 2 (Frontend) will call this API; the endpoint contracts defined in FRD-TodoApp.md are the integration boundary

</code_context>

<deferred>
## Deferred Ideas

None — discussion stayed within phase scope.

</deferred>

---

*Phase: 01-backend-storage*
*Context gathered: 2026-05-07*

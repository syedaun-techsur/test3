# Roadmap: Basic To-Do App

## Overview

Two phases deliver a complete, working to-do application. Phase 1 builds the backend server with durable SQLite storage and a fully tested REST API. Phase 2 wires up the frontend UI to that API, delivering all four CRUD operations through a clean browser interface. After Phase 2, users can manage tasks reliably with no friction.

## Phases

**Phase Numbering:**
- Integer phases (1, 2, 3): Planned milestone work
- Decimal phases (2.1, 2.2): Urgent insertions (marked with INSERTED)

Decimal phases appear between their surrounding integers in numeric order.

- [ ] **Phase 1: Backend & Storage** - REST API server with SQLite persistence for all CRUD operations
- [ ] **Phase 2: Frontend & Integration** - Vanilla JS UI wired to the API; all task operations usable in browser

## Phase Details

### Phase 1: Backend & Storage
**Goal**: A working API server that persists tasks durably and correctly handles all CRUD operations
**Depends on**: Nothing (first phase)
**Requirements**: PERS-01
**Success Criteria** (what must be TRUE):
  1. `GET /api/tasks` returns a JSON array of all tasks ordered by creation time
  2. `POST /api/tasks` creates a task and returns the saved object with a server-assigned id
  3. `PATCH /api/tasks/:id` toggles a task's completed state and returns the updated object
  4. `DELETE /api/tasks/:id` removes a task and returns 204
  5. Tasks are fully restored (title + completion state) after a server restart
**Plans**: TBD

### Phase 2: Frontend & Integration
**Goal**: Users can create, view, complete, and delete tasks through a browser UI backed by the Phase 1 API
**Depends on**: Phase 1
**Requirements**: TASK-01, TASK-02, TASK-03, TASK-04
**Success Criteria** (what must be TRUE):
  1. User can type a task title and submit it; the new task appears in the list immediately without a page reload
  2. User sees all existing tasks on page load, ordered oldest-first, with each task's completion status visible
  3. User can click a checkbox/toggle on any task; it switches between active and completed styles instantly
  4. User can click a delete button on any task; it disappears from the list immediately
  5. After a hard browser refresh, the full task list (including completion states) is restored from the server
**Plans**: TBD

## Progress

**Execution Order:**
Phases execute in numeric order: 1 → 2

| Phase | Plans Complete | Status | Completed |
|-------|----------------|--------|-----------|
| 1. Backend & Storage | 0/TBD | Not started | - |
| 2. Frontend & Integration | 0/TBD | Not started | - |

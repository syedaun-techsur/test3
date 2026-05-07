# UX Mockup
## Basic To-Do App (TodoApp)

**Project:** TodoApp — Basic To-Do App
**Generated:** 2026-05-07
**Based on:** UserStories-TodoApp.md, PRD-TodoApp.md, FRD-TodoApp.md, JOURNEYS-TodoApp.md
**Version:** 1.0

---

## Overview

TodoApp is a single-screen application. There is exactly one view: the main task list page. All user interactions — adding, completing, and deleting tasks — happen inline on this page with no navigation, no modals, and no page reloads.

**Design Principles:**

- **Zero friction first.** The input field is the hero element. It is auto-focused on load so a user can start typing within 1 second of arriving (JRN-01.1, JRN-02.1).
- **Optimistic UI everywhere.** All mutations (create, complete, delete) update the list instantly. Backend sync happens in parallel. Failures revert the UI and surface a clear error message (US-0.5, US-2.4, US-3.3).
- **Visual clarity over density.** Completed tasks are visually muted so active tasks pop without any filtering or tab-switching (US-1.3, US-2.3).
- **Inline error communication.** Errors appear near the element that caused them, auto-dismiss after 5 seconds, and never block the user from acting (US-0.3, US-0.4, US-0.5).

---

## User Flows

### Flow 1: Add a Task

**Trigger:** User arrives at the app URL, or has just submitted a previous task.
**User Stories:** US-0.1, US-0.2, US-0.3, US-0.4, US-0.5
**Journeys:** JRN-01.1 (Capture stage), JRN-02.1 (First Capture + Rapid Re-Entry stages)

```
[Page Load — Input Auto-Focused]
         │
         ▼
[User Types Task Title in Input Field]
         │
         ├── Input is empty / whitespace-only ──▶ [Show inline error: "Task title cannot be empty."]
         │                                                  │
         │                                                  └── Error auto-dismisses after 5s or on next interaction
         │                                                      Input stays focused
         │
         ├── Input exceeds 500 characters ──▶ [Show inline error: "Task title must be 500 characters or fewer."]
         │                                             │
         │                                             └── User's text remains in input; no API call made
         │
         └── Input is valid (non-empty, ≤ 500 chars)
                   │
                   ▼
         [User presses Enter  OR  clicks Add button]
                   │
                   ▼
         [Task appended to bottom of list immediately (optimistic)]
         [Input cleared; focus returns to input field]
         [POST /api/tasks sent in background]
                   │
                   ├── 201 Created ──▶ [Task confirmed in list. No further UI change.]
                   │
                   ├── 503 ──▶ [Remove task from list] ──▶ [Show inline error: "Unable to save task. Please try again."]
                   │                                                 │
                   └── 500 ──▶ [Remove task from list] ──▶ [Show inline error: "Something went wrong. Please try again."]
                                                                     │
                                                              User's text restored to input field
```

**Steps:**
1. Input field is visible, labeled with placeholder text `Add a task…`, and auto-focused on page load.
2. User types a task title. No character counter is shown unless the limit is approached (optional progressive enhancement — out of scope for v1).
3. User submits via **Enter key** or the **Add (+) button** adjacent to the input.
4. Frontend validates: empty/whitespace → inline error below input; title > 500 chars → inline error below input. No API call is made.
5. On valid submission: new task row is immediately appended to the bottom of the task list; input is cleared; focus returns to the input for the next entry.
6. POST request fires in the background. On failure, the optimistically added row is removed, the user's text is restored to the input field, and an inline error message appears below the input.

---

### Flow 2: Load Task List on Page Open

**Trigger:** User opens or refreshes the app URL.
**User Stories:** US-1.1, US-1.2, US-1.5, US-4.1, US-4.2
**Journeys:** JRN-01.1 (Arrive + Orient), JRN-01.2 (Close & Return), JRN-02.1 (Pre-Meeting Setup), JRN-02.2 (Morning Load)

```
[User opens app URL]
         │
         ▼
[Page renders: Header + Input field (auto-focused) + Loading skeleton in list area]
[GET /api/tasks fires immediately]
         │
         ├── 200 OK, array has items ──▶ [Render all tasks in creation-time order (oldest first)]
         │                                        │
         │                                        └── [Exit: User sees full list, ready to act]
         │
         ├── 200 OK, empty array ──▶ [Render empty-state message: "No tasks yet. Add one above!"]
         │                                    │
         │                                    └── [Exit: User sees prompt to add first task]
         │
         ├── 503 ──▶ [Show error in list area: "Unable to load tasks. Please refresh the page."]
         │
         ├── 500 ──▶ [Show error in list area: "Something went wrong loading your tasks."]
         │
         └── Parse error ──▶ [Show error in list area: "Unable to display tasks. Please refresh the page."]
```

**Steps:**
1. Page renders immediately with the input field visible and focused.
2. A skeleton loading state (placeholder rows) appears in the task list area while `GET /api/tasks` is in-flight.
3. On success, skeleton is replaced by the real task list. Completed tasks render with muted/strikethrough style; active tasks render normally.
4. On empty array, the empty-state message replaces the skeleton.
5. On error, the error message replaces the skeleton in the list area — not as a page-level banner.

---

### Flow 3: Complete or Reopen a Task

**Trigger:** User clicks the checkbox/toggle on any task row.
**User Stories:** US-2.1, US-2.2, US-2.3, US-2.4
**Journeys:** JRN-01.2 (Complete stage), JRN-02.2 (Mark Complete stage)

```
[User clicks completion toggle on a task row]
         │
         ▼
[Task visual state flips immediately (optimistic)]
   Active → Completed: strikethrough text, muted color, checkbox checked
   Completed → Active: normal text, full opacity, checkbox unchecked
[PATCH /api/tasks/:id sent in background]
         │
         ├── 200 OK ──▶ [State confirmed. No further UI change.]
         │
         ├── 404 ──▶ [Revert task visual to pre-toggle state]
         │           [Show inline error near task: "Task not found. Please refresh the page."]
         │
         ├── 503 ──▶ [Revert task visual to pre-toggle state]
         │           [Show inline error near task: "Unable to update task. Please try again."]
         │
         └── 500 ──▶ [Revert task visual to pre-toggle state]
                     [Show inline error near task: "Something went wrong. Please try again."]
```

**Steps:**
1. Each task row has a checkbox (or toggle) on the left edge. It is large enough to hit comfortably (minimum 24×24px touch target).
2. A single click/tap immediately flips the visual state — no wait for the server.
3. PATCH fires in background. On success, nothing changes (already correct). On failure, the visual reverts and an inline error appears.
4. The task always remains in the list — it is never auto-hidden or auto-deleted on completion.

---

### Flow 4: Delete a Task

**Trigger:** User clicks the delete icon on any task row.
**User Stories:** US-3.1, US-3.2, US-3.3
**Journeys:** JRN-01.2 (Delete stage), JRN-02.2 (Delete Cancelled Task stage)

```
[User clicks delete icon on a task row]
         │
         ▼
[Task row removed from list immediately (optimistic)]
[List reflows smoothly; no jump or flash]
[DELETE /api/tasks/:id sent in background]
         │
         ├── 200 OK / 204 No Content ──▶ [Deletion confirmed. No further UI change.]
         │                                        │
         │                                        └── If deleted task was the last one:
         │                                            [Empty-state message appears: "No tasks yet. Add one above!"]
         │
         ├── 404 ──▶ [No state restoration needed (item already removed visually)]
         │           [Show inline error: "Task not found. It may have already been deleted."]
         │
         ├── 503 ──▶ [Restore task row to its original position in the list]
         │           [Show inline error near restored task: "Unable to delete task. Please try again."]
         │
         └── 500 ──▶ [Restore task row to its original position in the list]
                     [Show inline error near restored task: "Something went wrong. Please try again."]
```

**Steps:**
1. Each task row has a delete icon (×, trash icon, or similar) on the right edge, visible at all times or on hover (desktop).
2. A single click removes the row immediately — no confirmation dialog.
3. List smoothly reflows to fill the gap. If the list becomes empty, the empty-state message appears without a page reload.
4. DELETE fires in background. On 503 or 500 failure, the task is restored to its previous position in the list.

---

## Screen Design

### Screen: Main Task Page

**Purpose:** The single screen of the app. Handles task input, list display, and all inline interactions.
**User Stories:** All stories (US-0.1 through US-4.4)
**Journeys:** All journeys (JRN-01.1, JRN-01.2, JRN-02.1, JRN-02.2)

#### Layout

```
┌──────────────────────────────────────────────────────────────┐
│                         TodoApp                              │  ← App title / branding (minimal)
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌──────────────────────────────────────────┐  ┌─────────┐  │
│  │  Add a task…                             │  │   Add   │  │  ← Task input row (US-0.1, US-0.2)
│  └──────────────────────────────────────────┘  └─────────┘  │
│  ┌──────────────────────────────────────────────────────────┐│
│  │ ⚠ [Inline error message — appears here on validation     ││  ← Validation / API error zone (US-0.3–0.5)
│  │   or submission failure; hidden by default]              ││
│  └──────────────────────────────────────────────────────────┘│
│                                                              │
│  ── Task List ────────────────────────────────────────────── │
│                                                              │
│  ┌──────────────────────────────────────────────────────────┐│
│  │ ☐  Buy groceries                                    [×]  ││  ← Active task row
│  ├──────────────────────────────────────────────────────────┤│
│  │ ☑  ~~Write weekly report~~                          [×]  ││  ← Completed task row (muted + strikethrough)
│  ├──────────────────────────────────────────────────────────┤│
│  │ ☐  Follow up with venue re: date                    [×]  ││  ← Active task row
│  ├──────────────────────────────────────────────────────────┤│
│  │ ☑  ~~Send budget draft to board~~                   [×]  ││  ← Completed task row
│  └──────────────────────────────────────────────────────────┘│
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

**Empty state variant:**
```
┌──────────────────────────────────────────────────────────────┐
│                         TodoApp                              │
├──────────────────────────────────────────────────────────────┤
│  ┌──────────────────────────────────────────┐  ┌─────────┐  │
│  │  Add a task…                             │  │   Add   │  │
│  └──────────────────────────────────────────┘  └─────────┘  │
│                                                              │
│  ── Task List ────────────────────────────────────────────── │
│                                                              │
│            No tasks yet. Add one above!                      │  ← Empty state (US-1.2, US-3.2)
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

**Loading state variant (page open, GET /api/tasks in-flight):**
```
┌──────────────────────────────────────────────────────────────┐
│                         TodoApp                              │
├──────────────────────────────────────────────────────────────┤
│  ┌──────────────────────────────────────────┐  ┌─────────┐  │
│  │  Add a task…                             │  │   Add   │  │
│  └──────────────────────────────────────────┘  └─────────┘  │
│                                                              │
│  ── Task List ────────────────────────────────────────────── │
│                                                              │
│  ┌──────────────────────────────────────────────────────────┐│
│  │ ░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░││  ← Skeleton row
│  ├──────────────────────────────────────────────────────────┤│
│  │ ░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░││  ← Skeleton row
│  ├──────────────────────────────────────────────────────────┤│
│  │ ░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░││  ← Skeleton row
│  └──────────────────────────────────────────────────────────┘│
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

**List load error variant:**
```
│  ── Task List ────────────────────────────────────────────── │
│                                                              │
│  ⚠  Unable to load tasks. Please refresh the page.          │  ← Error in list area, not page banner (US-1.5)
│                                                              │
```

---

#### Information Hierarchy

| Priority | Content | Placement | Rationale |
|----------|---------|-----------|-----------|
| **Primary** | Task input field | Top of content area, full-width, auto-focused | Entry point for all data; Marcus and Priya's first interaction every session (JRN-01.1, JRN-02.1) |
| **Primary** | Add button | Adjacent (right) of input field | Always visible; required for mouse/touch users (US-0.2) |
| **Primary** | Active task titles | Task list rows, full-opacity, normal weight | What users need to act on today |
| **Secondary** | Completion checkboxes | Left edge of each task row | Functional affordance; accessible but not visually dominant |
| **Secondary** | Completed task titles | Task list rows, muted color + strikethrough | Done items; visible for reference but visually receded (US-2.3) |
| **Secondary** | Delete icons | Right edge of each task row | Available per-row; not visually dominant to avoid accidental deletion |
| **Tertiary** | Inline error messages | Below input field (creation errors) or near task row (update/delete errors) | Contextual, temporary; auto-dismiss (US-0.3–0.5, US-2.4, US-3.3) |
| **Tertiary** | Empty-state message | Center of list area | Only shown when list is empty; orienting, not critical (US-1.2) |
| **Tertiary** | App title / branding | Page header | Context only; not interactive |

---

#### Task Row Anatomy

```
┌──────────────────────────────────────────────────────────────────┐
│  [checkbox]  [task title text]                        [delete ×] │
│              └─ active: full opacity, normal text                │
│              └─ completed: muted opacity, strikethrough text     │
│  ─────────────────────────────────────────────────────────────── │
│  [inline error message — task-level, hidden by default]          │
└──────────────────────────────────────────────────────────────────┘
```

- **Checkbox:** Left-aligned. Minimum 24×24px. Checked state for completed tasks. Single click toggles.
- **Title text:** Takes up remaining horizontal space. Non-interactive (no edit in v1). Truncates with ellipsis if overflowing on narrow viewports.
- **Delete icon:** Right-aligned. Uses a recognizable icon (× or trash). Visible always on mobile; visible on hover on desktop.
- **Task-level inline error:** Appears below the task row when a completion toggle or delete action fails. Hidden when no error.

---

#### States

| State | Appearance | User Feedback | Relevant Stories |
|-------|------------|---------------|-----------------|
| **Page loading** | Input visible + focused; 3 skeleton rows in list area | No text — skeleton implies loading | US-1.1 |
| **List populated (default)** | Tasks rendered with correct active/completed styling | None — list is the feedback | US-1.1, US-1.3 |
| **List empty** | No task rows; centered message in list area | "No tasks yet. Add one above!" | US-1.2, US-3.2 |
| **List load error** | Error message in list area; no task rows shown | Error string (context-specific) | US-1.5 |
| **Input focused** | Input has visible focus ring; cursor inside | Placeholder text `Add a task…` | US-0.1 |
| **Input validation error** | Red/warning border on input; error message below | "Task title cannot be empty." or "Task title must be 500 characters or fewer." | US-0.3, US-0.4 |
| **Task creation in-flight** | Task row appears immediately (optimistic); Add button briefly disabled to prevent double-submit | New row visible; input cleared | US-0.1, US-0.2 |
| **Task creation failed** | Optimistic row removed; user's text restored to input | Inline error below input; auto-dismisses after 5s | US-0.5 |
| **Task active** | Full opacity text; unchecked checkbox | Normal appearance | US-1.3, US-2.2 |
| **Task completed** | Muted color (~50–60% opacity); strikethrough title; checked checkbox | Visual differentiation at a glance | US-1.3, US-2.1, US-2.3 |
| **Completion toggle in-flight** | Visual state flips immediately; checkbox not re-clickable until confirmed | Immediate visual response | US-2.1, US-2.2 |
| **Completion toggle failed** | Visual state reverts to pre-toggle appearance | Inline error near task row; auto-dismisses after 5s | US-2.4 |
| **Task delete in-flight** | Task row removed immediately; list reflows | Smooth row removal | US-3.1 |
| **Task deletion failed** | Task row restored to original position | Inline error near restored task row; auto-dismisses after 5s | US-3.3 |

---

#### Interactive Elements

| Element | Type | Behavior | Keyboard Support |
|---------|------|----------|-----------------|
| Task input field | Text input | Auto-focused on load; accepts typing; clears after successful submission | `Enter` submits |
| Add button | Primary button | Submits input on click; disabled briefly during in-flight create request | `Space` / `Enter` activates |
| Task checkbox/toggle | Checkbox | Single click toggles completion state; optimistic update | `Space` toggles when focused |
| Delete icon (×) | Icon button | Single click removes task; optimistic removal | `Enter` / `Space` activates when focused |
| Inline error message | Status text | Appears on validation failure or API error; auto-dismisses after 5s or on next user interaction | Non-interactive |

---

## Interaction Patterns

### Pattern 1: Optimistic UI Update

**When to use:** All task mutations — create (F0), complete/uncomplete (F2), delete (F3).
**Behavior:**
1. User action triggers immediate visual update (task added, toggled, or removed).
2. API call fires in parallel (background).
3. On API success: no further change (already correct).
4. On API failure: visual state reverts to pre-action state; inline error message shown.

**Rationale:** Keeps the UI feeling instant (< 1s feedback), which is critical for Priya's rapid meeting entry (JRN-02.1) and Marcus's single-action confidence (JRN-01.1, JRN-01.2).

**Examples:** Task creation (US-0.1, US-0.2), completion toggle (US-2.1, US-2.2), deletion (US-3.1).

---

### Pattern 2: Auto-Focus and Auto-Clear on Submission

**When to use:** After every successful task creation.
**Behavior:**
1. Task submitted (Enter or Add button).
2. Task appears in list.
3. Input field text is cleared.
4. Focus returns to input field immediately — user can start typing the next task without any click.

**Rationale:** Enables rapid consecutive entry without friction. A single missed refocus costs one extra click per task across a 5-item meeting capture session (JRN-02.1, Rapid Re-Entry stage). This is a Cross-Journey Pattern identified in JOURNEYS-TodoApp.md.

**Examples:** US-0.1, US-0.2 — applies every time a task is successfully created.

---

### Pattern 3: Inline Contextual Error Messages

**When to use:** Validation failures (US-0.3, US-0.4) and API errors (US-0.5, US-1.5, US-2.4, US-3.3).
**Behavior:**
- Error messages appear inline, near the element responsible for the error:
  - **Below the input field:** For task creation errors (validation and API).
  - **Below the relevant task row:** For completion toggle and delete errors.
  - **In the list area:** For task list load errors (replaces skeleton/list content).
- Messages auto-dismiss after **5 seconds** or on the **next user interaction** (whichever comes first).
- The triggering element remains usable — errors do not lock the UI.

**Message text reference:**

| Trigger | Message |
|---------|---------|
| Empty/whitespace input submitted | "Task title cannot be empty." |
| Input > 500 characters | "Task title must be 500 characters or fewer." |
| Create → 503 | "Unable to save task. Please try again." |
| Create → 500 | "Something went wrong. Please try again." |
| List load → 503 | "Unable to load tasks. Please refresh the page." |
| List load → 500 | "Something went wrong loading your tasks." |
| List load → parse error | "Unable to display tasks. Please refresh the page." |
| Toggle → 404 | "Task not found. Please refresh the page." |
| Toggle → 503 | "Unable to update task. Please try again." |
| Toggle → 500 | "Something went wrong. Please try again." |
| Delete → 404 | "Task not found. It may have already been deleted." |
| Delete → 503 | "Unable to delete task. Please try again." |
| Delete → 500 | "Something went wrong. Please try again." |

---

### Pattern 4: Empty State Lifecycle

**When to use:** When the task list is or becomes empty.
**Behavior:**
- On page load with empty backend response: empty-state message shown immediately after skeleton resolves.
- When last task is deleted: task row removed optimistically; empty-state message appears immediately in the list area (no reload required).
- When a new task is added to an empty list: empty-state message disappears immediately as the new task row appears.

**Message:** `"No tasks yet. Add one above!"`

**Placement:** Centered within the task list area — not as a page-level message (US-1.2, US-3.2).

---

### Pattern 5: Skeleton Loading

**When to use:** During the initial `GET /api/tasks` request on page load.
**Behavior:**
- While the API request is in-flight, the list area shows 3 placeholder skeleton rows (shimmering/static grey bars).
- The input field is rendered and focused immediately — the user does not need to wait to start typing.
- Skeleton is replaced by real task rows (or empty-state, or error) when the API responds.

**Rationale:** Prevents layout shift and gives visual confirmation the app is working, without blocking input (US-1.1, JRN-01.1 Orient stage).

---

## Responsive Considerations

### Desktop (> 1024px)

- Page content is constrained to a comfortable reading width (e.g., max-width: 680px) and centered horizontally.
- Delete icon (×) is **visible on row hover** only — keeps rows visually clean at rest; appears on mouse-over.
- Input field and Add button sit side-by-side in a single row.
- Task title text does not wrap for typical task lengths (< 100 chars); longer titles wrap within the row.

```
┌──────────────────────────────────────────────────────────────┐  ← max-width: 680px, centered
│  ┌──────────────────────────────────────────┐  ┌─────────┐  │
│  │  Add a task…                             │  │   Add   │  │
│  └──────────────────────────────────────────┘  └─────────┘  │
│  ☐  Buy groceries                                       [×]  │  ← [×] visible on row hover
│  ☑  ~~Write weekly report~~                             [×]  │
└──────────────────────────────────────────────────────────────┘
```

### Tablet (768px – 1024px)

- Layout identical to desktop; max-width constraint applies.
- Delete icon is **always visible** (no hover dependency — touch devices have no hover state).
- Touch targets: checkbox and delete icon minimum 44×44px (Apple HIG / WCAG 2.5.5).

### Mobile (< 768px)

- Full-width layout; no horizontal margin constraint.
- Input field and Add button remain in a single row; Add button text may reduce to `+` icon only to save space.
- Delete icon is **always visible** on the right of each row.
- Task title truncates with ellipsis if it overflows the row; full title accessible via screen reader.
- Touch targets: minimum 44×44px for all interactive elements.

```
┌───────────────────────────────────┐  ← full width
│  ┌───────────────────────┐  ┌──┐  │
│  │  Add a task…          │  │+ │  │  ← Add button = "+" icon on mobile
│  └───────────────────────┘  └──┘  │
│  ☐  Buy groceries           [×]   │  ← [×] always visible
│  ☑  ~~Write weekly report…  [×]   │  ← title truncated with ellipsis if needed
└───────────────────────────────────┘
```

---

## Accessibility Notes

### Color & Contrast
- All text must meet **WCAG AA** contrast ratio (4.5:1 for normal text, 3:1 for large text).
- Completed task "muted" styling must still meet **3:1** contrast ratio against the background — do not rely solely on strikethrough to communicate completion (strikethrough alone fails for users with low vision).
- Error messages must use a color (e.g., red) **plus** a text prefix (e.g., "⚠") — do not rely on color alone (WCAG 1.4.1).

### Keyboard Navigation
- Tab order: App title (skip) → Input field → Add button → Task rows (in list order) → [within each row: Checkbox → Delete button].
- The input field receives focus on page load automatically (`autofocus` attribute or JS `.focus()` on mount).
- Enter key on the input field submits the task (US-0.1); Space/Enter on checkbox toggles completion; Space/Enter on delete button deletes.
- All interactive elements must have visible focus rings (do not remove `outline` without a custom visible replacement).

### Screen Reader Support
- Input field: `aria-label="New task title"` or visible `<label>` element.
- Add button: `aria-label="Add task"` if it uses only a "+" icon with no text.
- Each task row checkbox: `aria-label="Mark [task title] as complete"` (active) / `aria-label="Mark [task title] as incomplete"` (completed).
- Delete button per row: `aria-label="Delete task: [task title]"` — avoids ambiguous "Delete" repeated for every row.
- Inline error messages: wrapped in `role="alert"` or `aria-live="polite"` so screen readers announce them without requiring focus.
- Empty-state message: announced naturally as list content; no special ARIA needed.
- Loading skeleton: `aria-busy="true"` on the list container while `GET /api/tasks` is in-flight; removed when resolved.

### Motion & Animation
- List reflow animations (row insertion/removal) should respect `prefers-reduced-motion`: use instant transitions when the user has reduced motion enabled.
- Skeleton shimmer animation: disabled under `prefers-reduced-motion`.

### Touch
- All interactive touch targets: minimum **44×44px** (WCAG 2.5.5 / Apple HIG).
- Checkbox and delete icon are the smallest targets; pad them with invisible hit-area if the visual element is smaller.

---

## Story Coverage Matrix

| User Story | Flow | Screen Element | State Designed |
|------------|------|----------------|---------------|
| US-0.1 Add via Enter | Flow 1 | Input field + Enter key | ✅ Default, Success |
| US-0.2 Add via Add button | Flow 1 | Add button | ✅ Default, Success |
| US-0.3 Prevent empty submission | Flow 1 | Input error zone | ✅ Validation error |
| US-0.4 Enforce 500-char limit | Flow 1 | Input error zone | ✅ Validation error |
| US-0.5 Handle creation backend failure | Flow 1 | Input error zone + list revert | ✅ API error |
| US-1.1 View all tasks on load | Flow 2 | Task list | ✅ Loading, Populated |
| US-1.2 Empty state when no tasks | Flow 2 | Empty-state message | ✅ Empty |
| US-1.3 Active and completed tasks together | Screen design | Task row styles | ✅ Active, Completed |
| US-1.4 List reflects changes without reload | Flows 1–4 | Optimistic UI pattern | ✅ All mutation states |
| US-1.5 Handle list load failure | Flow 2 | List area error | ✅ 503, 500, Parse error |
| US-2.1 Mark task complete | Flow 3 | Checkbox toggle | ✅ Active→Completed |
| US-2.2 Unmark completed task | Flow 3 | Checkbox toggle | ✅ Completed→Active |
| US-2.3 Visual distinction active vs completed | Screen design | Task row styles | ✅ Both styles |
| US-2.4 Handle completion backend failure | Flow 3 | Task row error + revert | ✅ 404, 503, 500 |
| US-3.1 Delete a task | Flow 4 | Delete icon | ✅ Default, Success |
| US-3.2 Empty state after last task deleted | Flow 4 | Empty-state message | ✅ Empty |
| US-3.3 Handle deletion backend failure | Flow 4 | Task row error + restore | ✅ 404, 503, 500 |
| US-4.1 Tasks survive browser refresh | Flow 2 | Page load / list restoration | ✅ Restore state |
| US-4.2 Tasks survive server restart | Flow 2 | Page load / list restoration | ✅ Restore state |
| US-4.3 Backend auto-initializes storage | Flow 2 | First-run empty state | ✅ Empty list on first load |
| US-4.4 Writes committed before success response | Flows 1, 3, 4 | Optimistic UI + error revert | ✅ Failure revert states |

**Coverage:** 21 / 21 user stories (100%)

---

*Document generated: 2026-05-07*
*Related documents: UserStories-TodoApp.md, PRD-TodoApp.md, FRD-TodoApp.md, JOURNEYS-TodoApp.md*

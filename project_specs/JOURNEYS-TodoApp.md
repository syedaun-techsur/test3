# User Journey Maps
## Basic To-Do App (TodoApp)

| Field | Value |
|---|---|
| **Product Name** | TodoApp — Basic To-Do App |
| **Version** | 1.0 |
| **Date** | 2026-05-07 |
| **Status** | Draft |
| **Related Personas** | PERSONAS-TodoApp.md (PER-01 Marcus Webb, PER-02 Priya Nolan) |
| **Related JTBD** | JTBD-TodoApp.md |
| **Related PRD** | PRD-TodoApp.md |

---

## Journey Index

| JRN ID | Persona | Scenario | Key JTBD | Stages |
|---|---|---|---|---|
| JRN-01.1 | PER-01 Marcus Webb | First-time task capture mid-work session | JTBD-01.1, JTBD-01.2 | 5 |
| JRN-01.2 | PER-01 Marcus Webb | End-of-day list cleanup after a busy session | JTBD-01.3, JTBD-01.4 | 5 |
| JRN-02.1 | PER-02 Priya Nolan | In-meeting action item capture for the team | JTBD-02.1, JTBD-02.2 | 6 |
| JRN-02.2 | PER-02 Priya Nolan | Morning team check-in and progress signalling | JTBD-02.2, JTBD-02.3, JTBD-02.4 | 5 |

---

## PER-01: Marcus Webb — Everyday Task Tracker

---

### JRN-01.1: First-Time Task Capture Mid-Work Session

**Persona:** PER-01 (Marcus Webb)

**Scenario:** Marcus is deep in a client project when a new commitment surfaces — a revision request comes in via email and he needs to add it to his list before losing the thought. He has heard about TodoApp and opens it in a new browser tab. He has never used it before, has no account, and does not want to spend any time setting up. All he wants to do is type the task, press Enter, and return to his client work. This journey covers the critical first-contact experience from URL open to confirmed task capture.

**Related Jobs:** JTBD-01.1, JTBD-01.2

---

#### Journey Stages

| Stage | Action | Touchpoint | Thinking | Feeling | Pain Point | Opportunity |
|---|---|---|---|---|---|---|
| **1. Arrive** | Opens TodoApp URL in a new browser tab | App load / landing view (F1, F4) | "Please don't show me a sign-up form right now — I just need to type something" | Apprehensive, impatient | Every other tool has forced him through an account creation step before reaching the input | Load input field as the primary focused element the instant the page renders — no onboarding screen |
| **2. Orient** | Glances at the page to understand what's in front of him | Task list view (F1) | "OK — there's an input box. That's all I need. Good." | Relieved, cautiously optimistic | If the list is empty and the input isn't obvious, he may second-guess whether he's on the right page | Prominent, auto-focused text input with a clear placeholder ("Add a task…") as the visual anchor |
| **3. Capture** | Types the task title and presses Enter | Task input field (F0) | "Type it, hit Enter — done. Am I done?" | Focused, slightly uncertain until the task appears | A delay or blank state after pressing Enter creates doubt about whether the task was saved | New task appears in the list instantly (< 1 second), input clears and refocuses for the next entry |
| **4. Confirm** | Scans the list and sees the new task at the top | Task list (F1) | "There it is. It saved. I can go back to what I was doing." | Confident, satisfied | If the list doesn't update immediately, Marcus may press Enter again and create a duplicate | Subtle visual confirmation (e.g., brief highlight on the new row) reinforces that the save succeeded |
| **5. Return** | Switches back to his client work tab | Browser tab bar (external) | "I'll come back to this list when I need it." | Settled, unbothered | None at this stage — the job is done | A memorable, short URL or bookmark prompt so returning is effortless |

#### Key Moments

- **Decision Point — Arrive stage:** If any modal, overlay, or sign-up prompt appears before the input field, Marcus closes the tab and never returns. This is the single highest-risk moment in his entire relationship with the product.
- **Risk of Abandonment — Capture stage:** A slow response (> 1 second) after pressing Enter creates anxiety that the task was lost; Marcus may submit again (duplicate) or abandon the app.
- **Delight Opportunity — Confirm stage:** The input clearing and refocusing immediately after Enter signals that the app is ready for the next task — a tiny, powerful signal of flow alignment.

#### Success Outcome

Marcus records his first task within 5 seconds of the page loading, with zero login, zero configuration, and zero page reload (JTBD-01.1 success measure).

#### Feature Touchpoints

| Stage | Features |
|---|---|
| Arrive | F1 (Task List View — initial load), F4 (Task Persistence — fetches existing tasks) |
| Orient | F1 (Task List View) |
| Capture | F0 (Task Creation) |
| Confirm | F1 (Task List View — live update) |
| Return | — (external browser behaviour) |

---

### JRN-01.2: End-of-Day List Cleanup After a Busy Session

**Persona:** PER-01 (Marcus Webb)

**Scenario:** It's 5:30 pm. Marcus has been adding and completing tasks throughout the day. His list now contains a mix of completed items (strikethrough) and a few remaining open tasks he will carry over to tomorrow. Before closing his laptop he wants to: mark one last task as done, delete a handful of completed and cancelled tasks to clear the noise, and then verify the list is clean before stepping away. He relies on the app to hold that state overnight so he picks up exactly where he left off in the morning.

**Related Jobs:** JTBD-01.3, JTBD-01.4

---

#### Journey Stages

| Stage | Action | Touchpoint | Thinking | Feeling | Pain Point | Opportunity |
|---|---|---|---|---|---|---|
| **1. Review** | Scans the full task list, mentally categorising items | Task list view (F1, F2) | "What's done, what's not, what can I bin?" | Focused, slightly fatigued | Completed and open tasks look too similar at a glance; hard to parse the list quickly | Clear visual separation — completed items in muted/strikethrough style, open items prominent |
| **2. Complete** | Clicks the completion toggle on the last open task just finished | Task completion toggle (F2) | "That one's done — tick it off" | Satisfied | Toggle target area is too small or misaligned; misclick opens task detail instead of toggling | Large, accessible toggle hit-area; immediate visual state change on click |
| **3. Delete** | Clicks the delete button on several completed and stale tasks | Delete button per task (F3) | "These are done — get them off the list. Is there a bulk delete? Probably not, I'll do it one by one." | Efficient, mildly tedious | No bulk delete in v1 means repeated single deletions; each one should at least be instant | Each deletion is immediate with no confirmation dialog (per PRD); smooth list reflow so the remaining items don't jump |
| **4. Verify** | Looks at the remaining list — only open carry-over tasks remain | Task list view (F1) | "Good. That's exactly what I want to see tomorrow morning." | Calm, confident | If the list still shows a stale completed item (UI lag), Marcus questions whether his deletions worked | Instant, reliable list update after every delete; no ghost rows or delayed removal |
| **5. Close & Return** | Closes the browser tab; re-opens it the next morning | App reload (F1, F4) | "It was all there last night — let me check it's still there." then "Perfect. Exactly as I left it." | Trusting (night), confirmed (morning) | Previous tools lost data on tab close or cache clear; lingering distrust from past experiences | Page load instantly restores full list with correct completion states — first-load confidence moment |

#### Key Moments

- **Decision Point — Delete stage:** Marcus notices there is no bulk delete. He accepts it in v1 but mentally flags it. If individual deletions are slow or glitchy, this becomes a pain point that compounds.
- **Risk of Abandonment — Close & Return stage:** The morning-after moment is a trust checkpoint. If even one task is missing or in the wrong state, Marcus loses confidence in the app as a reliable tool and reverts to his text file.
- **Delight Opportunity — Verify stage:** A clean list of only open items — with no clutter — is intrinsically satisfying. The app earns trust passively by reflecting Marcus's mental model perfectly.

#### Success Outcome

Every task Marcus marked complete or deleted is in the correct state after a hard browser refresh the following morning; zero data loss and zero ghost items (JTBD-01.4 success measure; JTBD-01.3 supporting measure).

#### Feature Touchpoints

| Stage | Features |
|---|---|
| Review | F1 (Task List View), F2 (visual completion state) |
| Complete | F2 (Task Completion toggle + persistence) |
| Delete | F3 (Task Deletion) |
| Verify | F1 (Task List View — updated state) |
| Close & Return | F4 (Task Persistence), F1 (Task List View — restored on reload) |

---

## PER-02: Priya Nolan — Lightweight Project Coordinator

---

### JRN-02.1: In-Meeting Action Item Capture for the Team

**Persona:** PER-02 (Priya Nolan)

**Scenario:** Priya's weekly team coordination meeting is underway. Four people are on a video call discussing a volunteer initiative. Action items are flying — three in the first ten minutes. Priya has TodoApp open in a browser tab on her laptop. She needs to log each action item the moment it's called out, without asking anyone to pause, without switching apps, and without any friction that causes her to miss the next item. After the meeting, one team member on a different device will load the same URL and expect to see every item Priya just captured. This journey covers rapid repeated task entry and immediate cross-session visibility.

**Related Jobs:** JTBD-02.1, JTBD-02.2

---

#### Journey Stages

| Stage | Action | Touchpoint | Thinking | Feeling | Pain Point | Opportunity |
|---|---|---|---|---|---|---|
| **1. Pre-Meeting Setup** | Opens TodoApp URL on her laptop before the call starts | App load / task list (F1, F4) | "Let me pull this up now so it's ready when the meeting starts — I don't want to be fiddling with a URL while someone is talking" | Organised, slightly rushed | If the page is slow to load or requires any step before the input is ready, the setup window is lost | Sub-1-second page load with input immediately focused; existing tasks from prior sessions are already visible |
| **2. First Capture** | Types first action item as it's called out, presses Enter | Task input (F0) | "Sprint notes — 'Follow up with venue re: date' — Enter. Done." | Focused, flowing | If there is any latency between Enter and the task appearing, Priya loses confidence mid-meeting | Task appears in list within 1 second; input clears and refocuses automatically — zero friction for the next entry |
| **3. Rapid Re-Entry** | Types 4 more action items in quick succession as the meeting progresses | Task input (F0), task list (F1) | "Keep up, keep up — another one: 'Send budget draft to board'… Enter. One more: 'Confirm volunteers for Saturday'… Enter." | Concentrated, under mild pressure | If the input does not refocus after each Enter, Priya must click back into the field each time — one extra click per item compounds quickly | Auto-refocus after every submission; list grows visibly below, showing all captured items at a glance |
| **4. Mid-Meeting Scan** | Glances at the list to confirm all items are captured | Task list view (F1) | "Let me check I got all five. Yes — five items. Good." | Reassured | List is getting long and it's hard to distinguish the new items from old ones without timestamps or visual grouping | Newest items appear at the top (or are visually fresh) so Priya can count the meeting's additions at a glance |
| **5. Post-Meeting Share** | Shares the app URL in the team chat so others can see the list | Browser address bar (external) | "I'll just drop the link in chat — they can open it and see everything I added." | Efficient, pleased | Priya has to verbally instruct teammates how to see the list (it's not a shared link they've used before); first-time openers may be uncertain | The URL itself is the share mechanism — no invite step, no permissions; a consistent, memorable URL builds habit |
| **6. Cross-Device Verification** | A teammate opens the URL on their own device and confirms the list matches | App load on second device (F1, F4) | (Teammate) "She said she added everything in the meeting — let me check... yep, all five are there." | (Teammate) Confident, no friction | If the teammate sees a different or empty list (e.g., stale cache), it erodes trust in the app's reliability as a shared source | Server-side persistence guarantees every load reflects the latest state regardless of device or session |

#### Key Moments

- **Decision Point — Rapid Re-Entry stage:** The rate of input is determined by the meeting pace, not Priya's typing speed. Any UI friction (missed refocus, delayed list update) causes her to fall behind and risk missing an item — this is the highest-stakes moment of the journey.
- **Risk of Abandonment — Cross-Device Verification stage:** If a teammate loads the URL and sees a different or empty list, the team immediately loses faith in the tool and Priya faces social pressure to switch back to the Google Doc. This is the moment the app's persistence architecture is publicly tested.
- **Delight Opportunity — Post-Meeting Share stage:** The simplicity of sharing — just a URL, no accounts, no invite — is a genuine differentiator that Priya will articulate to her team. This moment converts Priya from a user into an informal advocate.

#### Success Outcome

Priya captures 5 consecutive action items in under 90 seconds during a live meeting; every item is visible to a team member loading the same URL on a separate device within 5 seconds of the last entry — no login, no manual sync required (JTBD-02.1 success measure).

#### Feature Touchpoints

| Stage | Features |
|---|---|
| Pre-Meeting Setup | F1 (Task List View — load), F4 (Task Persistence — existing tasks restored) |
| First Capture | F0 (Task Creation) |
| Rapid Re-Entry | F0 (Task Creation — repeated), F1 (Task List View — live update) |
| Mid-Meeting Scan | F1 (Task List View) |
| Post-Meeting Share | — (URL sharing, external) |
| Cross-Device Verification | F1 (Task List View — second device), F4 (Task Persistence — server state) |

---

### JRN-02.2: Morning Team Check-In and Progress Signalling

**Persona:** PER-02 (Priya Nolan)

**Scenario:** It is 9:00 am the day after the team meeting. Priya opens her laptop and loads TodoApp to review the action items she captured yesterday. Two tasks were completed by a volunteer last night — she needs to mark them done so the team can see progress when they check in. A third task has been cancelled and should be removed. After Priya's updates, a volunteer on a different device opens the same URL to check their assignments for the day. This journey covers the daily review and progress signalling workflow — the ongoing team coordination loop that makes the app valuable beyond the initial capture moment.

**Related Jobs:** JTBD-02.2, JTBD-02.3, JTBD-02.4

---

#### Journey Stages

| Stage | Action | Touchpoint | Thinking | Feeling | Pain Point | Opportunity |
|---|---|---|---|---|---|---|
| **1. Morning Load** | Opens the app URL on her laptop | App load / task list (F1, F4) | "Let me see where we are. Everything from yesterday should still be here." | Cautiously expectant | Priya has been burned by localStorage tools losing data overnight; the first second of load is a trust test | All tasks — including completion states from the previous session — are fully restored on load; the list looks exactly as she left it |
| **2. Review** | Reads through the full list to assess current status | Task list view (F1, F2) | "Three open, two done from last night — good. Which one is cancelled again?" | Focused, oriented | Completed and open tasks are visually indistinct; Priya has to read each title to find the open items | Strikethrough / muted style for completed tasks makes the active items pop without filtering or navigation |
| **3. Mark Complete** | Clicks the completion toggle on two tasks that were finished last night | Completion toggle per task (F2) | "Venue confirmed — done. Budget draft sent — done. That'll show everyone those are cleared." | Satisfied, purposeful | If the toggle requires a double-click or has a tiny hit area, Priya misses it and the task stays open — a false signal to the team | Single-click toggle; immediate visual state change; completion state synced to backend so any reload reflects the update |
| **4. Delete Cancelled Task** | Clicks delete on the one cancelled task | Delete button (F3) | "That one's off the table — remove it so it doesn't confuse anyone." | Decisive, efficient | No confirmation dialog (per PRD) means an accidental click permanently removes the task with no undo | Instant deletion with no dialog (fast); consider a very brief undo window (2–3 seconds) as a v2 enhancement note |
| **5. Verify & Close** | Reviews the cleaned-up list; satisfied, closes the laptop | Task list view (F1) | "Two done, three still open, one removed. That's accurate. The team will see this when they check in." | Confident, trustful | If the list doesn't update instantly after each action, Priya questions whether her changes were persisted | Immediate list update after every toggle and delete; no ghost rows; the visual state is the truth |

#### Key Moments

- **Decision Point — Morning Load stage:** The instant the page loads, Priya either confirms or loses trust in the app. If every task is present and correctly statused, she's committed. If anything is missing, the Google Doc becomes the backup and TodoApp is demoted to "optional."
- **Risk of Abandonment — Mark Complete stage:** If a team member loads the page immediately after Priya marks tasks complete and sees the old state (not persisted yet), they will assume the app is broken or out of sync. This is the scenario that turns a team off a shared tool.
- **Delight Opportunity — Verify & Close stage:** A clean, accurate list — completed tasks styled differently, only open items prominent — validates Priya's coordination work visually. The app is silently doing the status-update job she used to do manually in group chat.

#### Success Outcome

After Priya marks two tasks complete and deletes one, any team member loading the same URL within 5 seconds sees the updated list with zero missing tasks and correct completion states — no manual sync, no re-entry required (JTBD-02.3 and JTBD-02.4 success measures).

#### Feature Touchpoints

| Stage | Features |
|---|---|
| Morning Load | F1 (Task List View), F4 (Task Persistence — overnight durability) |
| Review | F1 (Task List View), F2 (visual completion state) |
| Mark Complete | F2 (Task Completion — toggle + backend persist) |
| Delete Cancelled Task | F3 (Task Deletion) |
| Verify & Close | F1 (Task List View — final state) |

---

## Cross-Journey Patterns

### Common Pain Points Across All Journeys

- **Trust gap at first load (JRN-01.2, JRN-02.1, JRN-02.2):** Every journey contains a moment — usually the page load — where the user is silently asking "Is my data still here?" All three affected journeys converge on F4 (Task Persistence) as the make-or-break feature. A sub-1-second load with complete data restoration is the single most impactful thing the app can do to build user trust.

- **Input refocus after task creation (JRN-01.1, JRN-02.1):** Both Marcus (rapid task capture mid-flow) and Priya (rapid multi-item capture in a meeting) depend on the input field automatically clearing and refocusing after pressing Enter. A missing refocus costs one extra click per task — trivial once, significant across 5–15 entries per session.

- **Visual distinction between completed and open tasks (JRN-01.2, JRN-02.2):** Both personas rely on at-a-glance list scanning to assess status. In both journeys, poor visual differentiation (completed vs. open) forces slower, line-by-line reading that disrupts the flow. Strikethrough or muted styling on completed tasks is a shared solution.

- **Immediate UI response to actions (JRN-01.1, JRN-01.2, JRN-02.1, JRN-02.2):** Every journey includes a confirmation moment where the user visually checks that their action (create, complete, delete) took effect. Any latency > 1 second at these moments introduces doubt, risk of duplicate actions, or abandonment. Optimistic UI updates (update the list instantly, sync to backend in parallel) resolve this across all journeys.

### Shared Opportunities (Solve Once, Benefit All)

| Opportunity | Journeys Affected | Features |
|---|---|---|
| Auto-focus + auto-clear input after Enter | JRN-01.1, JRN-02.1 | F0 |
| Sub-1-second page load with full data restoration | JRN-01.2, JRN-02.1, JRN-02.2 | F1, F4 |
| Strikethrough / muted style for completed tasks | JRN-01.2, JRN-02.2 | F2 |
| Instant list update (optimistic UI) for all mutations | JRN-01.1, JRN-01.2, JRN-02.1, JRN-02.2 | F0, F2, F3 |

### Persona Convergence Point

Stages where both personas interact with the same feature in the same way:
- **Task Creation (F0):** Both rely on type-and-Enter as the complete submission flow; both need auto-refocus.
- **Task List View (F1):** Both use it as the primary orientation surface on every session open.
- **Task Persistence (F4):** Both have a "did it survive?" trust checkpoint — Marcus at the individual session level, Priya at the cross-device and overnight level.

---

## Journey-to-JTBD Traceability

| Journey | Stage | JTBD ID | Expected Outcome |
|---|---|---|---|
| JRN-01.1 | Arrive | JTBD-01.1 | Page loads with no login, modal, or onboarding; input field is immediately available |
| JRN-01.1 | Orient | JTBD-01.2 | Existing tasks (if any) visible in under 1 second; clean single-list view |
| JRN-01.1 | Capture | JTBD-01.1 | Task submitted via Enter in ≤ 2 interactions; appears in list within 1 second |
| JRN-01.1 | Confirm | JTBD-01.1 | New task visible in list immediately after submission with no page reload |
| JRN-01.2 | Review | JTBD-01.2 | All tasks (open and complete) visible in single list; completed tasks visually distinct |
| JRN-01.2 | Complete | JTBD-01.3 | Task marked complete in 1 interaction; visual state changes immediately |
| JRN-01.2 | Delete | JTBD-01.3 | Task removed in 1 interaction; disappears from list within 1 second |
| JRN-01.2 | Verify | JTBD-01.3 | List reflects current state after all actions; no ghost rows or stale items |
| JRN-01.2 | Close & Return | JTBD-01.4 | 100% of tasks and completion states restored after hard browser refresh; zero data loss |
| JRN-02.1 | Pre-Meeting Setup | JTBD-02.1 | App loads in < 1 second; input immediately ready; no login or setup step |
| JRN-02.1 | First Capture | JTBD-02.1 | Task appears in list within 1 second; input clears and refocuses |
| JRN-02.1 | Rapid Re-Entry | JTBD-02.1 | 5 consecutive tasks captured in < 90 seconds; each appears immediately after Enter |
| JRN-02.1 | Mid-Meeting Scan | JTBD-02.2 | All captured items visible in one list with no filtering required |
| JRN-02.1 | Cross-Device Verification | JTBD-02.2 | Task list on second device matches first device after page reload; no login required |
| JRN-02.2 | Morning Load | JTBD-02.4 | Full task list with correct completion states restored after overnight gap; zero data loss |
| JRN-02.2 | Review | JTBD-02.2 | Single consistent list view; completed tasks visually distinct from open tasks |
| JRN-02.2 | Mark Complete | JTBD-02.3 | Completion toggle requires 1 interaction; state persisted to backend immediately |
| JRN-02.2 | Delete Cancelled Task | JTBD-02.3 | Task removed in 1 interaction; list updates immediately; backend state updated |
| JRN-02.2 | Verify & Close | JTBD-02.4 | Any team member reloading the page within 5 seconds sees the updated list; no sync action required |

---

*Document generated: 2026-05-07*
*Related documents: PERSONAS-TodoApp.md, JTBD-TodoApp.md, PRD-TodoApp.md*

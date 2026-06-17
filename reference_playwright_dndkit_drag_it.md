---
name: reference_playwright_dndkit_drag_it
description: "dnd-kit drag-reorder silently no-ops in Playwright integration tests (no PATCH, then a response timeout) for three distinct reasons — below-fold handle, rapid Mouse.Move steps, and a lingering MUI Menu backdrop."
metadata:
  node_type: memory
  type: reference
  originSessionId: ae937c6e-d416-4daf-b4cc-519fa25fd6f9
---

Driving a dnd-kit drag-reorder from a Playwright IT with the raw `Mouse` API fails *silently* — `onDragOver` never fires, no reorder PATCH is sent, and the step ends in a response timeout. Three independent causes, all seen in recipe/list IT work:

1. **Handle below the fold.** `Mouse.Move` uses viewport coordinates and does not auto-scroll, so on the default 1280×720 viewport a handle low on a long edit page is never actually hovered. Call `ScrollIntoViewIfNeededAsync` on the handle before measuring/moving.
2. **`Mouse.Move(Steps=N)` fires sub-moves back-to-back** with no scheduler gap, so they all land before dnd-kit's `onDragStart` re-render measures the droppable rects → collision detection finds no target. Replace with an explicit move-then-`WaitForTimeoutAsync(20)` loop so the moves spread over time.
3. **Lingering MUI `Menu` backdrop.** After clicking a `Menu` item, an invisible full-viewport backdrop stays in the DOM through the close transition. The raw `Mouse` API skips actionability checks, so `mouse-down` lands on the backdrop instead of the handle. Wait for the backdrop element to detach before any raw mouse interaction. (`FillAsync`/`ClickAsync` auto-retry and are immune — this only bites the raw `Mouse` path.)

**Why:** the symptom is identical in all three — drag no-ops → no PATCH → timeout — so it reads as a flaky test when it is actually a measurement / hit-testing race. Each was confirmed by live MCP-browser reproduction on the dev stack.

**How to apply:** for any dnd-kit drag step in an IT — scroll the handle into view, drive pointer-moves with explicit spacing, and ensure no MUI overlay/backdrop is mid-transition before the `mouse-down`. Related: [[feedback_playwright_assertions]], [[feedback_playwright_mcp_mui_ids]], [[reference_mui_x_datepicker_playwright]], [[project_it_serves_clientapp_build]].

(originSessionId `ae937c6e` covers causes 1–2; cause 3 — the MUI Menu backdrop — was diagnosed in session `19dda25f`.)

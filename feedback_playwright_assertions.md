---
name: feedback_playwright_assertions
description: In Reqnroll/Playwright UI tests use Assertions.Expect(locator).ToBeVisibleAsync()/.Not.ToBeVisibleAsync() instead of locator.IsVisibleAsync(); the snapshot API races re-renders and is the wrong tool for post-action assertions.
type: feedback
originSessionId: e95f7df5-e5df-45cd-b74c-f2146a93e340
---
When writing UI test steps that verify post-action state ("X appears", "X is gone", "X is checked"), use Playwright's retrying `Assertions.Expect(...)` family. Avoid the snapshot `IsVisibleAsync` / `IsCheckedAsync` / `TextContentAsync` family for assertions.

**Why:** `IsVisibleAsync` is a non-retrying snapshot — it returns the visibility state at one instant, with no polling. Any assertion built on it races against pending React re-renders, TanStack Query invalidate-and-refetch cycles, and unresolved DELETE→GET round trips. We hit this on `User deletes an inventory`: the test asserted `Assert.False(await item.IsVisibleAsync())` immediately after a click, but the click resolved before `mutateAsync` → invalidate → refetch → re-render finished. The element was still in the DOM ~5/6 runs, so the assertion failed flakily (and once headless mode made the runner faster, it became near-deterministic). `Expect(...).ToBeVisibleAsync()` / `.Not.ToBeVisibleAsync()` polls until the condition holds (default 5s timeout, ~25–100ms interval, returns immediately on first success), which adapts to whatever timing the SUT actually has.

**How to apply:**
- For "should appear" / "should disappear" assertions in step files (`Frigorino.IntegrationTests/Slices/**/*Steps.cs`), use `await Assertions.Expect(locator).ToBeVisibleAsync()` or `.Not.ToBeVisibleAsync()`.
- Equivalent retrying alternative: `await locator.WaitForAsync(new() { State = WaitForSelectorState.Visible/Hidden })` — also fine and used in `ListSteps`.
- Reserve snapshot APIs (`IsVisibleAsync`, `IsCheckedAsync`, `TextContentAsync`, etc.) for cases where you've already established a steady state via `WaitForURLAsync`, `WaitForLoadStateAsync(NetworkIdle)`, or `WaitForAsync()`.
- A discarded snapshot-API result (`var visible = await x.IsVisibleAsync();` with no assertion, or `await x.IsVisibleAsync();` as a statement) is always a bug — replace with `Expect` if it was meant as an assertion, or `WaitForAsync()` if it was meant to wait for the element.
- If a real failure causes a 5s wait that hurts CI feedback, lower per-call: `new() { Timeout = 2000 }`. Don't lower the global default — the 5s margin is what makes tests robust against unrelated slowness.

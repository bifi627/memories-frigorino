---
name: project_composer_keyboard_focus
description: Composer mobile soft-keyboard focus handling — keepKeyboardOpen + Popper-not-Menu gotcha
metadata: 
  node_type: memory
  type: reference
  originSessionId: 3a58dd01-7f18-44ee-81ea-e4aad0be3386
---

Keeping the mobile soft keyboard open in the list/inventory Composer (`components/composer/Composer.tsx`):

- **`keepKeyboardOpen`**: one `onMouseDown` on the composer's outer Box that `preventDefault()`s for any non-input target (buttons, chips, padding) so tapping them doesn't blur the field and collapse the keyboard. Excludes `input, textarea, [contenteditable]` so real fields still focus. Replaced the old per-element `preventInputBlur`.

- **Attach menu = `Popper`, NOT `Menu`**: MUI's `Menu` is `Modal`-based — on open it marks the rest of the page `aria-hidden`, and mobile Chrome blurs a focused input inside an aria-hidden subtree → keyboard collapses. **No `Menu` focus prop fixes this** (`autoFocus={false}`, `disableAutoFocusItem`, `disableEnforceFocus`, `disableRestoreFocus` all insufficient because of aria-hidden). Use a non-modal `Popper` + `MenuList` (`autoFocusItem={false}`) + `ClickAwayListener` instead — it never traps focus or aria-hides. See `attachComposerFeature.tsx`.

- Edit mode autofocuses the field via a `useEffect` gated on `isEditing && !initialOpenId` (skip add mode + chip-opened panels) with `requestAnimationFrame(focusInput)`.

Shipped on `feat/whatsapp-composer` (PR #76 → stage). Toasts themed via MUI `GlobalStyles` over sonner data attributes (`[data-sonner-toaster] …`). Related: [[feedback_object_url_strictmode]].

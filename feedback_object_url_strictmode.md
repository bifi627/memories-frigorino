---
name: feedback_object_url_strictmode
description: "Blob object URLs in this React app must be created+revoked in ONE paired useEffect, never useMemo; the app runs in StrictMode"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: a3051593-93e0-4dcf-82fa-50453cee87f0
---

This SPA renders inside `<StrictMode>` (`ClientApp/src/main.tsx`). That makes the common "create an object URL in `useMemo`, revoke it in a separate `useEffect` cleanup" pattern **buggy**: StrictMode's mount→unmount→remount probe runs the effect setup→cleanup→setup, and `useMemo` (an optimization-only hook React may keep across the strict remount) can hand back the SAME url that the cleanup already revoked → a dead `blob:` URL → broken `<img>` (and no error fallback, because the hook still "has" a value).

**Rule:** for any `URL.createObjectURL` / `revokeObjectURL`, pair them in ONE `useEffect` keyed on the source (Blob/File), storing the url in `useState`:
```ts
const [url, setUrl] = useState<string>();
useEffect(() => {
  if (!blob) { setUrl(undefined); return; }
  const u = URL.createObjectURL(blob);
  setUrl(u);
  return () => URL.revokeObjectURL(u);
}, [blob]);
```
Each `createObjectURL` is revoked by its own closure, so the surviving setup's url is the one in state. If a loading→broken flash matters, gate the consumer's `isLoading` on `(!!blob && !url)`.

**Also:** don't cache an object URL in TanStack Query (`staleTime: Infinity`) and revoke it on unmount — a synchronous unmount→remount (e.g. checking off / reordering a list row that moves between the unchecked/checked `<ul>`s) revokes the cached url while the cache still holds it, then serves the dead url to the remounted observer. Cache the **Blob**; derive the url per-consumer with the effect above.

**Why:** rich-list-items #2 (image items) shipped both anti-patterns from verbatim plan code (`features/lists/items/useItemImage.ts`, `.../components/MediaPreviewSheet.tsx`); tsc/lint/prettier were all green and the planned integration test (opens lightbox once, never toggles) would have missed it. Manual browser verify caught it — toggling an image item broke its thumbnail. Concrete confirmation of [[feedback_verbatim_plan_verify]].

**How to apply:** when sub-feature #3 (documents) or any future feature adds a file/image preview, use the paired-effect pattern from the start; never `useMemo` an object URL.

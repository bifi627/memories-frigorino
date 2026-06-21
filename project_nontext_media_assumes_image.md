---
name: project_nontext_media_assumes_image
description: "Adding a new ListItemType requires auditing every UI path that branches on item.type !== \"Text\" — they assumed non-Text media == Image (thumbnail/img)"
metadata: 
  node_type: memory
  type: project
  originSessionId: 96057bd1-e155-4863-a199-cfbc023067a7
---

When adding a new `ListItemType` (e.g. `Document`), audit every frontend path that treats "non-Text" as "Image": `ListViewPage.handleEditItem` routes any `item.type !== "Text"` to the caption sheet, and `MediaCaptionSheet` / `MediaPreviewSheet` were written when media meant Image — they fetch a thumbnail (`useItemImage(..., "thumbnail")`) and render `<img>`. A `Document` has no thumbnail, so those paths show a `<BrokenImage>` box and fire a guaranteed 404.

**Why:** the per-task review missed this; only the opus whole-branch review caught it. The grouping `item.type !== "Text"` is not compiler- or test-enforced, so a new media type silently inherits image-only rendering.

**How to apply:** for any new media-ish `ListItemType`, branch every shared "non-Text media" component on the new type (icon + `fileName` card for documents; disable `useItemImage` for types with no thumbnail) and add a UI IT for the edit/caption path, not just the add path. Fixed in PR #139 for `MediaCaptionSheet` + `MediaPreviewSheet`.

Related contract-style risks: [[project_delete_inactive_items_contract]], [[project_listitem_slice_classification_contract]].

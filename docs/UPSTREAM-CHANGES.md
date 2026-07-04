# Changes awaiting upstreaming to storykit-starter

The canonical StoryKit framework repo is
[rsnyder/storykit-starter](https://github.com/rsnyder/storykit-starter).
This repo was baselined to `storykit-starter@2df0913fc0221dd07764b40f1111781efa3da6b6`
(see `SRC_REF` in `tools/sync_code.py`), and the framework improvements listed
below were developed here on top of that baseline.

**Every file listed under "Changed" or "Added" that appears in
`FILES_TO_SYNC` must be copied to storykit-starter before running
`tools/sync_code.py --apply` again, or the sync will destroy the
improvements.** Files listed under "Deleted" must also be deleted in
storykit-starter and removed from `FILES_TO_SYNC` there, or the next sync
resurrects them. After upstreaming, bump `SRC_REF` to the new
storykit-starter commit and confirm `python3 tools/sync_code.py --check`
reports no drift.

Update this file in every PR that touches a synced framework file.

## Changed (relative to baseline 2df0913)

| File | Change |
|---|---|
| `tools/sync_code.py` | Hardened: pinned `SRC_REF`, `--check`/`--apply` modes, drift diffs, no blind overwrite, token-scoping fallback |
| `.github/workflows/pages-deploy.yml` | Ruby version from `.ruby-version`; consistency + drift-check steps; htmlproofer test step re-enabled |
| `_includes/sidebar.html` | `#view-toggle` anchor: `href="#"` + `role="button"` (was `href=""`, an htmlproofer failure on every page) |
| `_admin/index.md` | Removed dead vis-network link (page recreated in docs overhaul); fixed `#guides` anchor |
| `_admin/2019-08-08-write-a-new-post.md` | Fixed dead relative link (file deleted in docs overhaul anyway) |

## Added

| File | Purpose |
|---|---|
| `.ruby-version` | Single source of truth for the Ruby version (local + CI) |
| `tools/check_consistency.py` | CI checks: preview CHIRPY_VERSION vs Gemfile.lock, Shoelace version convergence, sync-manifest validity |
| `docs/UPSTREAM-CHANGES.md` | This manifest |

## Deleted

| File | Reason |
|---|---|
| `_layouts/storykit.html` | Unreferenced stale near-copy of `post.html` (was never in `FILES_TO_SYNC`; local-only cleanup, nothing to delete upstream) |

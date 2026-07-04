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

### Documentation overhaul (Phase 2)

| File | Change |
|---|---|
| `_admin/index.md` | Rewritten as a documentation map: getting-started / viewers / interaction sections, troubleshooting pointer, Chirpy external link |
| `_admin/2026-02-15-storykit-overview.md` | Enable model corrected (on by default, `storykit: false` to opt out); entity-popup contents scoped to actual behavior; Chirpy doc links externalized |
| `_admin/2026-02-15-storykit-authors-guide.md` | Worked example now matches the real monument-valley post; guide list expanded; checklist updated; order 12→11 |
| `_admin/2026-02-15-storykit-preview-setup.md` | order 11→12 |
| `_admin/2026-02-15-storykit-formatting-tips.md` | Examples use real include names (`embed/map.html`, `embed/image.html`) instead of nonexistent `my-map.html`/`my-image.html` |
| `_admin/2026-02-15-storykit-image-viewer.md` | Added missing attributes: `id`, `class`, `width`, `attribution`; fixed seq/label typos; cross-link to action-links reference; order 22→21 |
| `_admin/2026-02-15-storykit-map-viewer.md` | Documented the load-bearing `flyto` action (coords + Wikidata forms, toggle-back behavior) with live example; added missing attributes `aspect`, `class`, `id`, `src`, marker example; order 24→22; mode 2col→flat |
| `_admin/2026-02-15-storykit-image-compare-viewer.md` | Added missing attributes: `width`/`height`, `id` |
| `_admin/2026-02-15-storykit-youtube-viewer.md` | Added missing attributes `aspect`, `class`; action table now includes `play`/`pause`; order 25→24 |
| `_admin/2026-02-15-storykit-entity-info-popups.md` | Claims scoped to what the code renders (no more dates/roles/locations promise); order 21→27 |

## Added

| File | Purpose |
|---|---|
| `.ruby-version` | Single source of truth for the Ruby version (local + CI) |
| `tools/check_consistency.py` | CI checks: preview CHIRPY_VERSION vs Gemfile.lock, Shoelace version convergence, sync-manifest validity |
| `docs/UPSTREAM-CHANGES.md` | This manifest |
| `_admin/2026-02-15-storykit-viewers-overview.md` | One-page tour of all viewers + common attribute pattern (order 20) |
| `_admin/2026-02-15-storykit-vis-network-viewer.md` | Network viewer reference incl. the `<id>-csv` data-block convention (order 25) — fixes the dead link previously in index.md |
| `_admin/2026-02-15-storykit-iframe-viewer.md` | Generic iframe embed reference (order 26) |
| `_admin/2026-02-15-storykit-action-links.md` | Unified action-link reference: syntax, per-viewer action tables (`zoomto`, `flyto`, `playat`/`play`/`pause`), live example, checklist (order 30) |
| `_admin/2026-02-15-storykit-display-modes.md` | Flat vs two-column modes, toggle, full `storykit:` settings table (order 31) |
| `_admin/2026-02-15-storykit-troubleshooting.md` | Symptom-first troubleshooting guide for the common failure modes (order 40) |

All new `_admin` pages were added to `FILES_TO_SYNC`.

## Deleted

| File | Reason |
|---|---|
| `_layouts/storykit.html` | Unreferenced stale near-copy of `post.html` (was never in `FILES_TO_SYNC`; local-only cleanup, nothing to delete upstream) |
| `_admin/2019-08-09-getting-started.md` | Chirpy leftover teaching a conflicting install/deploy workflow; **also delete in storykit-starter and remove from its `FILES_TO_SYNC`** |
| `_admin/2019-08-08-write-a-new-post.md` | Chirpy leftover; front-matter basics folded into the authors guide; **also delete upstream** |
| `_admin/2019-08-08-text-and-typography.md` | Chirpy leftover; **also delete upstream** |
| `_admin/2019-08-11-customize-the-favicon.md` | Chirpy leftover; **also delete upstream** |

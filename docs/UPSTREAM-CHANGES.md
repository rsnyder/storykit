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

### Preview tool reliability (Phase 7)

| File | Change |
|---|---|
| `preview/index.html` | `?dev[=<origin>]` mode loads site assets (storykit.js/css, viewer components) from a local Jekyll server instead of the deployed site, making JS/CSS edits visible in preview during framework development; limitations documented in the header comment. (`collapseMultilineTags` was verified already whitespace-tolerant — no change needed.) |
| `_admin/2026-02-15-storykit-preview-setup.md` | New "What the Preview Can and Can't Show" section (markdown-engine caveat, single-page build, GitHub lag, committed-content-only, dev mode note) |

### Dependency pinning, vendoring, Wikidata caching (Phase 6)

| File | Change |
|---|---|
| `assets/js/storykit.js` | Shoelace imports pinned to 2.18.0; per-QID sessionStorage cache (24 h TTL) for entity-popup data — repeat page loads no longer re-query Wikidata/Wikipedia |
| `_layouts/post.html` | Shoelace theme + autoloader pinned to 2.18.0 |
| `assets/components/image-compare.html` | Shoelace 2.15.0 → 2.18.0 |
| `assets/components/image.html` | OpenSeadragon 5.0.1 with SRI; marked pinned to 18.0.5 |
| `assets/components/map.html` | Leaflet 1.9.4 js/css + leaflet-gesture-handling 1.2.2 + exif-js 2.3.0 with SRI; **@allmaps/leaflet pinned to 1.0.0-beta.44** (newest release still shipping the bundled build — the unversioned URL had silently broken when beta.45 dropped it); SmoothWheelZoom now vendored locally; Leaflet 0.7.7 marker images now vendored locally; marked pinned |
| `assets/components/vis-network.html` | papaparse SRI; Shoelace pinned; marked pinned |
| `assets/components/youtube.html` | marked pinned |
| `preview/index.html` | LiquidJS/markdown-it/js-yaml/plugins pinned to exact versions |
| `tools/check_consistency.py` | `SHOELACE_STRICT = True` — divergent/unversioned Shoelace now fails CI |
| **New:** `assets/js/vendor/Leaflet.SmoothWheelZoom.js`, `assets/img/leaflet/marker-icon{,-2x}.png`, `marker-shadow.png`, `docs/dependencies.md` | Vendored fragile assets + full dependency inventory; vendored files added to `FILES_TO_SYNC` |

SRI hashes were computed from npm tarballs (byte-identical to jsDelivr/unpkg). ES-module imports carry pins only (SRI not applicable).

### Shared embed builder + validation (Phase 5)

| File | Change |
|---|---|
| `_includes/embed/_iframe.html` | **New** shared iframe emitter: consistent id/class/title/sizing attributes; emits `data-storykit-warn="no-id"` when a viewer has no id (**add to `FILES_TO_SYNC` upstream**) |
| `_includes/embed/{image,map,youtube,image-compare,vis-network,iframe}.html` | All six delegate emission to the shared partial; query-string values escaped per-value with `cgi_escape` (replaces image.html's bespoke `uri_escape`+`%26`/`%2B` double-encode; **fixes captions containing `&` being silently truncated**); map omits empty `center=`; map now actually forwards its documented `src` layer param; image-compare honors `class` (previously silently dropped) and accepts alignment params appended with `&` or `?`; iframe.html dead `qs` code removed and gains a `title` |
| `assets/js/storykit.js` | `addActionLinks` diagnostics: one `console.warn` listing action-link targets that don't exist on the page; one `console.info` when embeds lack ids |

Verified by decode-equivalence check across all 16 built pages with iframes: every component query string parses identically before/after except the two known caption-`&` truncation fixes.

### Mode controller + section hardening (Phase 4)

| File | Change |
|---|---|
| `assets/js/storykit.js` | Single display-mode controller (`initStoryKit`/`setViewMode`/`getViewMode`; precedence: post front matter > reader's saved choice > site default > flat; fires `storykit:modechange`); `init2col` is idempotent with a full `teardown2col` (AbortController-scoped listeners, `scroller.destroy()`, lazy scrollama creation); step selectors and `findViewerSource` are section-aware (fixes flat→col2 toggle yielding zero scroll steps); `restructureMarkdownToSections` now runs in both modes, documents its edge-case behavior, and guarantees stable section ids (`sk-section-<n>` fallback) |
| `_layouts/post.html` | Competing inline mode module (duplicate localStorage read, class toggling, log-only MutationObserver) replaced by one `initStoryKit(...)` call |
| `_includes/col2-toggle.html` | Reduced to a dumb button: calls `setViewMode`, follows `storykit:modechange` for its icon; no mode logic of its own |
| `technical-overview.md` | Two-column behavior description reconciled with the implementation (paragraph steps + section-aware viewer lookup; centralized mode controller) — *not a synced file; update the equivalent doc upstream if one exists* |

### postMessage protocol unification (Phase 3)

| File | Change |
|---|---|
| `assets/js/storykit.js` | One `storykit:*` envelope for all messages; origin check on by default (`location.origin`); deleted dead `setAspect`/`openLink` handlers; action links send a real args array (JSON in `data-args`), null-guard their target with a `console.warn` naming the missing id, and use `currentTarget` (child-element clicks no longer break) |
| `assets/components/image.html` | Uses shared runtime (`StoryKit.onAction`/`showDialog`); removed bespoke message parsing |
| `assets/components/map.html` | Fixed broken `showDialog` shape (`props:{}` wrapper the host never read); restored the expand button (markup + handler, hidden in max mode); `flyto` via `StoryKit.onAction`; removed bespoke parsing |
| `assets/components/youtube.html` | Uses shared runtime; protocol comments updated; removed bespoke parsing |
| `assets/components/image-compare.html` | `storykit:height` via `StoryKit.reportHeight` (replaces `image-compare:height`); dialog via runtime |
| `assets/components/vis-network.html` | Unguarded `JSON.parse` replaced with runtime promise helpers (`getHostId`/`requestHostElement`) incl. retry for the host-not-yet-listening race; warns instead of silently failing when the data block is missing |

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
| `assets/js/storykit-component.js` | Shared component runtime: enveloped messaging, origin checks, safe parsing, dialog/height/request helpers (**add to `FILES_TO_SYNC` upstream**) |
| `docs/postmessage-protocol.md` | Maintainer spec of the unified protocol |
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

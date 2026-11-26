# StitchKit handoff (v1.0.3, 2026-11-25)

StitchKit converts flat-color artwork into Brother .PES (and .DST) embroidery files entirely in the browser. It is hosted on GitHub Pages and installable as an offline app (PWA).

- Live URL: https://jerlyn.github.io/stitchkit/
- Repo: github.com/Jerlyn/stitchkit (public; Pages deploys from main / root)
- Wireframes: https://claude.ai/artifact/S5hzJ9VEo5ja2XE3FSRt4i
- Analytics: GoatCounter, code `stitchkit`, no cookies. Events: path `export` on each download. Public counter at /counter/export.json needs "Allow adding visitor counts" turned on in GoatCounter settings.
- Footer credit: Made by Design Lady (https://www.designlady.com). Hashtag: #StitchKit. MIT license, Design Lady LLC.

## Files in this project
- `stitchkit/index.html`: the whole app (engine, PES/DST writers, UI, embedded sample bird PNG, SVG badge and monogram samples).
- `stitchkit/sw.js`: service worker. **Bump `VERSION` on every release**, or installed copies keep the old app. Current: `stitchkit-v1.0.3`.
- `stitchkit/manifest.webmanifest`, `stitchkit/README.md` (now has a changelog).
- Icons (binary, not stored here): `icons/icon-192.png`, `icons/icon-512.png`, `icons/maskable-512.png`, `icons/apple-touch-icon.png`. Get them from the repo. Design: navy #020C28 rounded square, cyan #4ADEDE hoop ring, pink #DE6399 dashed running-stitch wave.

## Architecture (inside index.html)
- `<script id="sk-engine">`: `SKE` engine. Its textContent is also loaded into a Web Worker (Blob URL); falls back to the main thread.
  - `load`: estimates distinct colors (ignores alpha < 250), detects a solid border background (exact average color).
  - `quantize(k, removeBg)`: k-means++ in RGB (seed 7), dedupes clusters closer than 28, folds anti-aliasing clusters (tiny, or under 4% and mostly edge pixels) into real colors, 3x3 majority smoothing, drops the background cluster when removeBg.
  - `plan(settings)`: 8-connected components, specks under ~0.6 mm² absorbed; tatami fill along each region's principal axis (moments), underlay at +90° (1.8 mm spacing, 0.4 mm inset), pull compensation extends row ends; greedy row chaining; nearest-neighbor chain and region ordering; travel inside a region up to 8 mm instead of trimming; tie-ins and tie-offs; stitches split at 3.5 mm. Commands: 1 stitch, 2 jump, 3 trim, 4 color change, 5 end; units 0.1 mm, centered on 0,0.
- Writers: `skWritePES` (PES v1 + PEC) and `skWriteDST`, ports of pyembroidery, verified byte-identical on 30 random PES and 15 DST patterns.
- UI: 3 steps (Upload, Adjust threads, Download with success state). State object `S`, undo/redo history of settings snapshots, save/open project as `.stitchkit.json` (embeds the working PNG as a data URL).
- Starter samples (v1.0.3): `SAMPLES` map (bird PNG, `geometric-badge` SVG, `monogram-m` SVG; all original art) feeds `intakeImage`. Buttons are `[data-sample]` in `#samples`.
- Spool cards (v1.0.3): `spool(rgb)` SVG per thread, "Machine color #N" from `skPecName`, stitch-count badge (`.badge[data-count]`, filled by `updateCounts`). Up/down buttons unchanged.
- "Works best with" (v1.0.3): `renderExamples()` runs the main-thread `SKE` once on idle after load (logo = badge SVG, photo = procedural noisy sunset, k = 6), drawn by `drawMini` on natural canvas. Guarded by `intakeActive` so it never shares the main-thread engine with an upload in progress; retries on return to step 1.
- Preview canvas: navy grid mat, Brother-style plastic hoop to scale with left bracket, mm ruler ticks, woven fabric textures, thread sheen in 4 bands by stitch angle plus a shadow, stitch-out player (scrub slider with aria-valuetext, 1x/5x/10x, needle marker, dashed jump lines).
- Defaults: white/near-white backgrounds (luminance > 0.75) are left out automatically; colored backgrounds are stitched. Small images upscale with nearest-neighbor; >1000 px downscale to 1000. Hoop presets 100x100, 130x180, 160x260 (saved in localStorage). Fabric presets: woven, knit, towel, light.

## Quality bar (keep passing)
- axe-core WCAG 2.2 AA: 0 violations on all 3 steps, including step 2 with a thread editor open (fixed in v1.0.3: machine-color select label, Edit/Done button name). 44 px targets, native radios and checkboxes, visible focus, reduced motion respected (hoop animation, drop-zone march).
- No horizontal scroll at 390 px. Sticky preview on desktop (>= 1000 px).
- Offline: SW precaches the app shell; update banner appears only when a new SW is waiting and reloads only when the person chooses Reload.
- Brand palette: #36069A, #DE6399, #020C28, #4ADEDE; neutrals #FFFFFF, #F8F9FA, #212529, #6C757D, #ADB5BD. No em dashes in copy.

## Done in v1.0.3
5. Starter sample gallery: bird, geometric badge, monogram.
6. Thread spool cards with machine color number and stitch-count badge.
7. Live "works best with" examples (flat logo to clean stitches vs photo to muddy result).

## Next up (proposed)
- Nearest-color naming: navy #020C28 maps to "Olive Green #50" because the PEC palette has no true navy and the distance metric favors dark green. Consider a perceptual (CIELAB ΔE) match for the display name only; the PEC chart index must stay pyembroidery-compatible.
- Drag-and-drop reorder with keyboard alternative.

## Later ideas
Satin stitch for thin shapes; split shading into separate threads; file handling and Android share target for the PWA.

## Open checks for Jerlyn
- Stitch a file downloaded from the live site on the SE700 and report results.
- Decide whether the bird sample stays public in the repo.

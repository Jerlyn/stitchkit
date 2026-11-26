# StitchKit

Turn flat-color artwork into a Brother **.PES** (or Tajima **.DST**) machine embroidery file, entirely in your browser.

**Live:** https://jerlyn.github.io/stitchkit/

- **Private by design.** Images are processed on your device. Nothing is uploaded.
- **Built for home machines.** Hoop presets for 4×4 (Brother SE600/SE700), 5×7 and 6×10.
- **Adjustable threads.** Change color count, merge or skip colors, reorder the stitch sequence, and tune row spacing, stitch length, angle, underlay and pull compensation per color.
- **Starter samples.** Try a bird illustration, a geometric badge or a monogram before uploading your own art.
- **Spool cards.** Each thread shows its Brother machine color number and stitch count.
- **Pre-flight check.** Hoop fit, stitch limits, thin details, jump threads and fabric contrast before you download.
- **Works offline.** Install it as an app (Chrome, Edge, Android, or Safari's Add to Home Screen) and convert designs with no connection.
- **Accessible.** Keyboard and screen-reader friendly, WCAG 2.2 AA target, reduced-motion aware.

## How it works
1. Reduces the image to a few thread colors (k-means), cleans anti-aliased edges and absorbs tiny specks.
2. Traces each color region and fills it with tatami rows plus a perpendicular underlay, angled along each shape's long axis.
3. Orders regions and rows to limit jumps, adds tie-in and tie-off stitches, and writes PES v1 (with PEC block) or DST.

The PES, PEC and DST writers are ports of [pyembroidery](https://github.com/EmbroidePy/pyembroidery) (MIT) and were checked byte-for-byte against it.

## Deploy
No build step. Commit everything at the repo root (`index.html`, `manifest.webmanifest`, `sw.js`, `icons/`), then **Settings → Pages → Deploy from a branch → `main` / root**.

### Releasing an update
Bump `VERSION` in `sw.js` (for example `stitchkit-v1.0.1`) in the same commit as your change. Installed copies will show a "new version available" notice and update when the person chooses Reload. If you skip this, installed users keep the old version.

## Analytics
Visits and downloads are counted with [GoatCounter](https://www.goatcounter.com) (no cookies, no personal data). To show the public "designs converted" count on the success screen, enable **Settings → "Allow adding visitor counts on your website"** in GoatCounter.

## Changelog
- **1.0.3:** starter sample gallery (bird, geometric badge, monogram), thread spool cards with machine color number and stitch-count badge, live "works best with" examples (flat logo vs photo) rendered by the engine.

## Limitations (v1)
- Works best with flat art of 2 to 8 colors. Photos and gradients are simplified.
- Fill stitches only. Satin stitch for thin shapes is planned.
- Most home machines do not trim between shapes, so clip jump threads after each color.

## License
MIT © Design Lady LLC. Made by [Design Lady](https://www.designlady.com).

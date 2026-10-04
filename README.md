# SHRED-9000 — 3D interactive paper shredder

A browser paper-shredder simulation. Drag documents into the feed slot, watch the
counter-rotating cutters bite, and see the shredded particles fall and pile up in the
translucent waste bin.

## Run

```bash
python3 -m http.server 8211 --directory ~/Downloads/paper-shredder-3d
```

Then open <http://localhost:8211>. It must be served over HTTP — the page is an ES
module and will not run from a `file://` URL. Also registered in `.claude/launch.json`
as **shredder**.

Two files, no build step and no network access: `index.html` and a vendored
`three.module.min.js` (three.js r160, MIT).

## Controls

| Input | Action |
|---|---|
| Drag a document | Pull it into the slot — it magnets to the mouth when close |
| Drag empty space | Orbit · scroll or pinch to zoom |
| Click the rocker switch / bin handle | Toggle power · pull the bin out |
| `Space` | Feed a fresh A4 sheet |
| `1` `2` `3` | Cut security: P-2 strip · P-4 cross · P-5 micro |
| `P` / `R` | Cycle power · reverse |
| `X` / `C` / `E` | X-ray shell · camera preset · empty bin |

## What is simulated

- **Cutting.** The sheet is clipped at the blade line by a world-space clipping plane
  anchored to how much has actually been eaten, so reversing a half-shredded sheet
  backs out a chewed sheet rather than restoring it.
- **Particles.** Ribbons grow from the cutters and detach at the mode's chip length —
  one mechanism gives 6 mm ribbons, 4 × 42 mm chips, or 2 × 15 mm confetti. Up to 2400
  instanced chips fall with gravity, drag and flutter, then settle on a heightfield
  pile with an angle of repose.
- **Machine state.** Motor RPM and load, thermal duty cycle with cut-out and cool-down,
  jams (by material and cut mode) cleared with reverse, and bin fill — about 19 A4
  sheets to capacity.
- **Materials.** Paper, stapled bundles, credit cards, CDs and cardboard, each with its
  own jam risk, cutter load and bin volume.
- Audio is synthesised at runtime with the Web Audio API — no sound files.

## Notes

- The bin uses ordinary alpha blending rather than `MeshPhysicalMaterial.transmission`:
  the transmission pass rendered the tub solid black here, and blending both looks
  right and costs one less full-scene pass.
- Two point lights sit inside the bin. The tub has a lid, so nothing would otherwise
  light the pile; they stand in for light diffusing through the translucent walls.
- **QUALITY** toggles shadow filtering and pixel ratio if the frame rate needs help.

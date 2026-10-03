# Glimmer Hollow

A cozy pastel low-poly diorama builder for phones, tablets and desktop. One file (`index.html`), three.js r160 loaded from jsDelivr, no build step, no import map, no addons.

## How to play
- **Press and hold** anywhere: a glowing orb follows your finger (or mouse). Loose bricks inside its pickup ring glow, lift and fly to the build site.
- **Move** to sweep up bricks faster. **Hold still** and the ring slowly grows while bricks drift toward the orb, so holding alone always makes progress.
- **Rotate**: hold the round arrow buttons on the left/right edge to spin the view around your buildings (two-finger drag also orbits).
- **🧭 Explore** (top right): one finger drags the town around so you can look at it; tap 🧭 again to fly back to the build site.
- **Two fingers**: drag to orbit, pinch to zoom. One finger never moves the camera. On desktop: left-drag builds, right-drag orbits, wheel zooms.
- Finished buildings get a shop name / family name / landmark name. Tap a building (or pick it in **Town List**) to show its label, then tap the label to rename or 🎲 re-roll.
- **Photo**: frame a building or the whole town, toggle the caption badge, press the shutter. Share sheet where supported, otherwise a download.
- **Menu**: 3 palettes, quality (Auto / Low / Medium / High), reset town.
- Each town holds **30–40 buildings** (the cap is picked per town). Loose bricks never outnumber what the building still needs, so none are left lying around when the last piece lands; the scaffold grows with the building; cobble streets grow toward a building as it goes up.
- The plaza is round or square, and the first building can sit anywhere on it.
- **Menu → My Towns** is your **region**: keep up to 8 saved towns. Every town gets a place on one shared map, so from whichever town you are playing you can see all the others in the distance (toy-block miniatures with name signs), joined by roads. Use Explore (or Region map, or Photo → Region) to look around, tap a distant town to **Visit** it. **Menu → New town** adds another to the region without losing the current one.
- The Menu shows the game **version** (bump `VERSION` in `index.html` each release).
- **Sound** (all synthesised, no audio files): gentle generative music-box/pad music, soft hammering and trowel sounds while a building goes up (busier as bricks land), birds and rustling leaves near a forest, wind and leaves on a hill, flowing water near a river, cow moos and a cowbell on farmland, and footsteps, clip-clopping horse carts and wheel rumble on a road (the road has walkers and carts moving along it). Toggle with the speaker button. Audio starts on your first tap (browser rule); on iPhone/iPad (iOS 16.4+) it plays even with the ringer switch off, on older iOS the silent switch mutes it.
- Progress is saved automatically in `localStorage`.

## Your town
- Buildings are placed organically (not on a grid) and face the nearest street; cobblestone streets link each building to its neighbours.
- A new town sits beside a **river**, a **hill**, a **forest**, **farmland** (crop fields, pastures with cattle, a barn), **another town** or a **road** (random, or choose one in Menu → Reset town). Buildings keep clear of it. Rivers may get an arched wooden bridge; hills may get a winding stone path up to a lookout (open platform or stone gazebo) — all randomised.
- Roofs are built from rows of big overlapping tiles; walls from mixed-size bricks.

## Deploy on GitHub Pages
1. Put `index.html` and `README.md` in the root of a repository.
2. Settings → Pages → *Deploy from a branch* → `main` / `(root)` → Save.
3. Open `https://<user>.github.io/<repo>/`.

## Add a building type
Everything is data in `CONFIG.buildings` (see the `CONFIG` section of `index.html`). Add one object:
```js
chapel: { role: 'landmark', label: 'Chapel', nouns: ['Chapel', 'Hall'], parts: [
  { k: 'walls', w: 3, d: 4, courses: 7, base: 1,
    doors: [{ side: 'front', x: 0, w: 1, h: 4 }],
    windows: [{ side: 'left', x: 0, w: 0.7, y: 3, h: 2 }] },
  { k: 'gable', on: 0, axis: 'z', ov: 0.3, step: 0.5 },
  { k: 'flag', on: 1, n: 3 }
] }
```
- `role`: `home`, `shop` or `landmark` (roles are weighted in `CONFIG.weights`).
- Part kinds: `walls`, `ring`, `gable`, `cone`, `stack`, `awning`, `sign`, `flag`, `boxes` (free-form decorative boxes, e.g. windmill blades). `on` is the index of an earlier part to sit on. `at: [x, z]` offsets a part; `crenel`, `bands`, `base`, `stripes` are optional styling flags.
- Shop types need a `sign` part to show the name board. Landmarks need `nouns` for the name generator.
- Pedestal size, scaffold, scaffolding and garden spacing are derived automatically from the layout.

## Add name word pools
In `CONFIG.names`: `owners`, `surnames`, `streets`, `landmarkPrefix`, `towns`, and `shops` (each shop kind has its own `kind`, `short`, `adj`, `obj`, `brand` pools). Add words to any list, or add a new shop object. Names are seeded from the building, unique within the town, and at most 24 characters.

## Known limitations
- The adaptive quality controller can only see the display's frame interval. On a 60 Hz screen frames can't be shorter than ~16.7 ms, so "fast" means: under 12 ms, or at vsync with low CPU time. GPU time itself is not measurable in WebGL.
- Re-merging buildings and gardens on a Low ↔ Medium switch can cause a short hitch in very large towns.
- Merged buildings use bevelled bricks on Medium/High (about 44 triangles per brick); a very large town photographed in Town mode is heavy for old phones.
- Windows are lit with flat warm glass (no real night lighting). Interiors are hollow.
- Needs an internet connection to fetch three.js from the CDN (host a local copy and change the import URL to go offline).
- Audio only starts after the first touch (browser autoplay rules). On iOS older than 16.4 the ringer/silent switch mutes Web Audio.

## iOS vs Android (photos)
- **iOS Safari 15+**: `navigator.share` with files opens the share sheet → *Save Image*. If Safari rejects the share (user-activation timing), a preview sheet appears: tap **Share / Save** or long-press the picture → *Add to Photos*. Plain downloads land in Files, not Photos.
- **Android Chrome**: the share sheet works on recent versions; older ones fall back to a PNG download in Downloads/Gallery.
- **Desktop**: Chrome/Edge on Windows may open the system share dialog; Firefox/desktop Safari download the PNG.
- Photo resolution is up to 2× the screen buffer, capped at about 8 megapixels and the GPU's texture limit.

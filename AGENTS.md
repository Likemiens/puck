# AGENTS.md: puck

Notes for coding agents changing Puck or putting it into a project.

## What it is

One static page, `index.html`. A three.js hockey puck is shot at the screen, hits the glass, rebounds and can then be spun, and its face carries a logo. There is no build. The only dependency is three@0.184.0 from jsDelivr through an import map. Live: https://puck.likemiens.com

## Inputs

- `?logo=<preset>` takes a name from `PRESETS`: `cubas` (the default), `moai`, `skull`, `clown`, `nerd`, `sob`.
- `?logo=<url>` takes any image address, absolute or relative. It is fetched directly. If that fails, the page retries through `https://wsrv.nl/?url=…&output=png`. Both attempts share a 6 s deadline, and after it the page shows the default preset with a note.
- Something dropped on `window`: a `File` of any image type, or a `text/uri-list` link.
- A pointer drag on the canvas, the arrow keys on the focused canvas, and the buttons in `.dock` (one per preset, plus `.again`).

## Where things are in index.html

| What | Where |
|---|---|
| sizes and timing | the constants at the top of the module: `R H F FLIGHT FAR SHARDS FLAKES` |
| presets | `PRESETS`, entries `[name, title, file]`; the first one is the default |
| image to ink | `inkFrom(img)` holds the colour rules, `decode(blob)` gives SVGs a size, `fetchLogo(src)` resolves presets and does the proxy fallback |
| ink to face textures | `bakeFace(size, draw)`; `placed(art)` fits a logo on the face |
| side texture | `bakeRim(w, h)` |
| lighting | `studio()` |
| motion | `frame()` covers the flight, `hit()`, the springs and the idle sway; `throwAgain()` restarts the shot |
| ice and snow | `burst()` and `fly()`; the snow print on the glass is `frost` |

## Rules of thumb

- Keep it to one file. Textures are generated in the browser, not shipped.
- To change the logo, call `print(placed(art))` and then `throwAgain()`. `use(load, name)` does both, marks the preset button and reports errors.
- Preset files have to come over http(s). A page opened from `file://` cannot fetch them.
- Respect `prefers-reduced-motion`. The code holds it in `calm`: no shot, no idle sway, and the ↻ button is hidden.
- To test the animation frame by frame, stub `performance.now`, `requestAnimationFrame` and `Event.prototype.timeStamp` before the page loads, then step the clock yourself. The demo video was rendered that way.

## Embedding

Put the page in an iframe (example in the README) and pass the logo through `?logo=`. There is no postMessage API.

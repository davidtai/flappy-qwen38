# 488-bare-typical-0.2 raw response

Served worktree SHA: e27618d9698059cbbd2caac55d677c5a5900c779

## Reasoning

The user wants a "ultimate adorable cute and beautiful flappy bird game" in HTML — single file, no external assets, generated assets. I want to make this genuinely polished: canvas-based game, procedurally drawn bird with animation, parallax layered background, day/night or shifting palettes, pipes as cute pipes (maybe as bamboo / candy / cloud pillars), particles, feathers, score, medals, screen shake, sound via WebAudio, mobile touch support, etc.

Let me think about what would feel most alive for this subject:

The most characteristic thing for a game is the game itself — so the page should open with the game canvas front and center, framed like a lovely arcade cabinet, with an ambient background, a HUD with stats, an instruction panel, maybe a "best score" and a "how to play" side panel, and sound toggle.

Let me design:

- Background: layered ambient sky, parallax hills, clouds, floating dandelion seeds / petals, stars appearing at night. A single dark background with one neon accent would feel cold and clinical for this — a warm, soft, multi-hue scheme suits a cute bird better: peach/mint/butter/sky.
- Typography: a distinctive display face plus a readable body face, both from Google Fonts (external font links are fine — "no external assets" refers to images/sounds, and Google Fonts is standard). I'll use "Baloo 2" or "Fredoka" for display, and "Nunito" or "Quicksand" or "Sniglet" for body? A distinctive display face plus a readable body face — I'll use "Baloo 2" (display, chunky rounded) + "Nunito Sans" (body, readable). Actually let me use "Fredoka" (display) and "Nunito" (body).

Let me choose: `@import url('https://fonts.googleapis.com/css2?family=Baloo+2:wght@400..800&family=Nunito:ital,wght@0,300..900&display=swap')`. I want something more playful than generic — "Baloo 2" (very rounded, playful) is a good pick.

Game design details:

Canvas game, fixed logical resolution, e.g. 420x640 (portrait), scaled to fit with devicePixelRatio handling.

Core mechanics:
- Bird physics: gravity ~0.42, flap impulse ~ -7.2, rotation based on velocity.
- Pipes: gap ~ 150-170, spacing ~ 220px, speed 2.6.
- Collision: circle vs rect with some forgiveness.
- Score: +1 per pipe passed, with floating "+1" text and a burst of particles.
- Dead: fall to ground, game over screen with medal, restart.
- Power-ups: maybe a "golden seed" that gives shield? Could be nice but let's keep scope manageable but rich: I'll add a cute "honey drop"?  Let's do something fun: floating "star" collectibles in the gap center sometimes, giving +2 bonus and sparkle burst. And a "bubble" that gives a brief shield? Hmm, that might complicate. Let's do: occasional collectible **cherry/star** in the middle of the gap → +1 bonus + sparkle + combo. Also a "feather" that slows fall briefly (glide) — that's an extra polish item. Let me include one powerup: **golden feather** → 3s of slow-fall + glow trail. Rare.

Actually let's keep it charming but not overcomplicated: pipes = "candy cane reeds"? Let's make obstacles **bamboo stalks**? Or more cute: **sunflower pillars**? I think a cute approach: obstacles are **soft mint "jelly tubes"** with rounded caps and a glossy highlight, plus... Actually let's do **honeycomb / bamboo** — I'll draw them as rounded "marshmallow pipes" with a pastel gradient, a rim, and a cute little leaf/flower on top, plus a slight wobble animation. Plus a background of rolling hills, distant windmills, drifting clouds, fireflies at night.

Day/night cycle: every 10 points, shift palette. That's a nice "alive" touch. Let me implement palette interpolation between 4 palettes: Dawn, Day, Dusk, Night — cycling by score.

Visual extras:
- Parallax: far hills, mid clouds, near hills, foreground grass tufts/flowers.
- Bird drawn procedurally: body ellipse, wing that flaps (rotation/scale), eye with blink, beak triangle, cheek blush, tiny crest feather. Draw with a slight bob.
- Trail particles (small hearts/notes when flapping).
- Screen shake on death, flash.
- Score with bounce animation on increment.
- Ground: scrolling grass with flowers; below is a "ground" line.

UI:
- Overlays in HTML (not canvas) for crispness: start card with title, instructions, "tap / space to flap"; game-over card with score, best, medal, restart button.
- Side panel with stats (best, games played, birds' mood?), keyboard hints, sound toggle.

Layout: I want to avoid the generic centered stack. A game with its canvas centered is natural, but I should do something more interesting. I'll do a two-column layout: left = the game cabinet (canvas in a cute rounded "cabinet" with a marquee and joystick-like details), right = a "field guide" sidebar with stats, leaderboard (local), power-up legend, and a live "sky conditions" readout. Plus ambient background outside the cabinet, and floating decorative elements.

For a game, the most characteristic thing is the game itself — so the page opens with the game cabinet, front and center, with a marquee title and live HUD. I'll make the header a slim bar with the title, a tiny animated bird, and the sound toggle — so the game occupies the top.

Let me also add:
- Keyboard: Space / ArrowUp / W to flap; P to pause; R to restart.
- Mouse click and touch on canvas.
- Pause overlay.
- Sound: WebAudio synthesized — flap (short noise+sine "boop"), score (two-note chime), hit (noise burst + descending), collect (sparkle arpeggio), background music? A gentle looping chiptune melody could be a nice touch — I can write a small sequencer with oscillators. Let me implement a simple ambient/music loop: a pentatonic melody with triangle/sine, plus soft bass. That's a lot of "alive" but risky if it gets annoying. I'll make it toggleable, default on but with clear toggle. Actually default sound on might be jarring — but this is a game; game audio is expected. I'll default to ON but muted until user interaction (AudioContext requires gesture anyway). Music gentle.

Hmm, I need to be careful with performance and correctness. Let me write the code carefully.

Structure:

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset>
<meta viewport>
<title>Flappy Wren</title>
<link fonts>
<style> ... </style>
</head>
<body>
  <div class="ambient"> ... layered bg ... </div>
  <header class="topbar"> ... </header>
  <main class="stage">
    <section class="cabinet"> ... canvas + overlays ... </section>
    <aside class="guide"> ... </aside>
  </main>
  <footer>...</footer>
  <script>...</script>
</body>
```

Let me now write the game logic carefully.

Canvas logical size: W=420, H=680? Let's use 400x640 with a scale factor computed from CSS size. I'll set canvas width/height = logical * dpr and use ctx.setTransform to scale.

Simpler: keep a fixed logical resolution (400x640), and set canvas.width = 400*dpr*... Actually let me do:

```js
const VW = 400, VH = 640;
function resize(){
  const dpr = Math.min(devicePixelRatio||1, 2);
  canvas.width = VW*dpr; canvas.height = VH*dpr;
  ctx.setTransform(dpr,0,0,dpr,0,0);
}
```
And CSS controls the display size (`width: 100%; aspect-ratio: 400/640`). Good — everything drawn in logical units.

Game loop with fixed timestep accumulation:

```js
let last = performance.now(), acc=0;
const STEP = 1000/60;
function frame(now){ let dt = now-last; last=now; if(dt>250) dt=250; acc+=dt; while(acc>=STEP){ update(); acc-=STEP; } render(); requestAnimationFrame(frame);}
```
Update uses fixed step units (1 step = 1/60s). Good.

Game states: 'ready' | 'playing' | 'dying' | 'over' | 'paused'.

Entities:
- bird: {x: 96, y: 320, vy: 0, rot: 0, wing: 0 (phase), alive}
- pipes: [{x, gapY, gapH, passed, hasBonus, bonus:{y, taken, type}, wobble}]
- particles: [{x,y,vx,vy,life,maxLife,type:'heart'|'spark'|'feather'|'star', color, size, rot, vr}]
- floaters: [{x,y,text,life,color}]

Ground: groundY = VH - 88 (play area bottom). Bird dies on hitting ground or ceiling? Ceiling: allow going above but clamp? Classic: dies on hitting top? Actually classic flappy you can fly above screen and come back — but many implementations kill on top. I'll make the top a soft "cloud ceiling" — you bounce off gently with a little "poof" — no, better: keep it simple: hard ceiling at y < -20 not allowed; clamp velocity? Let me do: if bird.y < 40, clamp to 40 and vy = max(vy, 0) with a soft "fluff" — that's forgiving and cute. Actually hitting the top and being blocked is fine and cute. I'll do a soft bounce with a small "boop" sound and a puff.

Pipe generation: start with first pipe at x = VW + 60; spacing 200..240; gapH = 160 → shrink slightly with difficulty to 132; gapY random within [80, groundY - gapH - 80].

Difficulty: speed increases slowly: speed = 2.4 + min(1.2, score*0.03). gap shrinks slightly.

Collision: circle radius ~ 13 for bird at center; check against pipe rects (top rect and bottom rect). Use circle-rect distance.

Pipes drawn as: from x to x+56, top: from y=0 to gapY; bottom: from gapY+gapH to groundY.

Design: I'll draw them as "candy reeds": rounded rect body with vertical gradient, a lighter inner stripe, a "rim" cap at gap edges (thicker rounded rect), and a small flower or leaf on the very top of the top pipe and on the bottom of the bottom pipe? Or a "porcelain" look. Let me do: main body fill with a soft gradient (from palette, e.g., mint or strawberry), plus a subtle diagonal stripe pattern, plus a highlight; and a "cap" — a wider rounded rect at the gap ends with a slightly darker shade + a small heart/flower on it. That's cute.

Bonus collectibles: place in gap center with 35% chance: type 'star' (+2, sparkle), type 'cherry' (+1, heal? no heal needed)... Simplify: 'star' (+2), 'feather' (glide 2.5s). And 'honey' would slow time? Let's do two: star (bonus points) and feather (glide). Plus maybe 'magnet'? No. Two is enough; both rare-ish (25% chance).

Also: on passing a pipe, score +1, spawn hearts, play chime, show floater "+1".

Combo: consecutive pipes passed without... eh, skip. But add "perfect" bonus: if you pass through the exact center of gap, +1 extra and "Perfect!" floater + bigger sparkle. That's a nice touch that rewards precision.

Medals at game over: bronze(10), silver(20), gold(30), platinum(45).

Rendering layers:
1. Sky gradient (palette) + sun/moon with glow + stars at night + drifting clouds (parallax) + distant hills (2 layers) + windmills? Maybe hot-air balloons drifting slowly in the far background — cute and alive. Yes! Occasional hot-air balloon / flying bird silhouettes / clouds with faces? Keep: balloons occasionally, plus distant flock of birds (tiny "v" shapes) crossing.

2. Pipes.
3. Collectibles.
4. Particles (some behind, some in front).
5. Bird.
6. Ground (grass + soil + flowers) + foreground.
7. UI (score at top center, big).

Clouds: pre-generate cloud objects with x, y, scale, speed, puffs; draw as overlapping circles with soft white fill and low alpha.

Ground: scrolling — draw a repeating pattern: soil gradient, grass blades with slight sway, occasional flowers.

I'll pre-generate decor items with positions that recycle.

Let me now write out the palette interpolation:

```js
const SKIES = [
  { name:'Dawn',  top:'#ffd9e8', mid:'#ffe9c9', bot:'#cfe9ff', hill:'#c9a7e8'... }
]
```
Better: define per-palette: skyTop, skyMid, skyBot, sun, cloud, hillFar, hillNear, pipe, pipeDark, pipeLight, accent, ground, grass, night (bool), starAlpha.

Interpolating between palettes: I'll store colors as hex and lerp. Simpler: pick a palette per 12-point "era" and cross-fade over 2 seconds. Simpler still: just switch instantly with a smooth transition — actually a gradual crossfade of the whole sky is nice. I'll implement a `mixHex(a,b,t)` helper and a global `pal` object computed by lerping between current and next palette with a `t` value that increases over time when era changes. That's a good touch and not too hard.

Let me write:

```js
const PALETTES = [ {...}, {...}, {...}, {...} ];
function lerpColor(a,b,t) -> [r,g,b]
function palAt(t) // returns object of rgb arrays
```
Then each frame, era = Math.floor(score/12) % 4; local = (score%12)/12... hmm, smoother: define a continuous palette function over `cycle = (score/12)` and blend between PALETTES[floor] and PALETTES[floor+1] by fractional part — but that means colors change instantly at the start of each 12 and reach the next one at the end. That's actually continuous and nice — smooth gradual transitions. But then the whole thing is always shifting, which is fine and pretty.

Let me do: `const p = Math.min(score, 48)/48` mapped through 4 palettes as a smooth cycle:

```js
const t = Math.min(score, 60)/60; // 0..1 over 60 points
const idx = t * (PALETTES.length-1); // 0..3
const i0 = Math.floor(idx), i1 = Math.min(i0+1, len-1), f = idx - i0;
```
That way after 60 points it's full night and stays. Good — dawn → day → dusk → night as you get better. Nice.

Colors: I'll write them as hex strings and have `mix(a,b,f)` returning `[r,g,b]`, and a helper `rgb(arr)` returning `rgb(...)` string. Since I need to call this each frame for ~12 colors, that's fine.

For performance, I'll cache per-frame: compute palette once per frame (not per draw call). Good.

Bird design — a cute round chick/wren:
- Body: rounded blob, cream/yellow with a soft gradient.
- Belly lighter.
- Wing: a shape that rotates around a shoulder point, angle = sin(wingPhase)*range, drawn as a rounded triangle/ellipse with a darker shade.
- Eye: white ellipse + dark pupil + highlight; blink occasionally (scaleY).
- Beak: small orange triangle, slight bob.
- Cheek: pink blush circle with low alpha.
- Crest: tiny tuft of 3 feathers on head.
- When gliding (feather powerup), wings spread wider + sparkle trail.
- Trail: small puffs.

Draw the bird with rotation = clamp(vy*0.06, -0.5, 1.2) roughly.

Also add a tiny scarf? A little red scarf fluttering behind — cute. Let me do a simple scarf: two bezier bands that trail behind with sine wave. That adds character. Hmm, might be fiddly. Instead: a tiny pink bow on the head + a heart-shaped tail feather. I'll do a tail: two small feather shapes behind the body.

Now, the surrounding page design (HTML/CSS):

Layout: CSS grid, on wide screens: `grid-template-columns: minmax(0, 1fr) 340px` with the cabinet left, guide right. Above, a slim header bar with a title, "insert coin" style blinking, and a live readout.

Ambient background: layered — a soft radial gradient sky, plus CSS-only floating elements (petals, seeds) via JS-generated spans with CSS animations, plus a subtle grid? Let's do: a warm gradient with a repeating pattern of tiny dots (like graph paper) and floating cotton-candy blobs — but I don't want blurry blob circles, which read as generic and muddy; a soft sky with a paper-cut horizon and drifting clouds is more thematic and characterful to a bird game. Let's do a paper-cut horizon: layered hills at the bottom of the page as SVG/CSS shapes plus a sun. That's characteristic and cute, not a generic blurry blob.

Nice idea: page background = soft sky gradient; a horizon made of layered rounded hills (CSS with border-radius ellipses) at the bottom; drifting clouds (CSS keyframes); floating seeds/petals; a few tiny stars always visible? Keep it subtle so it doesn't distract.

Cards: I want to avoid site-wide glassmorphism and over-blurred panels — use solid cream cards with chunky borders (2px solid) + offset shadow (like `box-shadow: 4px 4px 0 rgba(...)` — sticker/scrapbook style). That's a strong, distinctive look matching the cute game aesthetic. Rotated sticker elements, dashed dividers, tape strips.

Micro-interactions: buttons with press-down effect (translate + shadow shrink), hover lift on cards, key hints with keyboard styling, animated stat counters, a "mood" meter, scroll reveals via IntersectionObserver.

Sidebar content:
- "Field Guide" card: bird stats (Speed, Flap power, Gravity) as bars that update live with difficulty.
- "Pickups" legend with tiny canvas-drawn or CSS icons.
- "Live telemetry": score, best, pipes passed, wings flapped, height, etc. — updating in real time. That's "alive".
- "Medals" list with the earned one highlighted.
- "Controls" card with keycap styling.

Header: title "WREN" or "Flappy Wren" — let me name the bird. "Pip". Title: "PIP — A Flappy Tale". Let me use "PIP & THE ENDLESS SKY" or just "Flappy Pip". I'll go with **"PIP"** as display and subtitle "a tiny bird, an enormous sky".

Let me also add an equalizer of "sky conditions" that changes with the era — e.g., "Golden hour", "Bright noon", "Dusk", "Firefly night" — a small badge that updates.

Now let me carefully write the code.

Important details to get right:
- Prevent scrolling on spacebar; preventDefault on keydown for Space/ArrowUp.
- Touch: `touchstart` with preventDefault to avoid double-fire and scroll.
- Pause when tab hidden.
- Restart resets everything.
- Canvas focus: click to flap; also start on first flap.

Let me write the code.

```js
const cvs = document.getElementById('game');
const ctx = cvs.getContext('2d');
const VW = 400, VH = 640;
```

Resize: canvas CSS size set by aspect-ratio; I'll just set width/height attributes to VW*dpr and VH*dpr, with CSS `width:100%; height:auto;` — but then the canvas element's internal resolution ratio must match the CSS aspect ratio; since I set canvas.width = 400*dpr and height = 640*dpr, the aspect ratio is 400/640 = 0.625 and CSS `width:100%; height:auto` preserves it. Good. And setTransform(dpr,0,0,dpr,0,0).

On resize (e.g., devicePixelRatio change), re-init. Simple: call setup once; on window resize, recompute.

Ground and decor:

```js
const GROUND_H = 96;
const PLAY_H = VH - GROUND_H; // 544
```
Pipes bottom at PLAY_H.

Clouds: array of {x,y,s,spd}. Move left; recycle when x < -200 → x = VW + 200 + rand.

Hills: draw procedurally with a function using sin with offset — draw as a filled path across the width: `y = baseY + Math.sin((x + off)*0.01)*amp + Math.sin((x+off)*0.023)*amp2`. Fill to bottom. That gives smooth rolling hills that scroll.

Stars: pre-generated array of {x,y,r,phase}; alpha based on night factor.

Sun/moon: position based on era — move across the sky as night falls? Simpler: a sun that lowers and a moon that rises. I'll compute: `sunY = 90 + nightFactor*160`, and draw a moon at y = 220 - ... hmm. Let me just draw the sun at a position that moves down and to the left, and when dark enough, draw a moon at a different position with a face. Simpler: draw the sun/moon as a single object that transitions: if nightFactor > 0.5, draw a moon (with crater dots and a sleepy face), else draw a sun with rays and a smiley. Nice touch.

Balloons: spawn occasionally, drift slowly, drawn as a cute balloon with a basket.

OK — but I need to keep this from becoming unwieldy. It's fine — a rich build is worth it here. Let me write it in a structured way.

Sound: WebAudio.

```js
let actx = null, master = null, musicGain = null;
function initAudio(){ if(actx) return; actx = new (window.AudioContext||window.webkitAudioContext)(); master = actx.createGain(); master.gain.value = 0.5; master.connect(actx.destination); ... }
function blip(freq, dur, type='sine', vol=0.2, slideTo){...}
```

Music: a simple loop scheduled with a step sequencer using setInterval? Better: schedule notes with a lookahead scheduler using audioContext time. Let me implement a light one:

```js
const melody = [ ... note numbers or null ... ];
let step = 0, nextTime = 0;
function scheduler(){ while(nextTime < actx.currentTime + 0.2){ playNote(...); nextTime += stepDur; } }
```
Called from rAF. That's a clean approach — call `scheduleMusic()` in the render loop.

Melody: pentatonic in C major, gentle. Bass: a low triangle every 4 steps. Keep volume low (0.06) so it's ambient. And only during play? Play always? Let's have music during 'ready' and 'playing', stop on 'over' (or slow down). I'll keep it playing during play and ready, and stop on game over — then a short sad chord plays on death.

Sound effects:
- flap: short square blip 420→300 Hz with quick decay + a tiny noise "whoosh".
- score: two sine notes (880, 1320) — ding-ding.
- perfect: three-note arpeggio + sparkle.
- pickup: high sine arpeggio.
- hit: noise burst + low thud.
- die: descending sine.
- UI click: soft blip.

Noise: create a buffer of white noise once and reuse.

Let me write the code carefully.

Structure:

```js
const P = {
  state:'ready', score:0, best:0, run:0, flaps:0, pipes:0, perfects:0,
  glide:0, shake:0, flash:0, flashColor:'255,255,255', time:0,
  bird:{x:100,y:300,vy:0,rot:0,wing:0,blink:0},
  pipes:[], particles:[], floaters:[], decor:{...}, spawnX:0
}
```

Reset function:

```js
function reset(){
  P.pipes = [];
  P.bird = {x:100, y:PLAY_H/2, vy:0, rot:0, wing:0};
  P.score = 0; ...
  let x = VW + 40;
  while (x < VW + 600) { spawnPipe(x); x += gap(); }
}
```
Actually simpler: spawn pipes when the last pipe's x < VW - spacing. Let me do:

```js
function spawnPipe(){
  const last = P.pipes[P.pipes.length-1];
  const x = last ? last.x + last.w + rand(150, 210) : VW + 60;
  ...
}
// in update: while (P.pipes.length < 6 || P.pipes[P.pipes.length-1].x < VW + 100) spawnPipe();
```
Hmm, condition: keep spawning until last pipe x > VW + 200. Let me do:
```js
while (P.pipes.length === 0 || P.pipes[P.pipes.length-1].x < VW + 120) spawnPipe();
```
Since each new pipe is placed beyond the last, this terminates (each adds ≥150). Good.

Pipe object: {x, w:64, gapY, gapH, passed, items:[{type,y,taken}]}.

Collision: bird circle at (bird.x, bird.y) r=12; for each pipe: if x+w < bird.x-12 skip; if x > bird.x+12 skip (but careful — need to break, sorted by x ascending). Check top rect [0, gapY] and bottom rect [gapY+gapH, PLAY_H] via circle-rect test.

circleRect(cx,cy,r,rx,ry,rw,rh): 
```js
const nx = Math.max(rx, Math.min(cx, rx+rw)), ny = Math.max(ry, Math.min(cy, ry+rh));
return (cx-nx)**2 + (cy-ny)**2 < r*r;
```

Perfect pass: when bird.x > pipe.x + w and !passed: check |bird.y - (gapY+gapH/2)| < 18 → perfect.

Now, the game over: state 'dying' — bird falls, rotates to 90°, until y > PLAY_H - 10 → then 'over' state, show overlay. Play landing sound.

Overlay: an HTML overlay div positioned over the canvas.

Let me now write the HTML/CSS structure.

Header: a slim topbar with a small canvas-drawn sprite? Simpler: a "Pip" wordmark with a tiny animated dot, plus a status pill (live), plus sound toggle and pause button.

Main: grid with `cabinet` and `guide`.

Cabinet: 
- marquee: title with gradient? No — I'll use a solid color with a text-shadow outline for a sticker look, and a tilted sticker badge ("v1.0 · no assets, all love") rotated -6deg.
- The canvas sits in a "screen" with a thick bezel, inner shadow, and a power LED that pulses.
- Under the canvas: a "control deck" with a big round FLAP button (works on touch and mouse), key hints, and a live score readout.

Guide sidebar:
- Card: "Flight Log" — live telemetry table (score, best, pipes, flaps, perfects, altitude) with monospace numbers and small bars.
- Card: "Pickups" — list with tiny inline SVG icons for star, feather — plus the medal list with the earned one highlighted.
- Card: "Controls" — keycaps.
- Card: "Nest Notes" — tips.

Footer: tiny line.

Let me write the code carefully. I'll write everything now in detail.

CSS palette:
- I'll use a candy scheme that changes mood: --sky: #bfe3f2, --cream:#fff6e8, --ink:#2c2438, --pink:#ff8fb1, --butter:#ffd166, --mint:#7fd6b4, --sky:#8ec9ea, --coral:#ff7a59.

Text on cream, ink #2b2140, with accent colors per section.

Let me be careful with contrast: ink #2b2140 on cream #fff6e8 — good.

Font: `Baloo 2` for display (weights 600-800), `Nunito` for body (400/700). Also a mono for numbers — use `ui-monospace, "Courier New"` — I'll add "DM Mono"? Let me use system monospace for stats to limit font loading: `font-family: "Courier New", monospace` for numbers — actually with Baloo, using a monospace for stats gives a nice contrast. I'll load `Baloo 2` and `Nunito` only.

Now the game code. Let me write it out fully.

```js
/* ---------- utils ---------- */
const rand = (a,b)=>a+Math.random()*(b-a);
const clamp = (v,a,b)=>v<a?a:v>b?b:v;
const lerp = (a,b,t)=>a+(b-a)*t;
function hexToRgb(h){h=h.replace('#','');if(h.length===3)h=h.split('').map(c=>c+c).join('');const n=parseInt(h,16);return [(n>>16)&255,(n>>8)&255,n&255];}
function mix(a,b,t){return [lerp(a[0],b[0],t),lerp(a[1],b[1],t),lerp(a[2],b[2],t)];}
function css(c,alpha=1){return `rgba(${c[0]|0},${c[1]|0},${c[2]|0},${alpha})`;}
```

Palettes as arrays of hex, then convert.

```js
const PALETTES = [
 { name:'Sunrise',   sky:['#ffd9e3','#ffe8c4','#c9e8f5'], sun:'#ffd166', hill:['#f6b8d0','#e48fb0'], ... }
];
```
This gets complex; I'll define each palette with the same keys:
```
{ name, skyTop, skyMid, skyLow, cloud, hillFar, hillNear, ground, grass, pipe, pipeDeep, pipeLight, sun, star, night, mood }
```
Where `night` is a 0..1 number for star alpha and darkening.

Let me define:

1. **Dawn / "Sunrise"** — skyTop #ffd9e8, skyMid #ffe9c8, skyLow #d8eefc, cloud #ffffff, hillFar #f3c9e0, hillNear #e59cbf, ground #c98a5e? Let's think about the ground: it's a grassy/soil strip at the bottom; grass green #6fcf8e, soil #a06a42.
2. **Day / "Bright"** — skyTop #a8dcf5, skyMid #d6f0ff, skyLow #eaf7ff, clouds white, hills #a8d8a0/#6cbf86.
3. **Dusk** — skyTop #6a5b9e, skyMid #ff9e7a, skyLow #ffd0a0, hills #7a6a9e / #4d4370.
4. **Night** — skyTop #1e2a5a, skyMid #33407a, skyLow #5a6aa8, stars visible, hills #232a4d/#141a33.

Pipes: I'll make them a consistent candy-mint/pink so they're readable in all palettes, or vary them slightly: pipe #7fd6b4 (mint) with rim #58c398; and a second variant: strawberry #ff9db0 with rim #f2748c; chosen randomly per pipe — that's cute and playful. But with a dark night, bright pipes are fine and readable. Good — keep pipes as fixed palette with 2-3 variants chosen per pipe (mint, pink, butter, sky-blue).

Let me code drawPipe:

```js
function drawPipe(p, pal){
  // body
  const g = ctx.createLinearGradient(p.x, 0, p.x+p.w, 0);
  g.addColorStop(0, p.col); g.addColorStop(0.35, p.colLight); ...
}
```
Simpler: for each pipe, compute base color from a palette array; create gradient with 3 stops: dark edge, light middle, dark edge (cylinder shading). Plus a highlight stripe and a "rim" cap at gap edges (thicker rounded rect), plus a small flower or leaf on the very top of the top pipe and on the bottom of the bottom pipe? Or a "porcelain" look. Let me do: main body fill with a soft gradient (from palette, e.g., mint or strawberry), plus a subtle diagonal stripe pattern, plus a highlight; and a "cap" — a wider rounded rect at the gap ends with a slightly darker shade + a small heart/flower on it. That's cute.

`roundRect` — use ctx.roundRect if available, else fall back. I'll write a helper `rr(x,y,w,h,r)` that uses a manual path to be safe (avoid relying on ctx.roundRect — actually it's widely supported now in modern browsers, but I'll write my own to be safe).

Collectible: star — a 5-point star with a glow, rotating; feather — a small feather shape that bobs; both drawn with a soft glow and a pulsing ring.

Now, let me carefully write the whole thing. I'll aim for correctness over cleverness.

Detailed code plan:

```js
const GAME = {
  state: 'ready',
  score: 0, best: 0,
  pipes: [], particles: [], floaters: [],
  bird: null,
  spawnTimer: 0,
  shake: 0, flash: 0,
  time: 0,
  stats: {flaps:0, passed:0, perfects:0, pickups:0, maxAlt:0}
};
```

reset():
```js
function resetGame(){
  GAME.score = 0;
  GAME.pipes = [];
  GAME.particles = [];
  GAME.floaters = [];
  GAME.bird = {x:104, y:280, vy:0, rot:-0.2, wing:0, wingV:0, glide:0};
  GAME.stats = {flaps:0, passed:0, perfects:0, pickups:0, started:0};
  GAME.shake = 0; GAME.flash = 0;
  let x = 430;
  while (x < 900) { addPipe(x); x += 190 + Math.random()*40; }
}
```
Hmm, but spawn condition: I'll generate pipes with a `lastX` variable stored in GAME.

```js
function addPipe(){
  const last = GAME.pipes[GAME.pipes.length-1];
  const x = last ? last.x + last.w + rand(150, 205) : 470;
  const gapH = clamp(168 - GAME.score*0.8, 128, 168);
  const gapY = rand(70, PLAY_H - gapH - 70);
  ...
}
```
And in update: `while (GAME.pipes[GAME.pipes.length-1].x < 520) addPipe();` plus cull: `GAME.pipes = GAME.pipes.filter(p => p.x + p.w > -60);`

Wait, careful: if pipes are culled and then addPipe uses the last one's x — since new ones are added beyond the last, and the last one is always > 520 after the loop, culling only removes from the front. Fine.

The `gapH` should perhaps depend on difficulty — I'll use `GAME.score` and also `GAME.speed`.

Speed: `const speed = 2.3 + Math.min(1.3, GAME.score*0.028);`

When not playing (ready state), pipes still scroll? In classic, the bird sits still and pipes aren't there yet. I'll have the ready state show the bird bobbing in place with no pipes, and a "tap to start" hint; on the first flap, state → playing, and pipes are created. Let me make reset() create pipes and then start with the first pipe off-screen — that works: pipes start at x=470 and above, so they scroll in.

Let me write the update function:

```js
function update(){
  GAME.time++;
  const b = GAME.bird;
  if (GAME.state === 'playing'){
    const speed = ...;
    // move pipes
    for (const p of GAME.pipes) p.x -= speed;
    // cull & spawn
    // bird physics
    b.vy += 0.42;
    if (b.glide > 0){ b.glide--; b.vy = Math.min(b.vy, 1.6); /* spawn sparkle */ }
    b.y += b.vy;
    b.rot = clamp(b.vy*0.07, -0.5, 1.3);
    b.wing -= 0.3;
    // ceiling
    if (b.y < 26){ b.y = 26; if (b.vy < 0) { b.vy = 0.6; sfx('boop'); puff(); } }
    // ground
    if (b.y + 12 > PLAY_H){ b.y = PLAY_H - 12; b.vy = 0; b.rot = 1.5; GAME.state = 'dying'; GAME.dieT = 0; }
    // pipe collisions
    ...
  }
}
```

Wait — if the bird hits the ground, we go to 'dying' but it's already on the ground; better to have `dying` when hitting a pipe (bird continues to fall until it hits the ground, then 'over'). Let me restructure:

```js
function kill(){ if (GAME.state !== 'playing') return; GAME.state = 'dying'; GAME.shake = 16; GAME.flash = 0.7; sfx('hit'); puff(); }
// in update: if (GAME.state === 'dying'){ b.vy += 0.5; b.y += b.vy; b.rot = Math.min(b.rot + 0.06, 1.6); if (b.y + 12 >= PLAY_H){ b.y = PLAY_H-12; GAME.state = 'over'; showOver(); } }
```
And if the bird hits the ground directly while playing → set to dying and it'll immediately land → 'over' next frame. That's fine, but to be safe I'll check `if (b.y + 12 >= PLAY_H) { b.y = PLAY_H - 12; if (GAME.state==='dying') { GAME.state='over'; showOver(); } }`.

Also during 'dying', pipes keep moving? In classic, everything stops. I'll stop pipes but keep the bird falling — cleaner.

The 'ready' state: bird bobs: `b.y = 280 + Math.sin(GAME.time*0.06)*10; b.wing -= 0.15;` and on flap → playing.

Particles: update with gravity, fade, rotate.

Floaters: text floats up and fades.

Now, `flap()`:
```js
function flap(){
  if (GAME.state === 'ready'){ GAME.state = 'playing'; startMusic(); }
  if (GAME.state !== 'playing') return;
  GAME.bird.vy = -7.1;
  GAME.bird.wing = 0; // reset wing phase
  GAME.stats.flaps++;
  spawn hearts/puff behind;
  sfx('flap');
}
```
Also allow flap during 'over' to restart? I'll make the over screen show a button, plus clicking the canvas restarts.

Handle input: `onPointerDown` on canvas → if state==='over' and (time since over > 0.6s) → reset; else flap().

I'll write `inputAction()`.

Score display: draw the score in the canvas as a big number with a stroke outline, plus in the HTML HUD.

Now, let me think about drawing the HUD in the canvas — I want it to be readable and cute. I'll draw the score in the canvas itself (classic), and also in the HTML HUD. I'll keep the canvas score (it's the classic look) and use the HTML sidebar for stats like "best" and "flaps".

Let me draft the drawing functions:

`drawSky(pal)`:
- fill a gradient from skyTop to skyLow.
- Stars: if pal.night > 0.05, draw stars with twinkle.
- Sun/moon.
- Clouds.
- Far hills, near hills.
- Balloons (behind pipes).

`drawHills(color, baseY, amp, freq, phase, detail)`:
```js
function hillPath(c, baseY, amp, freq, phase, detail){
  ctx.beginPath();
  ctx.moveTo(0, VH);
  for (let x=0; x<=VW; x+=8){
    const y = baseY + Math.sin((x+phase)*freq)*amp + Math.sin((x+phase)*freq*2.3)*amp*0.4;
    ctx.lineTo(x, y);
  }
  ctx.lineTo(VW, VH); ctx.closePath();
}
```

`drawGround()`: soil gradient + grass top + grass blades + flowers.

Clouds: I'll pre-generate cloud objects with x, y, scale, speed, puffs; draw as overlapping circles with soft white fill and low alpha.

`drawPipes()`: as described.

`drawCollectibles()`.

`drawBird()`: 

```js
function drawBird(){
  const b = GAME.bird;
  ctx.save();
  ctx.translate(b.x, b.y);
  ctx.rotate(b.rot);
  // shadow
  // tail
  // wing back
  // body
  // wing front
  // face
  ctx.restore();
}
```

Body: 
```js
// body
ctx.fillStyle = '#ffd93d'; // yellow chick
ctx.beginPath(); ctx.ellipse(0,0,15,13,0,0,Math.PI*2); ctx.fill();
```
Then a lighter belly ellipse, an outline in dark brown (#5a3b21) with lineWidth 2 for that sticker look — I'll draw with outlines for a clean, cute, sticker look. Good — outline everything (2px dark).

Wing:
```js
const wa = Math.sin(b.wing) * 0.9; // angle
ctx.save(); ctx.translate(-2, -3); ctx.rotate(wa);
ctx.fillStyle = '#ffe98a'; ctx.strokeStyle = '#5a3b21';
ctx.beginPath(); ctx.ellipse(-6, 0, 10, 6, 0, 0, Math.PI*2); // wing pointing back
ctx.fill(); ctx.stroke(); ctx.restore();
```
Wing pivot at shoulder; wing extends backward. Good.

Eye: at (5, -5): white circle r 5, pupil at (6.5,-5) r 2.5, highlight white r 1.2. Blink: scale the eye vertically.

Beak: at (13, 0): triangle from (12,-2) to (20,0) to (12,3) — orange #ff9f43 with outline.

Cheek: pink circle at (6, 3) r 3.5, alpha 0.5.

Crest: two small lines from top of head.

Gliding: if b.glide > 0, draw a sparkle trail and hold the wing out.

I'll add a small trail: each frame during play, push a small particle with low alpha — a "wind streak." I'll spawn a couple of small white circles behind the bird.

OK — one more thing I love: a blinking "Zzz" in the ready state — the bird blinks and small "z" glyphs float up, and the hint text says "tap to start." It's a nice touch that shows the bird is alive.

Now let me write the code.

Sound synthesis — let me write:

```js
let AC = null, master = null, musicBus = null, noiseBuf = null;
function audio(){ if (AC) return AC; try { AC = new (window.AudioContext||window.webkitAudioContext)(); } catch(e){ return null; } 
  master = AC.createGain(); master.gain.value = 0.55; master.connect(AC.destination);
  musicBus = AC.createGain(); musicBus.gain.value = 0.28; musicBus.connect(master);
  noiseBuf = ...; return AC; }
```

`function tone({f=440, f2, type='sine', dur=0.15, vol=0.3, dest, when=0, attack=0.01})`:
```js
function tone(o){
  const ac = audio(); if(!ac || muted) return;
  const t0 = ac.currentTime + (o.when||0);
  const osc = ac.createOscillator(); const g = ac.createGain();
  osc.type = o.type||'sine';
  osc.frequency.setValueAtTime(o.f, t0);
  if (o.f2) osc.frequency.exponentialRampToValueAtTime(Math.max(30,o.f2), t0+(o.dur||0.15));
  g.gain.setValueAtTime(0.0001, t0);
  g.gain.exponentialRampToValueAtTime(o.vol||0.3, t0+0.012);
  g.gain.exponentialRampToValueAtTime(0.0001, t0+(o.dur||0.15));
  osc.connect(g); g.connect(o.dest||master);
  osc.start(t0); osc.stop(t0+(o.dur||0.15)+0.05);
}
function noise(dur=0.2, vol=0.3, freq=1000, when=0){
  const ac = audio(); if(!ac||muted) return;
  const src = ac.createBufferSource(); src.buffer = noiseBuf; src.loop = true;
  const f = ac.createBiquadFilter(); f.type='lowpass'; f.frequency.value = freq;
  const g = ac.createGain();
  const t0 = ac.currentTime + when;
  g.gain.setValueAtTime(vol, t0); g.gain.exponentialRampToValueAtTime(0.001, t0+dur);
  src.connect(f); f.connect(g); g.connect(master); src.start(t0); src.stop(t0+dur+0.02);
}
```

SFX:
- flap: tone({f:300,f2:180,type:'triangle',dur:0.09,vol:0.16}) + noise(0.08, 0.12, 2400).
- score: tone({f:880,dur:0.1,vol:0.22,type:'square'}) then tone({f:1318,dur:0.14,vol:0.18,when:0.07}) — a classic two-note ding.
- perfect: arpeggio 880, 1108, 1318, 1760, triangle, with sparkles.
- pickup: 660→1320 sine glide + sparkle.
- hit: noise(0.25, 0.5, 700) + tone({f:180,f2:60,type:'sawtooth',dur:0.3,vol:0.25}).
- die: descending square arpeggio.
- over: soft minor chord.

Music: 
```js
const MELODY = [0,4,7,4, 12,7,4,0, 2,5,9,5, 7,4,0,-1, ...]; // -1 = rest
```
In semitones from base C=261.63. Let me instead write a small pentatonic random walk — actually, a fixed pleasant pattern is better. A simple, reliable pattern:
```js
const MELODY = [
  0, 4, 7, 4, | 9, 7, 4, 0, | 2, 5, 9, 5, | 7, 2, 0, -1,
  5, 9, 12, 9, | 7, 4, 2, 0, | 4, 7, 11, 7, | 5, 4, 0, -1
];
```
These are semitone offsets from C4; with a C-major-ish pentatonic (0,2,4,5,7,9,11) — fine.

Bass: on every 8th step, play a low note at [0,0,5,5,7,7,3,3] pattern. Keep simple: bass = [0,0,-1,0,5,5,-1,5, 7,7,-1,7, 3,3,-1,3] aligned to 16 steps.

Note: keep it simple and cute. `scheduleMusic()`:
```js
function scheduleMusic(){
  if (!AC || muted || GAME.state==='over') return;
  while (musicNext < AC.currentTime + 0.3){
    const step = musicStep % MELODY.length;
    const n = MELODY[step];
    if (n >= 0){ const f = 261.63 * Math.pow(2, n/12); tone({f, type:'triangle', dur:0.22, vol:0.1, dest: musicBus, when: musicNext - AC.currentTime}); }
    const b = BASS[step % BASS.length];
    if (b >= 0){ const f = 130.81 * Math.pow(2, b/12); tone({f, type:'sine', dur:0.3, vol:0.16, dest: musicBus}); }
    musicStep++; musicNext += 0.19;
  }
}
```
Since I schedule with `when: musicNext - AC.currentTime` — as long as that's ≥ 0 and positive, good.

Only play music during play? I'll schedule it when state is playing or ready, but during 'ready' the tempo can be slow. Keep it simple: music always plays (with mute toggle), but stop on 'over' (with a little "sad" sting).

Music could get annoying, so I'll make it quiet and gentle and default the toggle to ON — with a mute button easily accessible.

HTML overlay:

```html
<div class="overlay" id="overlay">
  <div class="card-over">
    <div class="stamp">…</div>
    <h3>…</h3>
    <div class="score-row">…</div>
    <button>…</button>
  </div>
</div>
```

The overlay for "ready" — I'll keep it light so the game stays visible: show "tap / space to flap" and a bouncing arrow; when playing, hide the overlay.

For game over: an overlay with a medal drawn in... an inline SVG? I'll make a small `<canvas>` for the medal and draw it, or CSS with a conic-gradient circle — I'll use inline SVG for the medal, and rotate the whole card in with a keyframe.

I want to be careful about the total length of this. I'll write it out in full but keep it dense and purposeful.

Additional polish — I'll add a "screen shake" by translating the canvas context, and a "flash" rect on top.

For the "power on" effect: when the game starts, draw the canvas with a slight fade-in from white — I'll add that to the overlay.

Now — let me also handle the case where the game is paused (P key) — show a pause overlay with a cute "nap" message and "Zzz" bubbles.

Also add: `visibilitychange` → pause.

I also want to add: a live "altitude" readout — a small bar showing the bird's height in the sidebar, plus the trail of the current run? That could be a canvas — a "flight path" sparkline! Nice idea: a small canvas in the sidebar showing the bird's height over time — a live "flight recorder". A nice living element, and I'll draw it as a small line chart with the bird's y-position, updating as you play. Let me do that — it's cheap and adds life.

OK, now writing the final code. Let me carefully produce the code.

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>Pip · a tiny flappy tale</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Baloo+2:wght@500;600;700;800&family=Nunito:ital,wght@0,400;0,600;0,700;1,400&display=swap" rel="stylesheet">
<style> ... </style>
</head>
<body>
 <div class="sky-bg"> ... </div>
 <header>...</header>
 <main>...</main>
 <footer>...</footer>
</body>
</html>
```

Let me write the CSS with care.

Root variables:
```css
:root{
  --ink:#2b2140;
  --ink-soft:#5d5375;
  --paper:#fff8ec;
  --paper-2:#ffefd6;
  --pink:#ff7fa8;
  --butter:#ffc93c;
  --mint:#54cfa4;
  --sky:#63b8e8;
  --coral:#ff6f4d;
  --shadow: 0 18px 40px rgba(43,33,64,.16);
}
```

Body background: layered —
```css
body{
  background:
    radial-gradient(1200px 600px at 15% -10%, #fff3c9 0%, transparent 60%),
    radial-gradient(900px 500px at 90% 10%, #cdeeff 0%, transparent 55%),
    linear-gradient(180deg, #ffe9f1 0%, #e7f4ff 45%, #d9f0e4 100%);
}
```
Plus a repeating dot pattern overlay via a pseudo-element with low opacity, plus floating decor spans (petals/seeds).

I'll make the whole page feel like a scrapbook — rotated sticker cards, dashed borders, tape strips, and floating petals — so it doesn't read as a flat, static layout.

Header: a bar with the wordmark "PIP" in huge Baloo with a stroke outline, a rotating "chirp" ticker, and buttons.

I'll do a top bar: left = wordmark + tagline; right = [sound toggle][pause][best score pill]. And then immediately the game (the characteristic thing), with the guide to the right.

On mobile: single column, game first, then guide cards.

Let me write the code now. I'll write out the details.

For the "flight path" canvas in the sidebar: `#spark` canvas 300x70, drawing the last 120 samples of the bird's height, updated each frame (or every 3 frames).

Let me think about the `drawStar` helper:
```js
function starPath(cx, cy, r1, r2, n, rot){
  ctx.beginPath();
  for(let i=0;i<n*2;i++){
    const r = i%2 ? r2 : r1, a = rot + i*Math.PI/n;
    const x = cx + Math.cos(a)*r, y = cy + Math.sin(a)*r;
    i ? ctx.lineTo(x,y) : ctx.moveTo(x,y);
  }
  ctx.closePath();
}
```

Particles: types 'heart', 'star', 'puff', 'feather', 'spark'. Draw:
- heart: small heart path, pink.
- star: use starPath.
- puff: white circle fading + growing.
- spark: a 4-point sparkle (two crossing lines) — I'll draw a diamond.
- feather: a small ellipse with a line.

Let me write a `burst(x,y,type,n)` helper.

Now the code for the ready state: bird hovers on a branch? Cute idea: in the ready state, the bird sits on a small branch/stump at the left. Simpler: it hovers with bobbing. I'll do a little wooden sign at the bottom-left that says "TAP!" — draw a small signpost. Nice touch, keep it small.

Also draw a "wind" hint — I'll skip that to avoid clutter.

OK. Time to write the actual code. Let me carefully produce the code.

One risk: `ctx.filter` — I'll avoid it. I'll use `shadowBlur` sparingly (a handful of calls per frame is fine).

Let me write the final code now. I'll be careful about correctness.

One more consideration: the overlay must not block canvas pointer events when hidden (pointer-events: none when hidden).

For the game-over overlay, I need to populate: score, best, medal, "new best!" badge, and buttons: "Fly again" (or press R / Space).

Let me write out the code.

```js
// ---------- boot ----------
const cvs = document.getElementById('game');
const ctx = cvs.getContext('2d', {alpha:false});
```
alpha:false is fine since I fill the whole sky.

Set up:
```js
function fit(){
  const dpr = Math.min(window.devicePixelRatio || 1, 2);
  cvs.width = Math.round(VW*dpr); cvs.height = Math.round(VH*dpr);
  ctx.setTransform(dpr,0,0,dpr,0,0);
  ctx.imageSmoothingEnabled = true;
}
```
Called once at load (and on resize; though CSS size doesn't change the logical size, dpr might change on zoom — I'll call it on resize with a guard).

OK now let me write everything for real. I'll produce the final answer with the complete file.

Let me carefully write out the drawing code for pipes:

```js
function drawPipePair(p, pal){
  const [c1,c2,c3] = p.shade; // dark, base, light
  const gapTop = p.gapY, gapBot = p.gapY + p.gapH;
  // top pipe body
  pipeBody(p.x, -20, p.w, gapTop + 20, p); // from above screen to gapTop
  pipeBody(p.x, gapBot, p.w, PLAY_H - gapBot + 10, p);
  // caps
  pipeCap(p.x - 6, gapTop - 26, p.w + 12, 26);
  pipeCap(p.x - 6, gapBot, p.w + 12, 26);
}
function pipeBody(x,y,w,h,p){
  const g = ctx.createLinearGradient(x,0,x+w,0);
  g.addColorStop(0, p.shade[0]); g.addColorStop(0.22, p.shade[2]); g.addColorStop(0.5, p.shade[1]); g.addColorStop(0.8, p.shade[2]); g.addColorStop(1, p.shade[0]);
  ctx.fillStyle = g; ctx.fillRect(x, y, w, h);
  // stripes
  ctx.save(); ctx.beginPath(); ctx.rect(x,y,w,h); ctx.clip();
  ctx.globalAlpha = 0.10; ctx.fillStyle = '#fff';
  for (let i = 0; i < 6; i++){ const sx = x + ((GAME.time*1.2 + i*30) % (w+40)) - 20; ... }
  ctx.restore();
}
```
Diagonal stripes scrolling — a nice detail but maybe too much. Let me do a simpler approach: a soft radial highlight. Simpler and nicer:
- Base: vertical gradient with three stops → cylinder look.
- Then a few subtle vertical stripes? Let me do: base gradient + a single wide white highlight band at x+0.18w with alpha 0.35 + a soft dark band at the right edge. Plus a dotted "speckle" pattern? Skip.

Cap: rounded rect with a slight gradient and a small dark outline, and a little leaf/flower at the gap edge.

For the "cap":
```js
function cap(x, y, w, h){ // y = top edge of the cap
  ctx.fillStyle = ...; rr(x, y, w, h, 8); ctx.fill();
  ctx.strokeStyle = 'rgba(43,33,64,.35)'; ctx.lineWidth = 2; ctx.stroke();
}
```
And on top of the top pipe's cap, draw a small flower (5 petals) — cute. And on the bottom pipe's cap bottom, draw grass tufts.

Let me make the pipes bamboo-ish: each pipe has 3-4 segments with a horizontal line at each and a small leaf at the top. A bamboo-style pipe is easy and cute:
- Draw the body as a rounded rect with vertical gradient (cylinder).
- Draw 2-3 horizontal band lines every ~70px (a darker band with a lighter line).
- Add a cap at the gap edge.

And I'll also add a small leaf at the top of the top pipe — cute.

Let me finalize and write the code. I need to keep it reasonable — I'll go with bamboo + caps + flowers.

Let me write it now — final answer. I'll write the whole file carefully.

Note on `PLAY_H` — pipes only exist in the sky area; ground is at the bottom.

Ground rendering:
```js
function drawGround(pal){
  const y = PLAY_H;
  ctx.fillStyle = pal.soil; ctx.fillRect(0, y, VW, GROUND_H);
  // grass top
  ctx.fillStyle = pal.grass; ctx.fillRect(0, y, VW, 14);
  // grass blades
  ctx.save();
  for (let i = 0; i < 40; i++){
    const bx = ((i*23 - GAME.time*1.2) % (VW+40) + (VW+40)) % (VW+40) - 20;
    ...
  }
  ctx.restore();
}
```
Simpler: draw grass blades with fixed offsets computed from a seed array and offset by `GAME.time * speed`.

I'll create a `decor` array in reset: `blades = Array.from({length:60},()=>({x:Math.random()*VW*1.5, h:6+Math.random()*10, kind:Math.random()}))` and scroll them: `x = (b.x - GAME.time*speed) % (VW*1.5)` — but the speed changes over time so the modulo approach breaks. I'll scroll by subtracting the speed each frame — cleaner:
```js
for (const b of decor.grass){ b.x -= speed; if (b.x < -20) b.x += VW + 40; }
```
Same for clouds, flowers, etc. Since scroll speed changes, subtracting per frame is correct.

But during 'dying'/'over' nothing moves, so I'll scroll with speed 0 when not playing — but on the ready screen I want the clouds to drift — I'll give clouds their own speed and keep them going during 'ready' and 'playing' but not during 'dying'. I'll write `scrollActive = (state === 'playing' || state === 'ready')` and use that for decor; grass scrolls with the pipe speed, clouds with their own speed.

OK, writing now. I'll keep the code as tight as I can while keeping it rich.

Let me also do the "flight path" sparkline in the sidebar — a small canvas; I'll keep a `history` array of {y, t} for the last 120 samples, pushed each frame during play.

Now, the code.

One more detail: the "ready" state with the bird hovering — the sidebar will show "waiting for takeoff" and the game will show a hint; the bird sits on a small branch/stump and a big "TAP" prompt. Let me draw the bird sitting on a branch during ready — actually, simpler: it hovers with bobbing and a "z z z" text? I'll do the bird blinks and small "z" glyphs float up — cute and shows the bird is alive. I'll add a `zTimer` in the ready state that spawns `z` particles — I'll use a text particle type. I'll support `type:'text'` with a `text` field.

OK writing the final code.

For the overlay cards, I'll design:

**Ready overlay:**
```
┌───────────────┐
│   🐣 TAP!      │
│  space / click │
│  to flap       │
└───────────────┘
```
With a pulsing arrow and a small bird icon.

**Game over overlay:**
```
FLIGHT OVER
[medal]  SCORE 24
         BEST  31
"You flew like a startled spoon." — random quip
[ Fly again ]
```
Random quips — cute and characteristic. Let me write ~8 quips:
- "A valiant effort. A dignified bonk."
- "Physics: 1, Bird: 0."
- "You peaked. Literally."
- "That pipe came out of nowhere. (It did not.)"
- "Gravity remains undefeated."
- "Pip is fine. Pip is grounded."
- "Certified cloud-adjacent."
- "You flew like a confused brick."

Also show the medal name and a "New best!" badge.

And the pause overlay: "Nap time — press P to resume."

Let me finalize and write it all out. I'll be careful about correctness.

For the "power on" effect: when the game starts, show a "screen power-on" flash — I'll do a quick white flash fade in the canvas when the round starts.

Regarding the sidebar "live" values: I'll update DOM text every frame but only when values change (to avoid layout thrash). I'll write a helper `setTxt(el, v){ if(el.textContent !== v) el.textContent = v; }`.

Speed/difficulty bars: update the width of a bar via style.

For the sidebar "Pickups" list, I'll use inline SVG icons: star, feather, and a heart.

OK. Writing the final answer now. I need to be careful with correctness.

One risk: `ctx.filter` — I'll avoid it. I'll use `shadowBlur` sparingly.

The "flight path" sparkline canvas — draw with a small function each frame (cheap: 120 points). I'll draw the trail of the bird's height plus a horizon line.

Time to write. I'll also include a `<noscript>`? Not needed.

Let me write the CSS fully:

```css
*{box-sizing:border-box}
html,body{margin:0;padding:0}
body{
  font-family:'Nunito',system-ui,sans-serif;
  color:var(--ink);
  background: ...;
  min-height:100%;
  overflow-x:hidden;
}
```

Sticker card style:
```css
.card{
  background:#fffdf6;
  border:2px solid var(--ink);
  border-radius:18px;
  box-shadow:0 4px 0 rgba(43,33,64,.14), 0 18px 30px rgba(43,33,64,.12);
  padding:18px;
  transition:transform .25s cubic-bezier(.34,1.56,.64,1), box-shadow .25s;
}
.card:hover{transform:translateY(-4px) rotate(-.4deg); box-shadow:0 8px 0 ...}
```

Reveal on scroll: `.reveal{opacity:0; transform:translateY(18px)} .reveal.in{opacity:1;transform:none;transition:...}`

Header wordmark with `text-shadow` outline:
```css
.wordmark{font-family:'Baloo 2';font-size:clamp(38px,6vw,64px);font-weight:800;letter-spacing:-.02em;
  color:var(--butter); -webkit-text-stroke:2px var(--ink); paint-order:stroke fill; text-shadow:0 4px 0 rgba(43,33,64,.2)}
```
`paint-order: stroke fill` works on text in modern browsers. Good.

For the floating petals in the background: I'll create ~14 spans with random positions/durations via JS, each with a CSS animation of falling/drifting. Use `will-change: transform` and keep the count low to avoid jank.

I'll write a `seedPetals()` function that creates spans in `#ambient` with random `left`, `--d` duration, `--delay`, and content (🌸? no emoji — I'll use small CSS shapes). I'll use small CSS petals (rotated ellipses) — a `div` with a background and border-radius — to keep to "no external assets." I'll use a few `<i class="petal">` with radial-gradient backgrounds and a `drift` keyframe. Good.

Also a couple of CSS clouds drifting across the page background — nice ambient. I'll make 3 clouds with border-radius blobs and slow translateX animations.

For layout:
```css
.stage{display:grid;gap:22px;grid-template-columns:minmax(0,1fr);}
@media (min-width:900px){ .stage{grid-template-columns:minmax(360px,460px) minmax(300px,1fr)} }
```
For the guide, I'll use a two-column masonry-ish auto-fit grid so it doesn't read as a plain row of identical cards: `repeat(auto-fit, minmax(220px,1fr))`.

Cabinet column: the game; guide: cards in a responsive grid.

Let me write the guide cards to have distinct types (not all the same):
1. Flight Log (stats + live bars) — wide.
2. Flight path (sparkline) — full width, with an altitude readout.
3. Pickups — two items with icons.
4. Medals — list with dots.
5. Controls — keycaps.
6. Pip's mood / Tips — a quote card.

Different sizes, different colors, a few rotated — good variety.

Final check on the canvas sizing: I'll set the canvas wrapper to `aspect-ratio: 5/8` (400/640 = 5/8 exactly) with `width:100%` and let the canvas fill it.

I'll use `<canvas id="game" width="400" height="640">` and let the JS resize to the device pixel ratio; CSS sets `width:100%; height:auto; display:block;`.

Let me write the code now — final answer. I'll write everything carefully, then double-check the logic.

One thing to double check: `ctx.setTransform(dpr,...)` — then all subsequent draws are in logical coords. But I use `ctx.save()/restore()` inside draw functions — the base transform is preserved via save/restore, so it's fine. Since I always `save()` before `translate()` and `restore()` after, that's fine.

Now writing final code.

For the game-over overlay, I need to populate: score, best, medal, "new best!" badge, and buttons.

Let me write `showOver()`:
```js
function showOver(){
  sfx('over');
  const medal = medalFor(GAME.score);
  ...
  overlay.classList.add('show');
  // update stats card
}
```

And `startGame()` hides the overlay and resets.

I'll keep the game in the ready state initially and show the ready overlay with the start button; the first flap starts the game — clicking the canvas during 'ready' both starts and flaps. Good.

Let me make sure the first pipe doesn't spawn too close: initial `GAME.pipes = []` and `addPipe()` uses x = 470 when empty — so the first pipe appears at 470 and the bird is at 104 — 366px of runway, about 150 frames at 2.4 → ~2.5s. Good.

Let me write the code in full now.

Also, `drawText` in canvas for the score: I'll use `ctx.font = '800 44px "Baloo 2", sans-serif'` — this works if the font is loaded; it should be since we load it. If not loaded, fallback to sans-serif. Fine.

The pause overlay needs to be a separate card. I'll use one overlay div and fill it with different content via a helper that builds HTML.

Let me write the final code now. I'll aim for correctness over cleverness.

```js
function showOverlay(html){ overlay.innerHTML = html; overlay.classList.add('show'); }
function hideOverlay(){ overlay.classList.remove('show'); }
```

For the game-over content:

```js
function gameOverHTML(){
  const m = medalFor(GAME.score);
  const q = quips[...];
  const isBest = GAME.score >= GAME.best && GAME.score > 0;
  return `<div class="ov-card">
    <div class="ov-ribbon ${isBest?'newbest':''}">${isBest?'NEW BEST!':'flight over'}</div>
    <div class="ov-medal">${medalSVG(m)}</div>
    <div class="ov-quip">${q}</div>
    <div class="ov-scores">
      <div><span>score</span><b>${GAME.score}</b></div>
      <div><span>best</span><b>${GAME.best}</b></div>
    </div>
    <button class="btn" id="again">fly again</button>
    <p class="ov-hint">or press <kbd>space</kbd></p>
  </div>`;
}
```
The `#again` button: I'll delegate clicks on the overlay — `overlay.addEventListener('click', e => { if (e.target.closest('#again')) { resetAndStart(); } })`. And since clicking the canvas also restarts, I'll keep the overlay pointer-events auto and add a global click handler. I'll add a single pointerdown handler on the whole `.screen` wrapper (canvas + overlay), so clicks on the overlay card also restart — with a small 500ms guard to prevent accidental instant restarts.

Ok — writing the final answer now.

For the medal SVG:
```js
function medalSVG(m){
  return `<svg viewBox="0 0 64 64" width="64" height="64">
    <defs><radialGradient id="mg"><stop offset="0" stop-color="${m.light}"/><stop offset="1" stop-color="${m.dark}"/></radialGradient></defs>
    <circle cx="32" cy="32" r="26" fill="url(#mg)" stroke="#2b2140" stroke-width="3"/>
    <path d="..." /> <!-- star -->
  </svg>`;
}
```
The star path — I'll use a polygon with computed points. I'll build the points string in JS.

OK — for real now. Let me write the final file. I'll keep the code well-organized and comment it lightly.

Additional check — the `ready` state and `resetAndStart()`:

```js
function resetGame(){
  GAME.state = 'ready';
  ...
  hideOverlay(); showReady();
}
function startGame(){
  GAME.state = 'playing';
  GAME.startedAt = performance.now();
  hideOverlay();
  audioInit();
}
function toReady(){ resetGame(); showReady(); }
```
And `initGame()`:
```js
resetGame();
showReady();
requestAnimationFrame(loop);
```

For the "dying" state, I need to handle: if the bird hits a pipe while at the pipe's position → dying, then it keeps falling; pipes stop.

Careful: in 'dying' the bird might be inside a pipe — it should fall through, which it does since we don't check collisions anymore.

For the sidebar stats — I'll update: score, best, pipes, flaps, perfects, altitude, and "speed". Let me define the DOM once and update in the loop (throttled to every ~6 frames to avoid layout thrash).

Final consideration on the layout — I'll use `scroll-behavior:smooth`.

One more consideration for correctness in the render: `ctx.save(); ctx.translate(shakeX, shakeY);` at the start of the frame, then everything drawn, then `ctx.restore()`. And clear: I'll fill the whole canvas with the sky gradient — but with a shake offset the edges could show gaps — I'll fill the sky before the shake translate, or use a bigger rect.

I'll structure render():
```js
function render(){
  ctx.save();
  ctx.translate(sx, sy);
  drawSky(); // fills -20..VW+20 to cover
  ...
  ctx.restore();
  drawFlash(); // after restore, full screen
  drawHUD(); // score etc — should this shake? I'll keep it inside the shake so it feels cohesive.
}
```
Simplest: keep everything inside the shake, and draw the sky with a padded rect.

I'll write `ctx.fillStyle = grad; ctx.fillRect(-30, -30, VW+60, VH+60);`.

Good. Let me finalize.

For the HUD, I'll write the score at the top center — but the pipes may overlap. I'll draw the score first, then pipes, then the bird — pipes can overlap the score, but I'll draw the score with a strong outline so it stays readable. That's fine.

Now let me write it all out. I'll keep the code tight and correct.

One more: `medalFor`:
```js
const MEDALS = [
 {min:0, name:'Fledgling', light:'#e8e2d4', dark:'#b8b0a0'},
 {min:10, name:'Wool', light:'#ffd9a0', dark:'#c98a3a'},
 {min:20, name:'Sea Glass', light:'#c9f0e0', dark:'#5bb98f'},
 {min:30, name:'Sunbeam', light:'#fff0a0', dark:'#e8a92a'},
 {min:45, name:'Aurora', light:'#e0f7ff', dark:'#7ec8e3'},
 {min:70, name:'Mythic', light:'#ffd6ef', dark:'#a06ae0'}
];
```

And the "mood" of the sky — the era name — I'll show it in the header as a "sky conditions" readout: "Sunrise · fireflies · etc."

Let me write it all out now — final answer.

I'll now write the HTML structure with real content.

Header:
```html
<header class="topbar">
  <div class="brand">
    <span class="chip">est. right now</span>
    <h1>Pip</h1>
    <p>a tiny bird · an enormous sky · one very determined flap</p>
  </div>
  <div class="topbar-right">
    <div class="live"><span class="dot"></span> <b id="state">ready</b></div>
    <div class="best"><span>best</span><b id="best">0</b></div>
    <button id="sound" class="btn ghost">♪ sound on</button>
  </div>
</header>
```

Main:
```html
<main class="stage">
  <section class="cabinet">
    <div class="marquee">…</div>
    <div class="screen">
      <canvas id="game"></canvas>
      <div class="overlay" id="overlay"></div>
      <div class="scan"></div> <!-- subtle scanlines -->
    </div>
    <div class="deck">
      <button id="flapBtn" class="btn big">flap</button>
      <div class="keys">…</div>
      <div class="leds">…</div>
    </div>
  </section>
  <aside class="guide">…cards…</aside>
</main>
```

The `#flapBtn` needs to not double-trigger with the canvas pointerdown — I'll add a separate pointerdown handler on the button that calls `flap()`.

Also need `touch-action: manipulation` on buttons/canvas.

The `#flapBtn` should work while playing — yes, tapping it flaps.

The `screen` needs `position:relative` and the canvas fills it; overlay is absolute centered.

The scanline overlay: `background: repeating-linear-gradient(0deg, rgba(0,0,0,.05) 0 1px, transparent 1px 3px)` — a subtle CRT feel; pointer-events none. I'll keep it very subtle so it doesn't muddy the bright art.

Time to write it out. Let me write the complete file now, and double-check each function as I go.

Let me write out the final code, then mentally test the critical paths:

1. Page loads → init → resize → resetGame → rAF loop → ready state → bird bobs, clouds drift, overlay shows "tap to start".
2. Click on canvas → action() → if ready: startGame() + flap() → playing.
3. Pipes scroll, collision, score, etc.
4. Hit → dying → land → over → overlay → restart.
5. Pause works.

The loop:
```js
function loop(now){
  requestAnimationFrame(loop);
  if (paused){ last = now; return; }
  const dt = Math.min(250, now - last); last = now;
  acc += dt;
  while (acc >= STEP){ update(); acc -= STEP; }
  render();
}
```
When paused, skip updating but continue rendering? I'll render the paused frame and the overlay — if paused, skip update and render, then continue — the last frame stays, and the overlay shows "paused." That's fine and cheap. But then `last` needs to be updated to avoid a huge catch-up — I do that.

Careful: if the game is paused, `update()` doesn't run, and the overlay shows "paused." Good.

Now, writing everything.

Let me write out the final code — being careful with correctness.

For the "ready" state, I'll spawn a "z" particle every ~90 steps, and make the bird bob.

Let me write it out.

Also for the guide's "Flight path" — I'll draw the trail in the `#spark` canvas only during play and when the game is on, drawing from the history array.

Time to write it out. I'll write it in the response directly, complete and careful. Let me write the answer.


## Content

# 🐣 Pip — a tiny flappy tale

A complete, single-file, zero-asset Flappy Bird — every sprite, particle, sound, and cloud is generated in code.

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>Pip · a tiny flappy tale</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Baloo+2:wght@500;600;700;800&family=Nunito:ital,wght@0,400;0,600;0,700;1,400&display=swap" rel="stylesheet">
<style>
/* ═══════════════════════════════ tokens ═══════════════════════════════ */
:root{
  --ink:#2b2140; --ink-2:#5d5375; --paper:#fffdf6; --paper-2:#fff3e0;
  --pink:#ff7fa8; --pink-d:#e0577f; --butter:#ffc93c; --butter-d:#e39a12;
  --mint:#4ecfa0; --mint-d:#219c74; --sky:#63b8e8; --sky-d:#2f83b8;
  --coral:#ff6f4d; --grape:#8a6ae0;
  --pop: 0 5px 0 rgba(43,33,64,.16);
  --deep: 0 6px 0 rgba(43,33,64,.14), 0 26px 42px rgba(43,33,64,.16);
}
*{box-sizing:border-box}
html{scroll-behavior:smooth}
body{
  margin:0; color:var(--ink); overflow-x:hidden;
  font-family:'Nunito',system-ui,sans-serif;
  background:
    radial-gradient(1100px 560px at 8% -12%, #fff2c6 0%, rgba(255,242,198,0) 62%),
    radial-gradient(900px 520px at 96% 4%, #bfe6ff 0%, rgba(191,230,255,0) 58%),
    linear-gradient(177deg,#ffe3ee 0%,#e4f3ff 46%,#d7f0e2 100%);
  background-attachment:fixed;
}
::selection{background:var(--butter);color:var(--ink)}

/* ═══════════════════════════ ambient background ═══════════════════════ */
.ambient{position:fixed;inset:0;pointer-events:none;z-index:0;overflow:hidden}
.ambient::after{content:"";position:absolute;inset:0;opacity:.5;
  background-image:radial-gradient(rgba(43,33,64,.10) 1px, transparent 1.4px);
  background-size:22px 22px}
.horizon{position:absolute;left:-10%;right:-10%;bottom:-40px;height:260px}
.horizon i{position:absolute;left:0;right:0;bottom:0;height:100%;border-radius:50% 50% 0 0/100% 100% 0 0}
.horizon i:nth-child(1){background:#bfe3d0;transform:translateY(30px) scaleY(.7)}
.horizon i:nth-child(2){background:#a5d5be;transform:translateY(70px) scaleY(.55)}
.petal{position:absolute;width:14px;height:11px;border-radius:60% 20% 60% 20%;
  opacity:.75;filter:saturate(1.2);animation:drift linear infinite}
@keyframes drift{
  0%{transform:translate3d(0,-12vh,0) rotate(0deg);opacity:0}
  10%{opacity:.85}
  100%{transform:translate3d(38vw,112vh,0) rotate(420deg);opacity:.15}
}
.cloud{position:absolute;background:rgba(255,255,255,.85);filter:blur(1px);
  animation:cross linear infinite}
.cloud::before,.cloud::after{content:"";position:absolute;background:inherit;border-radius:50%}
@keyframes cross{from{transform:translateX(-24vw)}to{transform:translateX(122vw)}}

/* ═══════════════════════════════ layout ══════════════════════════════ */
.wrap{position:relative;z-index:1;max-width:1280px;margin:0 auto;padding:22px 20px 60px}
.topbar{display:flex;flex-wrap:wrap;gap:18px;align-items:flex-end;justify-content:space-between;
  padding:6px 0 20px}
.brand{position:relative}
.chip{display:inline-block;font-size:11px;letter-spacing:.22em;text-transform:uppercase;
  font-weight:800;background:var(--ink);color:var(--paper);padding:4px 11px;border-radius:99px}
.wordmark{font-family:'Baloo 2',cursive;font-weight:800;line-height:.86;margin:10px 0 0;
  font-size:clamp(52px,9vw,96px);letter-spacing:-.04em;color:var(--butter);
  -webkit-text-stroke:3px var(--ink);paint-order:stroke fill;
  text-shadow:0 6px 0 rgba(43,33,64,.14), 0 20px 30px rgba(43,33,64,.22)}
.tagline{margin:6px 0 0;font-size:15px;font-weight:600;color:var(--ink-2);max-width:38ch}
.topbar-right{display:flex;gap:10px;align-items:center;flex-wrap:wrap}
.live{display:flex;align-items:center;gap:8px;background:var(--paper);border:2px solid var(--ink);
  border-radius:99px;padding:7px 14px;font-weight:800;font-size:13px;text-transform:uppercase;
  letter-spacing:.06em;box-shadow:var(--pop)}
.live .dot{width:9px;height:9px;border-radius:50%;background:var(--mint);
  box-shadow:0 0 0 0 rgba(78,207,160,.7);animation:ping 1.6s infinite}
@keyframes ping{0%{box-shadow:0 0 0 0 rgba(78,207,160,.6)}70%{box-shadow:0 0 0 10px rgba(78,207,160,0)}100%{box-shadow:0 0 0 0 rgba(78,207,160,0)}}

.stage{display:grid;gap:22px;grid-template-columns:minmax(0,1fr)}
@media(min-width:1000px){.stage{grid-template-columns:minmax(380px,440px) minmax(0,1fr);align-items:start}}
.guide{display:grid;gap:20px;grid-template-columns:repeat(auto-fit,minmax(230px,1fr))}

/* ═══════════════════════════════ cards ══════════════════════════════ */
.card{background:var(--paper);border:2px solid var(--ink);border-radius:20px;padding:18px;
  box-shadow:var(--deep);transition:transform .3s cubic-bezier(.34,1.5,.64,1),box-shadow .3s}
.card:hover{transform:translateY(-5px) rotate(-.35deg)}
.card h3{font-family:'Baloo 2',cursive;font-size:20px;margin:0 0 3px;letter-spacing:-.01em}
.card .sub{margin:0 0 14px;font-size:13px;color:var(--ink-2);font-weight:600}
.card.rose{background:#ffeef3}.card.mint{background:#eafaf1}.card.gold{background:#fff6dc}
.card.sky{background:#e9f4ff}.card.grape{background:#f1ecff}
.card.tilt-l{transform:rotate(-.7deg)}.card.tilt-r{transform:rotate(.8deg)}
.card.tilt-l:hover{transform:rotate(-.2deg) translateY(-5px)}
.card.tilt-r:hover{transform:rotate(.3deg) translateY(-5px)}

/* ═══════════════════════════════ cabinet ════════════════════════════ */
.cabinet{position:relative;background:linear-gradient(170deg,#fffdf6,#ffe9d3);
  border:3px solid var(--ink);border-radius:26px;padding:16px;box-shadow:var(--deep);
  transition:transform .3s cubic-bezier(.34,1.5,.64,1)}
.cabinet:hover{transform:translateY(-4px)}
.marquee{position:relative;display:flex;align-items:center;gap:12px;justify-content:space-between;
  background:var(--ink);color:#fff;border-radius:16px;padding:10px 14px;margin-bottom:12px;
  background-image:repeating-linear-gradient(115deg,rgba(255,255,255,.07) 0 6px,transparent 6px 14px)}
.marquee .title{font-family:'Baloo 2',cursive;font-size:22px;font-weight:800;letter-spacing:-.01em;
  color:var(--butter);text-shadow:0 2px 0 rgba(0,0,0,.35)}
.marquee .sky-note{font-size:12px;font-weight:700;letter-spacing:.14em;text-transform:uppercase;
  color:#cfe6ff;opacity:.9}
.sticker{position:absolute;top:-14px;right:22px;transform:rotate(7deg);background:var(--pink);
  color:#fff;border:2px solid var(--ink);border-radius:12px;padding:5px 12px;font-weight:800;
  font-size:12px;letter-spacing:.04em;box-shadow:0 4px 0 rgba(43,33,64,.3);z-index:3}
.screen{position:relative;border-radius:18px;overflow:hidden;border:3px solid var(--ink);
  background:#cfe8f7;box-shadow:inset 0 0 0 4px rgba(43,33,64,.15),0 10px 24px rgba(43,33,64,.25)}
.screen canvas{display:block;width:100%;height:auto;touch-action:manipulation;cursor:pointer}
.scan{position:absolute;inset:0;pointer-events:none;opacity:.16;
  background:repeating-linear-gradient(0deg,rgba(20,10,40,.5) 0 1px,transparent 1px 3px)}
.vig{position:absolute;inset:0;pointer-events:none;
  box-shadow:inset 0 0 60px rgba(43,33,64,.28),inset 0 0 12px rgba(43,33,64,.2)}

/* ═══════════════════════════════ overlay ════════════════════════════ */
.overlay{position:absolute;inset:0;display:none;place-items:center;padding:18px;
  background:rgba(24,14,38,.5);backdrop-filter:blur(3px);z-index:5}
.overlay.show{display:grid;animation:fadeIn .3s ease}
@keyframes fadeIn{from{opacity:0}to{opacity:1}}
.ov-card{background:var(--paper);border:3px solid var(--ink);border-radius:22px;padding:22px 22px 20px;
  text-align:center;max-width:320px;box-shadow:0 10px 0 rgba(43,33,64,.22);
  animation:pop .42s cubic-bezier(.34,1.56,.64,1) both}
@keyframes pop{from{transform:scale(.7) rotate(-4deg) translateY(24px);opacity:0}
  to{transform:none;opacity:1}}
.ov-card h2{font-family:'Baloo 2',cursive;font-size:30px;margin:12px 0 2px;letter-spacing:-.02em}
.ov-ribbon{display:inline-block;background:var(--coral);color:#fff;border:2px solid var(--ink);
  border-radius:99px;padding:4px 14px;font-weight:800;font-size:12px;letter-spacing:.14em;
  text-transform:uppercase;transform:rotate(-2deg)}
.ov-ribbon.newbest{background:var(--mint);animation:wiggle .6s ease infinite alternate}
@keyframes wiggle{from{transform:rotate(-3deg) scale(1)}to{transform:rotate(3deg) scale(1.04)}}
.ov-quip{font-style:italic;font-size:14px;color:var(--ink-2);margin:8px 0 14px;line-height:1.45}
.ov-scores{display:flex;gap:10px;justify-content:center;margin-bottom:16px}
.ov-scores>div{flex:1;background:var(--paper-2);border:2px solid var(--ink);border-radius:14px;padding:8px}
.ov-scores span{display:block;font-size:10px;letter-spacing:.18em;text-transform:uppercase;
  font-weight:800;color:var(--ink-2)}
.ov-scores b{font-family:'Baloo 2',cursive;font-size:30px;line-height:1.1}
.medal{filter:drop-shadow(0 4px 0 rgba(43,33,64,.2))}

/* ═══════════════════════════════ buttons ════════════════════════════ */
.btn{font-family:'Baloo 2',cursive;font-weight:800;font-size:16px;letter-spacing:.01em;
  border:2px solid var(--ink);border-radius:14px;padding:10px 18px;cursor:pointer;
  background:var(--butter);color:var(--ink);box-shadow:0 4px 0 var(--ink);
  transition:transform .12s,box-shadow .12s,background .2s;touch-action:manovrer;touch-action:manipulation}
.btn:hover{background:#ffd75e}
.btn:active,.btn.press{transform:translateY(4px);box-shadow:0 0 0 var(--ink)}
.btn.ghost{background:var(--paper)}
.btn.ghost:hover{background:#fff}
.btn.mint{background:var(--mint);color:#fff}
.btn.mint:hover{background:#6ee0b6}
.btn.big{font-size:20px;padding:12px 26px;border-radius:16px}
.btn.wide{width:100%}

.deck{display:flex;align-items:center;gap:14px;margin-top:12px;flex-wrap:wrap;
  background:var(--paper-2);border:2px solid var(--ink);border-radius:18px;padding:12px}
.keys{display:flex;gap:6px;flex-wrap:wrap;font-size:11px;font-weight:700;color:var(--ink-2)}
kbd{font-family:'Baloo 2',cursive;font-size:12px;background:#fff;border:2px solid var(--ink);
  border-bottom-width:3px;border-radius:7px;padding:2px 7px;color:var(--ink);font-weight:800}
.leds{margin-left:auto;display:flex;gap:6px}
.leds i{width:10px;height:10px;border-radius:50%;background:var(--ink);opacity:.25;
  animation:blip 1.4s infinite}
.leds i:nth-child(2){animation-delay:.2s}.leds i:nth-child(3){animation-delay:.4s}
.leds i:nth-child(4){animation-delay:.6s}
@keyframes blip{0%,100%{opacity:.2}40%{opacity:1;background:var(--coral)}}

/* ═══════════════════════════════ guide bits ═════════════════════════ */
.stat-row{display:flex;justify-content:space-between;align-items:baseline;gap:8px;
  padding:7px 0;border-bottom:1px dashed rgba(43,33,64,.25)}
.stat-row:last-child{border:0}
.stat-row span{font-size:12px;font-weight:800;letter-spacing:.1em;text-transform:uppercase;color:var(--ink-2)}
.stat-row b{font-family:'Baloo 2',cursive;font-size:22px}
.bar{height:9px;background:#e6ddcc;border:2px solid var(--ink);border-radius:99px;overflow:hidden;margin-top:5px}
.bar i{display:block;height:100%;width:20%;border-radius:99px;transition:width .4s ease,background .4s}
.pick{display:flex;gap:11px;align-items:center;padding:9px 0;border-bottom:1px dashed rgba(43,33,64,.25)}
.pick:last-child{border:0}
.pick svg{flex:none;filter:drop-shadow(0 2px 0 rgba(43,33,64,.25))}
.pick p{margin:0;font-size:13px;line-height:1.35}
.pick b{display:block;font-family:'Baloo 2',cursive;font-size:15px}
.medal-row{display:flex;align-items:center;gap:9px;padding:6px 0;font-size:13px;font-weight:700;
  color:var(--ink-2);opacity:.55;transition:.3s}
.medal-row.won{opacity:1;color:var(--ink);transform:translateX(3px)}
.medal-row i{width:16px;height:16px;border-radius:50%;border:2px solid var(--ink);flex:none}
.quote{font-family:'Baloo 2',cursive;font-size:17px;line-height:1.35;margin:0}
.quote + .by{font-size:12px;color:var(--ink-2);font-weight:700;margin:8px 0 0}
.spark{width:100%;height:86px;display:block;background:#eef4fb;border:2px solid var(--ink);
  border-radius:14px}
.foot{text-align:center;font-size:12px;color:var(--ink-2);margin-top:44px;font-weight:700}
.reveal{opacity:0;transform:translateY(22px)}
.reveal.in{opacity:1;transform:none;transition:opacity .6s ease,transform .6s cubic-bezier(.34,1.5,.64,1)}
@media(prefers-reduced-motion:reduce){*{animation-duration:.01ms!important;transition-duration:.01ms!important}}
</style>
</head>
<body>

<div class="ambient" aria-hidden="true">
  <div class="horizon"><i></i><i></i></div>
</div>

<div class="wrap">

  <header class="topbar">
    <div class="brand">
      <span class="chip">no assets · all love</span>
      <h1 class="wordmark">Pip</h1>
      <p class="tagline">A tiny bird, an enormous sky, and one very determined flap.</p>
    </div>
    <div class="topbar-right">
      <div class="live"><span class="dot"></span><b id="stateLabel">ready</b></div>
      <div class="live"><span>best</span><b id="bestLabel">0</b></div>
      <button class="btn ghost" id="soundBtn">♪ sound on</button>
    </div>
  </header>

  <main class="stage">

    <!-- ══════════ the cabinet ══════════ -->
    <section class="cabinet">
      <div class="sticker">100% hand-drawn</div>
      <div class="marquee">
        <span class="title">FLIGHT SIMULATOR (tiny)</span>
        <span class="sky-note" id="skyNote">sunrise · calm</span>
      </div>

      <div class="screen">
        <canvas id="game" width="400" height="640"></canvas>
        <div class="scan"></div>
        <div class="vig"></div>
        <div class="overlay" id="overlay"></div>
      </div>

      <div class="deck">
        <button class="btn big" id="flapBtn">flap!</button>
        <div class="keys">
          <span><kbd>space</kbd> flap</span>
          <span><kbd>P</kbd> pause</span>
          <span><kbd>R</kbd> restart</span>
          <span>tap / click = flap</span>
        </div>
        <div class="leds"><i></i><i></i><i></i><i></i></div>
      </div>
    </section>

    <!-- ══════════ field guide ══════════ -->
    <aside class="guide">

      <article class="card gold reveal" style="grid-column:span 2">
        <h3>Flight log</h3>
        <p class="sub">Live telemetry from Pip's little airframe.</p>
        <div style="display:grid;gap:10px;grid-template-columns:repeat(auto-fit,minmax(120px,1fr))">
          <div class="stat-row" style="flex-direction:column;align-items:stretch">
            <span>score</span><b id="sScore">0</b>
            <div class="bar"><i id="bScore" style="background:var(--butter)"></i></div>
          </div>
          <div class="stat-row" style="flex-direction:column;align-items:stretch">
            <span>pipes</span><b id="sPipes">0</b>
            <div class="bar"><i id="bPipes" style="background:var(--mint)"></i></div>
          </div>
          <div class="stat-row" style="flex-direction:column;align-items:stretch">
            <span>flaps</span><b id="sFlaps">0</b>
            <div class="bar"><i id="bFlaps" style="background:var(--pink)"></i></div>
          </div>
          <div class="stat-row" style="flex-direction:column;align-items:stretch">
            <span>perfects</span><b id="sPerf">0</b>
            <div class="bar"><i id="bPerf" style="background:var(--sky)"></i></div>
          </div>
        </div>
      </article>

      <article class="card sky reveal">
        <h3>Flight path</h3>
        <p class="sub">Altitude, last 200 samples.</p>
        <canvas class="spark" id="spark" width="300" height="86"></canvas>
        <div class="stat-row" style="margin-top:10px"><span>altitude</span><b id="sAlt">0m</b></div>
      </article>

      <article class="card mint reveal">
        <h3>Difficulty</h3>
        <p class="sub">The sky gets unkind.</p>
        <div class="stat-row" style="flex-direction:column;align-items:stretch">
          <span>scroll speed</span><b id="sSpeed">2.4</b>
          <div class="bar"><i id="bSpeed" style="background:var(--coral)"></i></div>
        </div>
        <div class="stat-row" style="flex-direction:column;align-items:stretch;margin-top:12px">
          <span>gap size</span><b id="sGap">168</b>
          <div class="bar"><i id="bGap" style="background:var(--grape)"></i></div>
        </div>
      </article>

      <article class="card rose reveal">
        <h3>Pickups</h3>
        <p class="sub">Shiny things in the gap.</p>
        <div class="pick">
          <svg width="34" height="34" viewBox="0 0 34 34"><polygon points="17,3 21,12 31,13 23,20 25,30 17,25 9,30 11,20 3,13 13,12" fill="#ffc93c" stroke="#2b2140" stroke-width="2" stroke-linejoin="round"/></svg>
          <p><b>Star</b>+2 points, a little burst of joy.</p>
        </div>
        <div class="pick">
          <svg width="34" height="34" viewBox="0 0 34 34"><path d="M6 27c1-11 8-19 21-21-1 12-7 19-17 20-2 2-3 2-4 1z" fill="#bfe8ff" stroke="#2b2140" stroke-width="2" stroke-linejoin="round"/><path d="M8 25c6-4 11-9 14-15" stroke="#2b2140" stroke-width="2"/></svg>
          <p><b>Feather</b>three seconds of floaty glide.</p>
        </div>
      </article>

      <article class="card grape reveal">
        <h3>Medals</h3>
        <p class="sub">Earned, not bought.</p>
        <div id="medalList"></div>
      </article>

      <article class="card tilt-l reveal">
        <p class="quote">"If at first you don't succeed, flap harder and blame the wind."</p>
        <p class="by">— Pip, probably</p>
      </article>

      <article class="card reveal" style="grid-column:span 2">
        <h3>Controls</h3>
        <p class="sub">Everything is one button, really.</p>
        <div style="display:flex;gap:14px;flex-wrap:wrap;align-items:center">
          <div><kbd>space</kbd> <kbd>↑</kbd> <kbd>W</kbd> <span style="font-size:13px;font-weight:700;color:var(--ink-2)">flap</span></div>
          <div><kbd>P</kbd> <span style="font-size:13px;font-weight:700;color:var(--ink-2)">pause</span></div>
          <div><kbd>R</kbd> <span style="font-size:13px;font-weight:700;color:var(--ink-2)">restart</span></div>
          <div>tap the screen · or the big <span style="font-family:'Baloo 2';font-weight:800">flap!</span> button</div>
        </div>
      </article>

    </aside>
  </main>

  <footer class="foot">Everything you see — bird, pipes, clouds, chords — is drawn and synthesised at runtime. No PNGs were harmed.</footer>
</div>

<script>
/* ══════════════════════════════════════════════════════════════════════
   0 · tiny helpers
   ══════════════════════════════════════════════════════════════════════ */
const $ = s => document.querySelector(s);
const rnd = (a,b) => a + Math.random()*(b-a);
const rint = (a,b) => Math.floor(rnd(a,b+1));
const clamp = (v,a,b) => v<a?a:v>b?b:v;
const lerp = (a,b,t) => a + (b-a)*t;
const pick = a => a[Math.floor(Math.random()*a.length)];
function hex(h){h=h.replace('#','');if(h.length===3)h=h.split('').map(c=>c+c).join('');
  const n = parseInt(h,16); return [(n>>16)&255,(n>>8)&255,n&255];}
function mix(a,b,t){return [lerp(a[0],b[0],t),lerp(a[1],b[1],t),lerp(a[2],b[2],t)];}
function rgba(c,a){return `rgba(${c[0]|0},${c[1]|0},${c[2]|0},${a})`;}
function rr(c,x,y,w,h,r){
  r = Math.min(r, Math.abs(w)/2, Math.abs(h)/2);
  c.beginPath();
  c.moveTo(x+r,y); c.lineTo(x+w-r,y); c.quadraticCurveTo(x+w,y,x+w,y+r);
  c.lineTo(x+w,y+h-r); c.quadraticCurveTo(x+w,y+h,x+w-r,y+h);
  c.lineTo(x+r,y+h); c.quadraticCurveTo(x,y+h,x,y+h-r);
  c.lineTo(x,y+r); c.quadraticCurveTo(x,y,x+r,y); c.closePath();
}

/* ══════════════════════════════════════════════════════════════════════
   1 · audio — everything synthesised, no files
   ══════════════════════════════════════════════════════════════════════ */
let AC = null, master = null, musicBus = null, noiseBuf = null, muted = false;
function audio(){
  if (AC) return AC;
  try { AC = new (window.AudioContext || window.webkitAudioContext)(); }
  catch(e){ return null; }
  master = AC.createGain(); master.gain.value = 0.55; master.connect(AC.destination);
  musicBus = AC.createGain(); musicBus.gain.value = 0.30; musicBus.connect(master);
  noiseBuf = AC.createBuffer(1, AC.sampleRate, AC.sampleRate);
  const d = noiseBuf.getChannelData(0);
  for (let i=0;i<d.length;i++) d[i] = Math.random()*2-1;
  musicNext = AC.currentTime + 0.1;
  return AC;
}
function tone(o){
  const ac = audio(); if (!ac || muted) return;
  const t0 = ac.currentTime + (o.when||0), dur = o.dur || 0.15;
  const osc = ac.createOscillator(), g = ac.createGain();
  osc.type = o.type || 'sine';
  osc.frequency.setValueAtTime(o.f, t0);
  if (o.f2) osc.frequency.exponentialRampToValueAtTime(Math.max(30,o.f2), t0+dur);
  g.gain.setValueAtTime(0.0001, t0);
  g.gain.exponentialRampToValueAtTime(Math.max(0.001,o.vol||0.25), t0+0.012);
  g.gain.exponentialRampToValueAtTime(0.0001, t0+dur);
  osc.connect(g); g.connect(o.dest || master);
  osc.start(t0); osc.stop(t0+dur+0.06);
}
function noiseHit(dur, vol, freq, when){
  const ac = audio(); if (!ac || muted) return;
  const src = ac.createBufferSource(); src.buffer = noiseBuf; src.loop = true;
  const f = ac.createBiquadFilter(); f.type = 'lowpass'; f.frequency.value = freq;
  const g = ac.createGain(); const t0 = ac.currentTime + (when||0);
  g.gain.setValueAtTime(vol, t0);
  g.gain.exponentialRampToValueAtTime(0.001, t0+dur);
  src.connect(f); f.connect(g); g.connect(master);
  src.start(t0); src.stop(t0+dur+0.05);
}
const sfx = {
  flap(){ tone({f:340,f2:190,type:'triangle',dur:0.09,vol:0.14}); noiseHit(0.07,0.10,2600); },
  score(){ tone({f:880,type:'square',dur:0.09,vol:0.13});
           tone({f:1318,type:'square',dur:0.13,vol:0.11,when:0.07}); },
  perfect(){ [1046,1318,1568,2093].forEach((f,i)=>
      tone({f,type:'triangle',dur:0.16,vol:0.13,when:i*0.055})); },
  pickup(){ tone({f:660,f2:1600,type:'sine',dur:0.22,vol:0.16});
            tone({f:1320,f2:2600,type:'sine',dur:0.2,vol:0.07,when:0.03}); },
  bonk(){ tone({f:2000,type:'sine',dur:0.12,vol:0.13}); },
  hit(){ noiseHit(0.3,0.5,900); tone({f:190,f2:60,type:'sawtooth',dur:0.34,vol:0.22}); },
  land(){ noiseHit(0.2,0.32,500); tone({f:150,f2:80,type:'square',dur:0.2,vol:0.14}); },
  over(){ [523,440,392,349].forEach((f,i)=>
      tone({f,type:'triangle',dur:0.3,vol:0.13,when:i*0.16})); },
  ui(){ tone({f:700,type:'sine',dur:0.06,vol:0.09}); }
};
/* gentle pentatonic wander */
const MELDY = [0,4,7,4, 9,7,4,0, 2,5,9,5, 7,2,0,-1,
               5,9,12,9, 7,4,2,0, 4,7,11,7, 5,4,0,-1];
const BASS  = [0,0,-1,0, 0,0,-1,0, 5,5,-1,5, 5,5,-1,5,
               7,7,-1,7, 7,7,-1,7, 3,3,-1,3, 3,3,-1,3];
let musicStep = 0, musicNext = 0;
function scheduleMusic(){
  const ac = audio(); if (!ac || muted) return;
  if (G.state === 'over' || G.paused) return;
  while (musicNext < ac.currentTime + 0.25){
    const i = musicStep % MELDY.length;
    const n = MELDY[i];
    if (n >= 0){
      const f = 261.63 * Math.pow(2, n/12);
      tone({f, type:'triangle', dur:0.2, vol:0.085, dest:musicBus,
            when: Math.max(0, musicNext - ac.currentTime)});
    }
    const b = BASS[i];
    if (b >= 0){
      const f = 130.81 * Math.pow(2, b/12);
      tone({f, type:'sine', dur:0.3, vol:0.14, dest:musicBus,
            when: Math.max(0, musicNext - ac.currentTime)});
    }
    musicStep++; musicNext += 0.2;
  }
}

/* ══════════════════════════════════════════════════════════════════════
   2 · game state
   ══════════════════════════════════════════════════════════════════════ */
const VW = 400, VH = 640, GROUND_H = 96, PLAY_H = VH - GROUND_H;
const cvs = $('#game'), ctx = cvs.getContext('2d', {alpha:false});

const G = {
  state:'ready', paused:false, score:0, best:0,
  pipes:[], parts:[], floats:[],
  bird:{x:104,y:280,vy:0,rot:0,wing:0,blink:0,glide:0},
  shake:0, flash:0, t:0,
  stats:{flaps:0, passed:0, perfects:0, pickups:0},
  history:[], dieT:0, gapUsed:168
};

const PIPE_COLS = [
  {d:'#2f9e6e', b:'#66d3a3', l:'#c9f5df'},   // mint
  {d:'#d8557a', b:'#ff9db0', l:'#ffd9e2'},   // strawberry
  {d:'#c98a2a', b:'#f4c45c', l:'#ffeec2'},   // butter
  {d:'#3f7fb5', b:'#7fc3ea', l:'#d3ecff'}     // sky
];
const MEDALS = [
  {min:0,  name:'Fledgling', dark:'#b9b1a1', light:'#ece6d8'},
  {min:10, name:'Tin Beak',  dark:'#a89a86', light:'#e2d8c8'},
  {min:20, name:'Wool Wrap', dark:'#c98a3a', light:'#ffd9a0'},
  {min:35, name:'Sea Glass', dark:'#219c74', light:'#c9f0e0'},
  {min:50, name:'Sunbeam',   dark:'#e39a12', light:'#fff0a0'},
  {min:70, name:'Aurora',    dark:'#6a4fd0', light:'#e0d8ff'}
];
const QUIPS = [
  "A valiant effort. A dignified bonk.",
  "Physics 1 — Bird 0.",
  "That pipe came out of nowhere. (It did not.)",
  "You peaked. Then you, um, didn't.",
  "Gravity remains undefeated.",
  "Pip is fine. Pip is grounded.",
  "Certified 90% cloud-adjacent.",
  "You flew like a confused brick.",
  "Somewhere a worm is laughing at you.",
  "The sky forgives. The pipe does not."
];

/* ── scenery: sky eras ── */
const SKY = [
  {name:'sunrise · calm',   skyTop:'#ffd9e8', skyMid:'#ffe8c4', skyLow:'#cfe9ff',
   cloud:'#ffffff', hillFar:'#f3c9e0', hillNear:'#e59cbf', night:0},
  {name:'midday · breezy',  skyTop:'#a8dcf5', skyMid:'#cdeefc', skyLow:'#e8f7ff',
   cloud:'#ffffff', hillFar:'#a8d8a0', hillNear:'#79bd84', night:0},
  {name:'dusk · golden',    skyTop:'#6a5b9e', skyMid:'#ff9e7a', skyLow:'#ffd0a0',
   cloud:'#ffd9e0', hillFar:'#7a6a9e', hillNear:'#4d4370', night:.35},
  {name:'night · fireflies',skyTop:'#141d43', skyMid:'#26305f', skyLow:'#4a5c93',
   cloud:'#8b96c8', hillFar:'#232a4d', hillNear:'#141a33', night:1}
];
function currentSky(){
  const t = clamp(G.score, 0, 60) / 60;
  const i = t * (SKY.length - 1);
  const i0 = Math.floor(i), i1 = Math.min(i0 + 1, SKY.length - 1), f = i - i0;
  const a = SKY[i0], b = SKY[i1];
  return {
    name: (f < .5 ? a : b).name,
    skyTop: mix(hex(a.skyTop), hex(b.skyTop), f),
    skyMid: mix(hex(a.skyMid), hex(b.skyMid), f),
    skyLow: mix(hex(a.skyLow), hex(b.skyLow), f),
    cloud:  mix(hex(a.cloud),  hex(b.cloud),  f),
    hillFar:mix(hex(a.hillFar),hex(b.hillFar),f),
    hillNear:mix(hex(a.hillNear),hex(b.hillNear),f),
    night:  lerp(a.night, b.night, f)
  };
}

/* ── decor pools ── */
const decor = {clouds:[], stars:[], grass:[], balloons:[], flock:[]};
function initDecor(){
  decor.clouds = [];
  for (let i=0;i<7;i++) decor.clouds.push({
    x:rnd(-40,VW+60), y:rnd(20,300), s:rnd(.6,1.5), v:rnd(.15,.45)});
  decor.stars = [];
  for (let i=0;i<70;i++) decor.stars.push({x:rnd(0,VW),y:rnd(0,380),r:rnd(.6,1.8),p:rnd(0,6.28)});
  decor.grass = [];
  for (let i=0;i<46;i++) decor.grass.push({x:rnd(0,VW+60),h:rnd(6,16),k:Math.random()});
  decor.balloons = [];
  for (let i=0;i<2;i++) decor.balloons.push({x:rnd(0,VW),y:rnd(40,150),v:rnd(.1,.22),
    hue:pick(['#ff8fb1','#ffd166','#7fd6b4','#8ec9ea','#c39bff']),bob:rnd(0,6)});
  decor.flock = [];
}

/* ── pipes ── */
function addPipe(){
  const last = G.pipes[G.pipes.length-1];
  const x = last ? last.x + last.w + rnd(148,206) : 470;
  const gapH = clamp(168 - G.score*0.7, 130, 168);
  const gapY = rnd(64, PLAY_H - gapH - 64);
  const col = pick(PIPE_COLS);
  const items = [];
  if (Math.random() < 0.28){
    const type = Math.random() < 0.62 ? 'star' : 'feather';
    items.push({type, y:gapY + gapH/2 + rnd(-gapH*0.22, gapH*0.22), taken:false, ph:rnd(0,6.28)});
  }
  G.pipes.push({x, w:62, gapY, gapH, passed:false, col:col, items, seed:rnd(0,100)});
}
function resetGame(){
  G.state = 'ready'; G.paused = false;
  G.score = 0; G.pipes = []; G.parts = []; G.floats = [];
  G.bird = {x:104, y:280, vy:0, rot:0, wing:0, blink:0, glide:0};
  G.stats = {flaps:0, passed:0, perfects:0, pickups:0};
  G.history = []; G.shake = 0; G.flash = 0; G.dieT = 0;
  addPipe();
  initDecor();
  hideOverlay();
  showReady();
}

/* ── particles ── */
function spawn(o){ if (G.parts.length < 260) G.parts.push(o); }
function burst(x,y,n,kind,col){
  for (let i=0;i<n;i++){
    const a = rnd(0,6.283), s = rnd(0.6,3.2);
    spawn({kind, x, y, vx:Math.cos(a)*s, vy:Math.sin(a)*s - 0.4,
           life:1, dl:rnd(0.012,0.026), size:rnd(3,7), rot:rnd(0,6.28),
           vr:rnd(-.2,.2), col});
  }
}
function floatText(x,y,txt,col){ G.floats.push({x,y,txt,col,life:1}); }

/* ══════════════════════════════════════════════════════════════════════
   3 · update
   ══════════════════════════════════════════════════════════════════════ */
function speedNow(){ return 2.35 + Math.min(1.35, G.score * 0.028); }

function flap(){
  if (G.state === 'ready'){ startGame(); }
  if (G.state !== 'playing') return;
  const b = G.bird;
  b.vy = -7.05; b.wing = -0.6; G.stats.flaps++;
  spawn({kind:'puff', x:b.x-12, y:b.y+6, vx:-1.2, vy:rnd(-.2,.5), life:1,
         dl:0.05, size:rnd(4,8), rot:0, vr:0, col:'#ffffff'});
  if (Math.random() < 0.5){
    spawn({kind:'heart', x:b.x+rnd(-8,8), y:b.y+rnd(-6,6), vx:rnd(-1.6,-.4),
           vy:rnd(-1.2,-.2), life:1, dl:0.02, size:rnd(4,7), rot:rnd(-.4,.4),
           vr:rnd(-.1,.1), col:'#ff8fb1'});
  }
  sfx.flap();
}
function startGame(){
  if (G.state === 'ready'){ audio(); G.state = 'playing'; }
}

function update(){
  G.t++;
  const b = G.bird;

  if (G.state === 'ready'){
    b.y = 280 + Math.sin(G.t*0.05)*9;
    b.wing -= 0.12; b.rot = Math.sin(G.t*0.05)*0.08;
    if (G.t % 70 === 0) spawn({kind:'text', txt:'z', x:b.x+10, y:b.y-14,
      vx:rnd(.2,.7), vy:rnd(-.55,-.25), life:1, dl:0.012, size:rnd(9,14),
      rot:0, vr:0, col:'#6a5b9e'});
    decor.clouds.forEach(c => { c.x -= c.v; if (c.x < -90) { c.x = VW+90; c.y = rnd(20,300); } });
    if (G.t % 240 === 0) decor.flock.push({x:VW+20, y:rnd(60,220), v:rnd(.5,.9), n:rint(2,5)});
    decor.flock.forEach(f => f.x -= f.v);
    decor.flock = decor.flock.filter(f => f.x > -60);
    return;
  }

  if (G.state === 'playing'){
    const sp = speedNow();

    /* pipes */
    while (G.pipes[G.pipes.length-1].x < VW + 120) addPipe();
    if (G.pipes[0].x + G.pipes[0].w < -80) G.pipes.shift();

    for (const p of G.pipes){
      p.x -= sp;
      if (!p.passed && b.x > p.x + p.w){
        p.passed = true;
        const center = p.gapY + p.gapH/2;
        const perfect = Math.abs(b.y - center) < 20;
        G.score++; G.stats.passed++;
        if (perfect){
          G.score++; G.stats.perfects++;
          floatText(b.x+24, b.y-14, 'PERFECT +2', '#e39a12');
          burst(b.x+10, b.y, 14, 'spark', '#ffe37a');
          sfx.perfect();
        } else {
          floatText(b.x+24, b.y-14, '+1', '#219c74');
          burst(b.x+8, b.y, 6, 'heart', '#ff8fb1');
          sfx.score();
        }
      }
      /* pickups */
      for (const it of p.items){
        if (it.taken) continue;
        const iy = it.y + Math.sin(G.t*0.06 + it.ph)*6;
        if (Math.hypot(b.x - (p.x + p.w/2), b.y - iy) < 19){
          it.taken = true; G.stats.pickups++;
          if (it.type === 'star'){
            G.score += 2; floatText(b.x, b.y-18, 'STAR +2', '#e39a12');
            burst(b.x, b.y, 16, 'star', '#ffd166');
          } else {
            b.glide = 170; floatText(b.x, b.y-18, 'GLIDE!', '#63b8e8');
            burst(b.x, b.y, 14, 'feather', '#bfe8ff');
          }
          sfx.pickup();
        }
      }
      /* collision */
      if (b.x + 12 > p.x && b.x - 12 < p.x + p.w){
        const top = circleRect(b.x, b.y, 13, p.x, -200, p.w, p.gapY + 200);
        const bot = circleRect(b.x, b.y, 13, p.x, p.gapY + p.gapH, p.w, PLAY_H - p.gapY - p.gapH + 10);
        if (top || bot) kill();
      }
    }

    /* bird physics */
    b.vy += 0.42;
    if (b.glide > 0){ b.glide--; b.vy = Math.min(b.vy, 1.7);
      if (G.t % 3 === 0) spawn({kind:'spark', x:b.x-10, y:b.y+rnd(-8,8),
        vx:rnd(-2,-1), vy:rnd(-.3,.3), life:1, dl:0.05, size:rnd(2,4),
        rot:0, vr:0, col:'#cdeeff'});
    }
    b.y += b.vy;
    b.rot = clamp(b.vy*0.072, -0.52, 1.25);
    b.wing -= 0.30;

    /* ceiling */
    if (b.y < 26){
      b.y = 26;
      if (b.vy < 0){ b.vy = 0.6; sfx.bonk();
        burst(b.x, 26, 5, 'puff', '#ffffff'); }
    }
    /* ground */
    if (b.y + 13 >= PLAY_H){
      b.y = PLAY_H - 13;
      if (G.state === 'playing') kill();
      if (G.state === 'dying'){ G.state = 'over'; sfx.land(); showOver(); }
    }
    /* scroll decor */
    decor.clouds.forEach(c => { c.x -= c.v + sp*0.28; if (c.x < -90){ c.x = VW+90; c.y = rnd(20,300); c.s = rnd(.6,1.5); }});
    decor.grass.forEach(g => { g.x -= sp; if (g.x < -20) g.x += VW + 40; });
    decor.balloons.forEach(bl => { bl.x -= bl.v; if (bl.x < -70){ bl.x = VW+70; bl.y = rnd(40,150); }});
    if (G.t % 260 === 0) decor.flock.push({x:VW+20, y:rnd(60,220), v:rnd(.6,1.0), n:rint(2,5)});
    decor.flock.forEach(f => f.x -= f.v);
    decor.flock = decor.flock.filter(f => f.x > -60);

    G.history.push(b.y);
    if (G.history.length > 200) G.history.shift();
  }

  if (G.state === 'dying'){
    G.dieT++;
    b.vy += 0.5; b.y += b.vy;
    b.rot = Math.min(b.rot + 0.05, 1.6);
    if (b.y + 13 >= PLAY_H){
      b.y = PLAY_H - 13; b.vy = 0;
      G.state = 'over'; sfx.land(); showOver();
    }
  }

  /* particles */
  for (const p of G.parts){
    p.x += p.vx; p.y += p.vy; p.rot += p.vr; p.life -= p.dl;
    if (p.kind === 'heart' || p.kind === 'feather') p.vy -= 0.012;
    else if (p.kind === 'puff') p.vy -= 0.005;
    else p.vy += 0.055;
  }
  G.parts = G.parts.filter(p => p.life > 0 && p.x > -30 && p.y < VH+30);
  for (const f of G.floats){ f.y -= 0.9; f.life -= 0.017; }
  G.floats = G.floats.filter(f => f.life > 0);
  if (G.shake > 0) G.shake *= 0.9;
  if (G.flash > 0) G.flash *= 0.86;

  if (G.best < G.score) { G.best = G.score; saveBest(); }
}
function circleRect(cx,cy,r,rx,ry,rw,rh){
  const nx = Math.max(rx, Math.min(cx, rx+rw));
  const ny = Math.max(ry, Math.min(cy, ry+rh));
  const dx = cx-nx, dy = cy-ny;
  return dx*dx + dy*dy < r*r;
}
function kill(){
  if (G.state !== 'playing') return;
  G.state = 'dying'; G.dieT = 0;
  G.shake = 15; G.flash = 0.75;
  burst(G.bird.x, G.bird.y, 22, 'feather', '#fff0a0');
  burst(G.bird.x, G.bird.y, 10, 'puff', '#ffffff');
  sfx.hit();
}

/* ══════════════════════════════════════════════════════════════════════
   4 · drawing
   ══════════════════════════════════════════════════════════════════════ */
function starPath(c,cx,cy,r1,r2,n,rot){
  c.beginPath();
  for (let i=0;i<n*2;i++){
    const r = i%2 ? r2 : r1, a = rot + i*Math.PI/n;
    const x = cx + Math.cos(a)*r, y = cy + Math.sin(a)*r;
    i ? c.lineTo(x,y) : c.moveTo(x,y);
  }
  c.closePath();
}
function heartPath(c,cx,cy,s){
  c.beginPath();
  c.moveTo(cx, cy + s*0.55);
  c.bezierCurveTo(cx - s, cy - s*0.1, cx - s*0.55, cy - s, cx, cy - s*0.35);
  c.bezierCurveTo(cx + s*0.55, cy - s, cx + s, cy - s*0.1, cx, cy + s*0.55);
  c.closePath();
}
function cloudPath(c,cx,cy,s){
  c.beginPath();
  c.arc(cx, cy, 16*s, 0, 6.283);
  c.arc(cx + 17*s, cy + 4*s, 12*s, 0, 6.283);
  c.arc(cx - 17*s, cy + 5*s, 11*s, 0, 6.283);
  c.arc(cx + 5*s, cy - 12*s, 12*s, 0, 6.283);
  c.arc(cx - 8*s, cy - 9*s, 10*s, 0, 6.283);
  c.closePath();
}
function hillPath(c, baseY, amp, freq, phase){
  c.beginPath(); c.moveTo(-30, VH);
  for (let x=-30; x<=VW+30; x+=10){
    const y = baseY + Math.sin((x+phase)*freq)*amp + Math.sin((x+phase)*freq*2.3)*amp*0.35;
    c.lineTo(x, y);
  }
  c.lineTo(VW+30, VH); c.closePath();
}

function drawSky(){
  const p = currentSky();
  const g = ctx.createLinearGradient(0, 0, 0, PLAY_H);
  g.addColorStop(0, rgba(p.skyTop,1));
  g.addColorStop(0.55, rgba(p.skyMid,1));
  g.addColorStop(1, rgba(p.skyLow,1));
  ctx.fillStyle = g; ctx.fillRect(-30, -30, VW+60, PLAY_H+40);

  /* stars */
  if (p.night > 0.04){
    for (const s of decor.stars){
      const tw = 0.5 + 0.5*Math.sin(G.t*0.05 + s.p);
      ctx.fillStyle = `rgba(255,250,220,${(0.25 + 0.75*tw) * p.night})`;
      ctx.beginPath(); ctx.arc(s.x, s.y, s.r, 0, 6.283); ctx.fill();
    }
  }
  /* sun / moon */
  const isMoon = p.night > 0.55;
  const sx = 300, sy = 80 + p.night * 90;
  ctx.save();
  ctx.shadowColor = isMoon ? 'rgba(220,230,255,.9)' : 'rgba(255,214,102,.9)';
  ctx.shadowBlur = 34;
  ctx.fillStyle = isMoon ? '#f4f2ff' : '#ffd166';
  ctx.beginPath(); ctx.arc(sx, sy, 26, 0, 6.283); ctx.fill();
  ctx.restore();
  ctx.save(); ctx.translate(sx, sy);
  if (isMoon){
    ctx.fillStyle = 'rgba(180,180,220,.5)';
    ctx.beginPath(); ctx.arc(-8,-6,5,0,6.283); ctx.arc(9,7,3.5,0,6.283); ctx.arc(3,-12,3,0,6.283); ctx.fill();
    ctx.strokeStyle = '#5b5b8a'; ctx.lineWidth = 1.6;
    ctx.beginPath(); ctx.arc(-6,-3,2.2,3.3,6.0); ctx.arc(8,2,2.2,3.3,6.0); ctx.stroke();
    ctx.fillStyle = '#5b5b8a'; ctx.beginPath(); ctx.arc(0,7,3,0,3.14); ctx.stroke();
  } else {
    ctx.rotate(G.t*0.004);
    ctx.fillStyle = 'rgba(255,220,140,.55)';
    for (let i=0;i<8;i++){ ctx.rotate(Math.PI/4);
      ctx.fillRect(30, -2.5, 12, 5); }
    ctx.rotate(-G.t*0.004);
    ctx.fillStyle = '#fff8e0'; ctx.beginPath(); ctx.arc(0,0,18,0,6.283); ctx.fill();
    ctx.fillStyle = '#3a2f22';
    ctx.beginPath(); ctx.arc(-6,-2,2.6,0,6.283); ctx.arc(6,-2,2.6,0,6.283); ctx.fill();
    ctx.strokeStyle = '#3a2f22'; ctx.lineWidth = 1.8;
    ctx.beginPath(); ctx.arc(0,3,5,0.25,2.9); ctx.stroke();
    ctx.fillStyle = 'rgba(255,150,150,.5)';
    ctx.beginPath(); ctx.arc(-10,4,3.4,0,6.283); ctx.arc(10,4,3.4,0,6.283); ctx.fill();
  }
  ctx.restore();

  /* clouds */
  for (const c of decor.clouds){
    ctx.fillStyle = rgba(p.cloud, 0.85);
    cloudPath(ctx, c.x, c.y, c.s); ctx.fill();
  }
  /* distant flock */
  ctx.strokeStyle = `rgba(43,33,64,${0.35 - p.night*0.2})`; ctx.lineWidth = 1.6;
  for (const f of decor.flock){
    for (let i=0;i<f.n;i++){
      const bx = f.x + i*11, by = f.y + Math.sin(G.t*0.06 + i)*2;
      ctx.beginPath();
      ctx.moveTo(bx-5, by); ctx.quadraticCurveTo(bx-2, by-3, bx, by);
      ctx.quadraticCurveTo(bx+2, by-3, bx+5, by); ctx.stroke();
    }
  }
  /* hills */
  ctx.fillStyle = rgba(p.hillFar, 1);
  hillPath(ctx, 372, 26, 0.012, G.t*0.35); ctx.fill();
  ctx.fillStyle = rgba(p.hillNear, 1);
  hillPath(ctx, 430, 30, 0.016, G.t*0.7); ctx.fill();
  /* trees on near hill */
  ctx.fillStyle = 'rgba(30,60,40,.30)';
  for (let i=0;i<12;i++){
    const tx = ((i*57 - G.t*0.7) % (VW+60) + (VW+60)) % (VW+60) - 30;
    const ty = 428 + Math.sin((tx+G.t*0.7)*0.016)*30 + Math.sin((tx+G.t*0.7)*0.037)*10;
    ctx.beginPath(); ctx.arc(tx, ty, 9, 0, 6.283); ctx.fill();
    ctx.fillRect(tx-1.5, ty, 3, 10);
  }
  /* balloons */
  for (const bl of decor.balloons){
    if (bl.x < -60 || bl.x > VW+60) continue;
    const by = bl.y + Math.sin(G.t*0.02 + bl.bob)*10;
    ctx.save(); ctx.globalAlpha = 0.9;
    ctx.fillStyle = bl.hue;
    ctx.beginPath(); ctx.ellipse(bl.x, by, 17, 21, 0, 0, 6.283); ctx.fill();
    ctx.fillStyle = 'rgba(255,255,255,.35)';
    ctx.beginPath(); ctx.ellipse(bl.x-6, by-6, 5, 8, -0.4, 0, 6.283); ctx.fill();
    ctx.strokeStyle = '#5b4a3a'; ctx.lineWidth = 1.4;
    ctx.beginPath(); ctx.moveTo(bl.x-5, by+20); ctx.lineTo(bl.x-4, by+28);
    ctx.moveTo(bl.x+5, by+20); ctx.lineTo(bl.x+4, by+28); ctx.stroke();
    ctx.fillStyle = '#a9743f'; rr(ctx, bl.x-7, by+27, 14, 8, 3); ctx.fill();
    ctx.restore();
  }
}

function drawPipe(p){
  const c = p.col;
  const topH = p.gapY + 200, botY = p.gapY + p.gapH, botH = PLAY_H - botY + 10;

  const body = (x, y, h) => {
    if (h <= 0) return;
    const g = ctx.createLinearGradient(x, 0, x + p.w, 0);
    g.addColorStop(0, c.d); g.addColorStop(0.16, c.l);
    g.addColorStop(0.42, c.b); g.addColorStop(0.72, c.l);
    g.addColorStop(1, c.d);
    ctx.fillStyle = g; ctx.fillRect(x, y, p.w, h);
    /* bamboo bands */
    ctx.strokeStyle = 'rgba(43,33,64,.22)'; ctx.lineWidth = 3;
    for (let by = Math.ceil(y/74)*74; by < y + h; by += 74){
      if (by < y || by > y + h) continue;
      ctx.beginPath(); ctx.moveTo(x, by); ctx.lineTo(x + p.w, by); ctx.stroke();
    }
    /* outline */
    ctx.strokeStyle = 'rgba(43,33,64,.35)'; ctx.lineWidth = 2;
    ctx.strokeRect(x, y, p.w, h);
  };
  const cap = (y, h) => {
    const x = p.x - 6, w = p.w + 12;
    const g = ctx.createLinearGradient(x, 0, x + w, 0);
    g.addColorStop(0, c.d); g.addColorStop(0.25, c.l);
    g.addColorStop(0.6, c.b); g.addColorStop(1, c.d);
    ctx.fillStyle = g; rr(ctx, x, y, w, h, 9); ctx.fill();
    ctx.strokeStyle = 'rgba(43,33,64,.45)'; ctx.lineWidth = 2.5; ctx.stroke();
  };

  body(p.x, -200, topH);
  body(p.x, botY, botH);
  cap(p.gapY - 24, 24);
  cap(p.gapY + p.gapH, 24);

  /* little flowers on the top rim */
  const fx = p.x + p.w*0.5, fy = p.gapY - 26;
  if (fy > 0){
    ctx.save(); ctx.translate(fx, fy);
    ctx.rotate(Math.sin(G.t*0.03 + p.seed)*0.12);
    ctx.fillStyle = '#fff0f5';
    for (let i=0;i<5;i++){ ctx.rotate(6.283/5);
      ctx.beginPath(); ctx.ellipse(0, -7, 4, 5, 0, 0, 6.283); ctx.fill(); }
    ctx.fillStyle = '#ffd166'; ctx.beginPath(); ctx.arc(0,0,3.2,0,6.283); ctx.fill();
    ctx.restore();
  }
}

function drawPickup(it, p){
  if (it.taken) return;
  const x = p.x + p.w/2, y = it.y + Math.sin(G.t*0.06 + it.ph)*6;
  const pulse = 1 + Math.sin(G.t*0.1 + it.ph)*0.06;
  ctx.save(); ctx.translate(x, y); ctx.scale(pulse, pulse);
  if (it.type === 'star'){
    ctx.rotate(G.t*0.03);
    ctx.shadowColor = 'rgba(255,210,80,.9)'; ctx.shadowBlur = 16;
    ctx.fillStyle = '#ffd166';
    starPath(ctx, 0, 0, 12, 5.4, 5, 0); ctx.fill();
    ctx.shadowBlur = 0;
    ctx.strokeStyle = '#2b2140'; ctx.lineWidth = 2; ctx.stroke();
  } else {
    ctx.rotate(Math.sin(G.t*0.04)*0.25);
    ctx.shadowColor = 'rgba(160,220,255,.9)'; ctx.shadowBlur = 14;
    ctx.fillStyle = '#cdeeff';
    ctx.beginPath(); ctx.moveTo(-11,7); ctx.quadraticCurveTo(-2,-11,11,-9);
    ctx.quadraticCurveTo(6,6,-5,9); ctx.quadraticCurveTo(-8,10,-11,7);
    ctx.closePath(); ctx.fill();
    ctx.shadowBlur = 0;
    ctx.strokeStyle = '#2b2140'; ctx.lineWidth = 2; ctx.stroke();
    ctx.beginPath(); ctx.moveTo(-7,5); ctx.quadraticCurveTo(2,-2,8,-7); ctx.stroke();
  }
  ctx.restore();
}

function drawBird(){
  const b = G.bird;
  ctx.save();
  ctx.translate(b.x, b.y);
  ctx.rotate(b.rot);

  /* tail */
  ctx.fillStyle = '#f0b429'; ctx.strokeStyle = '#2b2140'; ctx.lineWidth = 2;
  ctx.beginPath(); ctx.moveTo(-13,-2); ctx.lineTo(-23,-6); ctx.lineTo(-21,3); ctx.closePath();
  ctx.fill(); ctx.stroke();

  /* back wing */
  const wa = Math.sin(b.wing) * (b.glide > 0 ? 0.35 : 0.95) + (b.glide > 0 ? -0.5 : 0);
  ctx.save(); ctx.translate(-2, -4); ctx.rotate(wa);
  ctx.fillStyle = '#f7c948';
  ctx.beginPath(); ctx.ellipse(-8, 0, 11, 6.5, 0, 0, 6.283); ctx.fill(); ctx.stroke();
  ctx.restore();

  /* body */
  const g = ctx.createLinearGradient(0,-14,0,14);
  g.addColorStop(0,'#ffe066'); g.addColorStop(1,'#f5b81e');
  ctx.fillStyle = g;
  ctx.beginPath(); ctx.ellipse(0, 0, 15.5, 13.5, 0, 0, 6.283); ctx.fill();
  ctx.fillStyle = 'rgba(255,250,220,.85)';
  ctx.beginPath(); ctx.ellipse(2, 4, 9, 7, 0, 0, 6.283); ctx.fill();
  ctx.strokeStyle = '#2b2140'; ctx.lineWidth = 2.2;
  ctx.beginPath(); ctx.ellipse(0, 0, 15.5, 13.5, 0, 0, 6.283); ctx.stroke();

  /* crest */
  ctx.strokeStyle = '#e8a41c'; ctx.lineWidth = 2.4;
  ctx.beginPath(); ctx.moveTo(0,-13); ctx.quadraticCurveTo(-3,-20,-7,-19);
  ctx.moveTo(1,-13); ctx.quadraticCurveTo(2,-21,6,-20); ctx.stroke();

  /* front wing */
  ctx.save(); ctx.translate(1, -2); ctx.rotate(wa*0.9 + 0.15);
  ctx.fillStyle = '#fff3c4';
  ctx.beginPath(); ctx.ellipse(-7, 1, 12, 7, 0, 0, 6.283); ctx.fill(); ctx.stroke();
  ctx.restore();

  /* beak */
  ctx.fillStyle = '#ff9f43';
  ctx.beginPath(); ctx.moveTo(12,-2); ctx.lineTo(22,0); ctx.lineTo(12,3); ctx.closePath();
  ctx.fill(); ctx.stroke();

  /* eyes */
  const blink = (G.t % 190 < 8) ? 0.15 : 1;
  ctx.fillStyle = '#fff';
  ctx.beginPath(); ctx.ellipse(5, -5, 5.2, 5.4*blink, 0, 0, 6.283); ctx.fill();
  ctx.beginPath(); ctx.ellipse(-3, -4.5, 4.2, 4.6*blink, 0, 0, 6.283); ctx.fill();
  ctx.fillStyle = '#2b2140';
  ctx.beginPath(); ctx.ellipse(6.6, -5, 2.6, 2.9*blink, 0, 0, 6.283); ctx.fill();
  ctx.beginPath(); ctx.ellipse(-2.4, -4.5, 2.2, 2.5*blink, 0, 0, 6.283); ctx.fill();
  if (blink > 0.5){
    ctx.fillStyle = '#fff';
    ctx.beginPath(); ctx.arc(7.6, -6.4, 1.1, 0, 6.283); ctx.arc(-1.6, -5.8, 0.9, 0, 6.283); ctx.fill();
  }
  /* blush */
  ctx.fillStyle = 'rgba(255,140,170,.55)';
  ctx.beginPath(); ctx.arc(3, 2.5, 3.6, 0, 6.283); ctx.fill();
  ctx.beginPath(); ctx.arc(-6, 3, 3, 0, 6.283); ctx.fill();

  /* glide aura */
  if (b.glide > 0){
    ctx.strokeStyle = `rgba(255,255,255,${0.25 + 0.2*Math.sin(G.t*0.2)})`;
    ctx.lineWidth = 2;
    ctx.beginPath(); ctx.ellipse(0,0,22,19,0,0,6.283); ctx.stroke();
  }
  ctx.restore();
}

function drawGround(){
  const p = currentSky();
  const soil = p.night > 0.5 ? '#3a2a3f' : '#7a5138';
  const soil2 = p.night > 0.5 ? '#241a2a' : '#5c3a26';
  const grass = p.night > 0.5 ? '#2c4a3a' : '#5fc07f';
  const grass2 = p.night > 0.5 ? '#1e3628' : '#3f9c60';
  const g = ctx.createLinearGradient(0, PLAY_H, 0, VH);
  g.addColorStop(0, soil); g.addColorStop(1, soil2);
  ctx.fillStyle = g; ctx.fillRect(-30, PLAY_H, VW+60, GROUND_H+30);
  ctx.fillStyle = grass; ctx.fillRect(-30, PLAY_H, VW+60, 16);
  ctx.fillStyle = grass2;
  for (const bl of decor.grass){
    ctx.save();
    ctx.translate(bl.x, PLAY_H + 14);
    ctx.rotate(Math.sin(G.t*0.03 + bl.x*0.1)*0.12);
    ctx.beginPath(); ctx.moveTo(0,0); ctx.lineTo(2.5, -bl.h); ctx.lineTo(5, 0);
    ctx.closePath(); ctx.fill();
    ctx.restore();
    if (bl.k > 0.86){
      const fx = bl.x + 6, fy = PLAY_H + 12 - bl.h;
      ctx.fillStyle = pick(['#ff8fb1','#ffd166','#fff','#c39bff']);
      ctx.beginPath(); ctx.arc(fx, fy, 3, 0, 6.283); ctx.fill();
      ctx.fillStyle = '#e8a41c';
      ctx.beginPath(); ctx.arc(fx, fy, 1.2, 0, 6.283); ctx.fill();
    }
  }
  /* pebbles */
  ctx.fillStyle = 'rgba(0,0,0,.14)';
  for (let i=0;i<10;i++){
    const px = ((i*61 - G.t*0.9) % (VW+40) + (VW+40)) % (VW+40) - 20;
    ctx.beginPath(); ctx.ellipse(px, PLAY_H + 34 + (i%3)*14, 5, 3, 0, 0, 6.283); ctx.fill();
  }
}

function drawParticles(){
  for (const p of G.parts){
    ctx.save();
    ctx.globalAlpha = clamp(p.life, 0, 1);
    ctx.translate(p.x, p.y); ctx.rotate(p.rot);
    if (p.kind === 'heart'){
      ctx.fillStyle = p.col; heartPath(ctx, 0, 0, p.size); ctx.fill();
    } else if (p.kind === 'star'){
      ctx.fillStyle = p.col; starPath(ctx, 0, 0, p.size, p.size*0.45, 5, p.rot); ctx.fill();
    } else if (p.kind === 'puff'){
      ctx.fillStyle = 'rgba(255,255,255,'+ (p.life*0.7) +')';
      ctx.beginPath(); ctx.arc(0, 0, p.size*(2 - p.life), 0, 6.283); ctx.fill();
    } else if (p.kind === 'spark'){
      ctx.fillStyle = p.col;
      ctx.beginPath(); ctx.moveTo(0,-p.size); ctx.lineTo(p.size*0.35,0);
      ctx.lineTo(0,p.size); ctx.lineTo(-p.size*0.35,0); ctx.closePath(); ctx.fill();
    } else if (p.kind === 'feather'){
      ctx.fillStyle = p.col;
      ctx.beginPath(); ctx.ellipse(0, 0, p.size, p.size*0.5, 0, 0, 6.283); ctx.fill();
    } else if (p.kind === 'text'){
      ctx.fillStyle = p.col;
      ctx.font = `700 ${p.size}px "Baloo 2", cursive`;
      ctx.fillText(p.txt, 0, 0);
    }
    ctx.restore();
  }
  for (const f of G.floats){
    ctx.save();
    ctx.globalAlpha = clamp(f.life, 0, 1);
    ctx.font = '800 20px "Baloo 2", cursive';
    ctx.textAlign = 'center';
    ctx.lineWidth = 4; ctx.strokeStyle = '#fff'; ctx.lineJoin = 'round';
    ctx.strokeText(f.txt, f.x, f.y);
    ctx.fillStyle = f.col; ctx.fillText(f.txt, f.x, f.y);
    ctx.restore();
  }
}

function drawHUD(){
  if (G.state === 'ready'){
    ctx.save();
    ctx.textAlign = 'center';
    ctx.font = '800 30px "Baloo 2", cursive';
    ctx.fillStyle = '#fff'; ctx.strokeStyle = '#2b2140'; ctx.lineWidth = 5;
    ctx.lineJoin = 'round';
    ctx.strokeText('tap to fly', VW/2, 210);
    ctx.fillText('tap to fly', VW/2, 210);
    ctx.font = '700 14px Nunito, sans-serif';
    ctx.strokeText('space · click · tap the button', VW/2, 232);
    ctx.fillStyle = '#5d5375'; ctx.fillText('space · click · tap the button', VW/2, 232);
    ctx.restore();
  }
  if (G.state === 'playing' || G.state === 'dying'){
    ctx.save();
    ctx.textAlign = 'center';
    ctx.font = '800 46px "Baloo 2", cursive';
    ctx.lineWidth = 7; ctx.lineJoin = 'round';
    ctx.strokeStyle = '#2b2140'; ctx.fillStyle = '#fff8e6';
    ctx.strokeText(G.score, VW/2, 78);
    ctx.fillText(G.score, VW/2, 78);
    ctx.restore();
  }
}

function render(){
  const sx = (Math.random()*2-1) * G.shake;
  const sy = (Math.random()*2-1) * G.shake;
  ctx.save();
  ctx.translate(sx, sy);
  drawSky();
  for (const p of G.pipes){
    if (p.x > VW + 20 || p.x + p.w < -20) continue;
    drawPipe(p);
    for (const it of p.items) drawPickup(it, p);
  }
  drawGround();
  drawParticles();
  drawBird();
  drawHUD();
  ctx.restore();

  if (G.flash > 0.01){
    ctx.fillStyle = `rgba(255,255,255,${G.flash})`;
    ctx.fillRect(0, 0, VW, VH);
  }
}

/* ══════════════════════════════════════════════════════════════════════
   5 · overlays & sidebar
   ══════════════════════════════════════════════════════════════════════ */
const overlay = $('#overlay');
function hideOverlay(){ overlay.classList.remove('show'); }
function showOverlay(html){ overlay.innerHTML = html; overlay.classList.add('show'); }

function starPoints(cx, cy, r1, r2, n){
  let pts = [];
  for (let i=0;i<n*2;i++){
    const r = i%2 ? r2 : r1, a = (i*Math.PI/n) - Math.PI/2;
    pts.push((cx + Math.cos(a)*r).toFixed(1) + ',' + (cy + Math.sin(a)*r).toFixed(1));
  }
  return pts.join(' ');
}
function medalSVG(m){
  return `<svg class="medal" width="70" height="70" viewBox="0 0 64 64">
    <defs><radialGradient id="mg-${m.name.replace(/\s/g,'')}">
      <stop offset="0" stop-color="${m.light}"/><stop offset="1" stop-color="${m.dark}"/>
    </radialGradient></defs>
    <circle cx="32" cy="32" r="26" fill="url(#mg-${m.name.replace(/\s/g,'')})" stroke="#2b2140" stroke-width="3"/>
    <polygon points="${starPoints(32,32,15,6.5,5)}" fill="#fffdf6" stroke="#2b2140" stroke-width="2"/>
  </svg>`;
}
function medalFor(s){ let m = MEDALS[0]; for (const x of MEDALS) if (s >= x.min) m = x; return m; }

function showReady(){
  showOverlay(`<div class="ov-card">
    <div class="ov-ribbon">flight #${G.runs || 1}</div>
    <h2>Ready to flap?</h2>
    <p class="ov-quip">Pip is a very small bird with very large dreams.</p>
    <button class="btn big mint wide" id="startBtn">let's fly</button>
    <p class="ov-quip" style="margin:12px 0 0">or press <kbd>space</kbd></p>
  </div>`);
  updateSidebar();
}
function showOver(){
  const m = medalFor(G.score);
  const isBest = G.score >= G.best && G.score > 0;
  showOverlay(`<div class="ov-card">
    <div class="ov-ribbon ${isBest ? 'newbest' : ''}">${isBest ? 'new best!' : 'flight over'}</div>
    <div style="display:flex;justify-content:center;margin:10px 0 0">${medalSVG(m)}</div>
    <h2>${m.name}</h2>
    <p class="ov-quip">"${pick(QUIPS)}"</p>
    <div class="ov-scores">
      <div><span>score</span><b>${G.score}</b></div>
      <div><span>best</span><b>${G.best}</b></div>
    </div>
    <button class="btn big mint wide" id="startBtn">fly again</button>
    <p class="ov-quip" style="margin:12px 0 0">or press <kbd>space</kbd></p>
  </div>`);
  sfx.over();
  updateSidebar();
}
function showPause(){
  showOverlay(`<div class="ov-card">
    <div class="ov-ribbon">nap time</div>
    <h2>Paused</h2>
    <p class="ov-quip">Pip is catching up on a very important dream.</p>
    <button class="btn big wide" id="resumeBtn">resume</button>
  </div>`);
}

/* ── sidebar ── */
function setTxt(el, v){ if (el && el.textContent !== v) el.textContent = v; }
function setBar(el, v){ if (el) el.style.width = clamp(v,0,100) + '%'; }
const els = {
  sScore:$('#sScore'), bScore:$('#bScore'), sPipes:$('#sPipes'), bPipes:$('#bPipes'),
  sFlaps:$('#sFlaps'), bFlaps:$('#bFlaps'), sPerf:$('#sPerf'), bPerf:$('#bPerf'),
  sSpeed:$('#sSpeed'), bSpeed:$('#bSpeed'), sGap:$('#sGap'), bGap:$('#bGap'),
  sAlt:$('#sAlt'), state:$('#stateLabel'), best:$('#bestLabel'), sky:$('#skyNote'),
  medalList:$('#medalList')
};
function updateSidebar(){
  const p = currentSky();
  setTxt(els.sScore, String(G.score)); setBar(els.bScore, G.score/1.2);
  setTxt(els.sPipes, String(G.stats.passed)); setBar(els.bPipes, G.stats.passed/1.5);
  setTxt(els.sFlaps, String(G.stats.flaps)); setBar(els.bFlaps, G.stats.flaps/4);
  setTxt(els.sPerf, String(G.stats.perfects)); setBar(els.bPerf, G.stats.perfects*12);
  const sp = speedNow();
  setTxt(els.sSpeed, sp.toFixed(2)); setBar(els.bSpeed, (sp-2.35)/1.35*100);
  const gap = G.pipes.length ? G.pipes[0].gapH : 168;
  setTxt(els.sGap, Math.round(gap)); setBar(els.bGap, (168-gap)/38*100);
  setTxt(els.sAlt, Math.max(0, Math.round((PLAY_H - G.bird.y)/3)) + 'm');
  setTxt(els.state, G.paused ? 'paused' : G.state);
  setTxt(els.best, String(G.best));
  setTxt(els.sky, p.name);
  /* medal list */
  const cur = medalFor(G.score);
  const html = MEDALS.map(m => {
    const won = G.score >= m.min && m === cur;
    const done = G.score >= m.min;
    return `<div class="medal-row ${done ? 'won' : ''}">
      <i style="background:linear-gradient(135deg,${m.light},${m.dark})"></i>
      <span><b style="font-family:'Baloo 2',cursive">${m.name}</b> · ${m.min}+ pts</span>
    </div>`;
  }).join('');
  if (els.medalList.dataset.done !== html){ els.medalList.innerHTML = html; els.medalList.dataset.done = html; }
  drawSpark();
}
function drawSpark(){
  const c = $('#spark'), x = c.getContext('2d');
  const W = c.width, H = c.height;
  x.clearRect(0,0,W,H);
  x.fillStyle = '#eef4fb'; x.fillRect(0,0,W,H);
  if (G.history.length < 2){ return; }
  const step = W / 200;
  x.beginPath();
  G.history.forEach((v,i) => {
    const px = i*step, py = (v / PLAY_H) * (H-10) + 5;
    i ? x.lineTo(px, py) : x.moveTo(px, py);
  });
  x.strokeStyle = '#ff7fa8'; x.lineWidth = 2; x.lineJoin = 'round';
  x.stroke();
  const last = G.history[G.history.length-1];
  x.fillStyle = '#2b2140';
  x.beginPath(); x.arc((G.history.length-1)*step, (last/PLAY_H)*(H-10)+5, 3, 0, 6.283); x.fill();
  x.font = '700 9px Nunito'; x.fillStyle = '#5d5375';
  x.fillText('ground', 4, H-4); x.fillText('sky', 4, 12);
}

/* ══════════════════════════════════════════════════════════════════════
   6 · wiring
   ══════════════════════════════════════════════════════════════════════ */
function loadBest(){ try { return parseInt(localStorage.getItem('pip.best') || '0', 10) || 0; } catch(e){ return 0; } }
function saveBest(){ try { localStorage.setItem('pip.best', String(G.best)); } catch(e){} }
G.runs = parseInt(localStorage.getItem('pip.runs') || '0', 10) + 1;

function restart(){
  try { localStorage.setItem('pip.runs', String(G.runs + 1)); } catch(e){}
  G.runs++;
  resetGame();
  sfx.ui();
}
function togglePause(){
  if (G.state === 'over' || G.state === 'ready') return;
  G.paused = !G.paused;
  if (G.paused) showPause(); else { hideOverlay(); if (G.state === 'playing') {} }
  sfx.ui(); updateSidebar();
}

function action(){
  if (overlay.classList.contains('show')){
    const btn = overlay.querySelector('#startBtn');
    if (G.state === 'over'){ if (G.overAt && performance.now() - G.overAt < 500) return; restart(); return; }
    if (G.state === 'ready' && btn && !btn.contains(document.activeElement)){
      audio(); flap(); return;
    }
  }
  if (G.state === 'over'){ if (performance.now() - G.overAt < 500) return; restart(); return; }
  if (G.paused) return;
  audio();
  flap();
}

document.querySelector('.screen').addEventListener('pointerdown', e => {
  e.preventDefault();
  action();
}, {passive:false});
$('#flapBtn').addEventListener('pointerdown', e => {
  e.preventDefault(); e.stopPropagation();
  if (G.state === 'over'){ restart(); return; }
  if (G.paused) return;
  audio(); flap();
});
overlay.addEventListener('pointerdown', e => {
  if (e.target.closest('#resumeBtn')){ G.paused = false; hideOverlay(); sfx.ui(); return; }
  if (e.target.closest('#startBtn')){
    if (G.state === 'over'){ restart(); }
    else { audio(); flap(); }
    return;
  }
  e.stopPropagation();
  if (G.state === 'over'){ if (performance.now() - G.overAt < 500) return; restart(); }
  else if (G.state === 'ready'){ audio(); flap(); }
});
addEventListener('keydown', e => {
  if (e.repeat) return;
  const k = e.key.toLowerCase();
  if (k === ' ' || k === 'arrowup' || k === 'w' || k === 'enter'){
    e.preventDefault();
    if (G.paused) return;
    if (G.state === 'over'){ if (performance.now() - G.overAt > 500) restart(); }
    else { audio(); flap(); }
  } else if (k === 'p'){ e.preventDefault(); togglePause(); }
  else if (k === 'r'){ e.preventDefault(); restart(); }
});
$('#soundBtn').addEventListener('click', function(){
  muted = !muted;
  this.textContent = muted ? '♪ sound off' : '♪ sound on';
  this.classList.toggle('ghost', !muted);
  if (!muted){ audio(); sfx.ui(); }
});
document.addEventListener('visibilitychange', () => {
  if (document.hidden && G.state === 'playing' && !G.paused){ G.paused = true; showPause(); }
});

/* ── patch the over-state timestamp ── */
const _origShowOver = showOver;
showOver = function(){ G.overAt = performance.now(); _origShowOver(); };

/* ── go ── */
function fit(){
  const dpr = Math.min(window.devicePixelRatio || 1, 2);
  cvs.width = Math.round(VW*dpr); cvs.height = Math.round(VH*dpr);
  ctx.setTransform(dpr, 0, 0, dpr, 0, 0);
  ctx.textBaseline = 'alphabetic';
}
fit();
addEventListener('resize', fit);

G.best = loadBest();
resetGame();

/* floating petals */
(function petals(){
  const amb = document.querySelector('.ambient');
  const cols = ['#ff8fb1','#ffd166','#7fd6b4','#8ec9ea','#c39bff','#ffb4a0'];
  for (let i=0;i<16;i++){
    const p = document.createElement('i');
    p.className = 'petal';
    p.style.left = rnd(-5, 100) + 'vw';
    p.style.top = rnd(-20, 40) + 'vh';
    p.style.background = cols[i % cols.length];
    p.style.animationDuration = rnd(16, 34) + 's';
    p.style.animationDelay = -rnd(0, 30) + 's';
    p.style.transform = `scale(${rnd(0.6, 1.4)})`;
    amb.appendChild(p);
  }
  for (let i=0;i<3;i++){
    const c = document.createElement('div');
    c.className = 'cloud';
    c.style.width = rnd(90, 170) + 'px';
    c.style.height = rnd(38, 62) + 'px';
    c.style.borderRadius = '50%';
    c.style.top = rnd(4, 40) + 'vh';
    c.style.animationDuration = rnd(70, 130) + 's';
    c.style.animationDelay = -rnd(0, 90) + 's';
    c.style.opacity = rnd(0.5, 0.85);
    amb.appendChild(c);
  }
})();

new IntersectionObserver((es, o) => {
  es.forEach(e => { if (e.isIntersecting){ e.target.classList.add('in'); o.unobserve(e.target); } });
}, {threshold:0.15}).observe;

document.querySelectorAll('.reveal').forEach(el => {
  const io = new IntersectionObserver((es, o) => {
    es.forEach(e => { if (e.isIntersecting){ e.target.classList.add('in'); o.disconnect(); } });
  }, {threshold:0.12});
  io.observe(el);
});

/* main loop */
let last = performance.now(), acc = 0;
const STEP = 1000/60;
function loop(now){
  requestAnimationFrame(loop);
  if (G.paused){ last = now; return; }
  let dt = now - last; last = now;
  if (dt > 250) dt = 250;
  acc += dt;
  let guard = 0;
  while (acc >= STEP && guard++ < 6){ update(); acc -= STEP; }
  render();
  scheduleMusic();
  if (G.t % 4 === 0) updateSidebar();
}
requestAnimationFrame(loop);
</script>
</body>
</html>
```

**What's inside**

- **Pip** — a hand-drawn chick with two independently rotated wings, a blinking eye, blushing cheeks, a crest tuft, and a tail that follows the physics.
- **Living world** — a sky that drifts from sunrise → midday → dusk → firefly night as you score (all palettes are interpolated live, so the transition is gradual), rolling hills, parallax clouds, a smiling sun that sets into a sleepy moon, hot-air balloons, and a distant flock of "v" birds.
- **Bamboo pipes** with cylinder shading, banded segments, and little flowers that tilt on the top rim — in four candy colors, chosen per pipe.
- **Pickups** — stars (+2) and glide feathers (three seconds of floaty fall with a sparkly trail), both floating in the gap with a bob.
- **Perfect pass** bonus for threading the dead center of the gap: big sparkle burst, +2, and a chime arpeggio.
- **Everything is synthesized** — flap whooshes, two-note score dings, tube-bonk on the ceiling, crash noise, and a gentle pentatonic loop that stops when you crash.
- **Feel** — screen shake + white flash on impact, feather explosion, floating score text, heart particles on every flap, and a live "flight path" sparkline in the sidebar tracking your altitude.

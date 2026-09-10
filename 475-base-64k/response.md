# 475-base raw response

Served worktree SHA: aab2aca74cf105a6d6ab124c221b7d5b761403e9

## Reasoning

The user wants an adorable, cute, beautiful Flappy Bird game in a single HTML file, with all assets generated procedurally (canvas drawing). Let me make it genuinely charming and polished.

Design direction: cute, beautiful. I want a dreamy sky with sunset gradients, parallax clouds, floating islands, pipes as cheerful pipes, and a little bird with a blinking eye, wing flap animation, squash-and-stretch, feather particles, sparkles, trails, screen shake, etc.

I'll open with something characteristic rather than a generic title card. Maybe the start screen is a diegetic "menu world" where the bird hovers over a sleep-state scene, with a title treatment that's more like a hand-painted game logo. I'll pair a distinctive display face with a readable body face — Google Fonts? No external assets allowed... "no external assets" — fonts from Google Fonts are external. Hmm. I'd rather err on the safe side and use a distinctive stack of built-in fonts. Let me pick something distinctive: display could be a chunky rounded serif — options widely available: 'Chalkboard SE', 'Trebuchet MS', 'Georgia', 'Palatino', 'Rockwell', 'Baskerville'... Also, I could embed a tiny custom-drawn logo in canvas for the title, which is really the "no external assets" spirit.

I'll do: title drawn in canvas (custom letterforms? too much work). Alternative: use CSS with a font stack like `'Chalkboard SE', 'Comic Sans MS', 'Baloo', system-ui` — hmm, Comic Sans is a bit much. Let's think: cute + beautiful. A nice pairing available cross-platform without downloads:
- Display: `'Georgia', 'Playfair Display', serif` — too formal for cute.
- Rounded sans: `'Avenir Rounded', 'Nunito', 'Trebuchet MS', 'Segoe UI', sans-serif`.

Actually I could embed a small woff2 base64... too heavy to hand-write. Better: draw the big title in canvas with custom-drawn chunky letters? That's actually feasible and impressive: I can build a tiny "pixel/vector" letterform system for the title letters only. Hmm, risky to look bad.

Alternative good approach: Use CSS text with layered strokes to make a chunky game-logo look: thick dark outline via text-shadow layers, slight rotation per-letter with spans, gradient-free but with warm cream fill + pink inner shadow. That reads as "handmade game logo" and is distinctive. I'll generate per-letter spans with JS for a bouncy title. Good.

Font pairing: For a cute arcade game, a chunky display serif with heavy stroke contrast reads "hand-painted arcade" — e.g., `'Bookman Old Style', 'Georgia', serif` for the logo, paired with `'Trebuchet MS', 'Verdana', sans-serif` for body/UI. Hmm, "Trebuchet MS" is decent and widely available on Windows/Mac. Alternatively `'Chalkboard SE', 'Comic Sans MS', 'Chalkboard', 'Trebuchet MS'` for playful. Cute is the priority, so let me lean playful. Actually I'll use for the logo a stack: `'Chalkboard SE','Chalkboard','Marker Felt','Trebuchet MS',sans-serif` with heavy weight, letter outlines, per-letter bounce. Body/UI: `'Trebuchet MS','Verdana',sans-serif`. And numbers/score in a mono: `'Courier New', monospace` bold for HUD numbers gives arcade feel. Hmm, for HUD score I'd rather draw it in canvas with custom chunky digits — actually I can draw digits with canvas fillText using a font stack and heavy stroke — looks good with outline. Let's do score drawn on canvas with thick outline, big, top center. That's characteristic: Flappy Bird's big number.

Layout: For a game, the game itself should be the focus. Let me make a full-viewport scene: the game canvas as a "window" (portrait arcade cabinet feel) centered in a cozy ambient room/sky scene with parallax, decorative elements, a side panel with controls/legend/stats (not a generic hero).

Actually maybe better: the whole page IS the game world — canvas fills a framed panel with a layered ambient background behind (animated gradient sky, drifting clouds, sun, distant hills), and a side rail with the title, stats (best, coins, birds collected), and controls legend. On narrow screens it stacks.

Gameplay features for "ultimate":
- Smooth physics: gravity, flap impulse, rotation tied to velocity, terminal velocity.
- Pipes: cute green pipes with faces? Faces on pipes might be too much. Instead: pipes as candy-cane / bubblegum columns with leaves and a "mouth" rim, plus alternative flower-tube obstacles. Let me do: pipes drawn as glossy stems with rim "lips", tiny ladybug or leaf decoration, and later variants (crystal, candy).
- Moving pipes (horizontal drift) at higher scores, and moving "float" gaps.
- Collectibles: stars/cherries between pipes for combo bonus; a golden feather.
- Power-ups: bubble shield, slow-mo ("time syrup"), magnet? Keep it manageable: shield bubble (one free hit), star trail (invincible dash?), and cherries to collect for score multiplier. Let's implement:
  - **Cherries** (spawn in gaps & arcs): collect 5 → +1 extra life? Better: cherries = points & fill a "Sparkle meter"; when full, triggers Star Power (invincible, glowing, pipes become pastel, 4s).
  - **Bubble shield** pickup: absorbs one hit, pops with particles.
- Day/night cycle by altitude/time: sky shifts from dawn → day → sunset → night with stars & fireflies. This gives a "beautiful" payoff. Sky transitions every 20 pipes with distinct palettes.
- Weather: drifting petals/leaves, occasional rain in night? Keep petals + snow in one biome? Let's do drifting particles themed per biome (petals, dandelion fluff, embers, snow, star motes).
- Parallax layers: far mountains, mid hills with trees, near bushes, floating clouds, birds silhouettes flying by, hot air balloon occasionally.
- Trail behind bird + sparkle particles + feather burst on death.
- Screen shake on hit, hitstop, flash, vignette pulse.
- Audio: procedural WebAudio (flap = noise+pitch blip, ping on collect, sad trombone-ish on death, chord progression ambient? maybe simple arpeggio music with square/triangle waves and a toggle). Music toggle + SFX toggle. Also a little "checkpoint" chime.
- Difficulty ramp with speed increase, and a "gentle mode" (cozy) vs "classic" vs "spicy" difficulty selection — cute and accessible.
- Pause with P/Esc. Restart with R. Space/click/tap to flap. Keyboard + mouse + touch.
- Practice mode? maybe "Cozy mode" where hitting a pipe slows you down and you recover. Keep simple: Cozy = forgiving hitboxes + slower.
- Persistence: best score in localStorage (fine), plus cherry total, and unlocked hat! Hats: the bird can wear a hat you unlock... cute! Cosmetic hats unlocked by score milestones: none / party hat (5) / straw hat (15) / crown (30) / wizard (50) / astronaut helmet? Let's do hats: party cone, daisy crown, tiny crown, wizard hat, and cap. Chosen automatically by best score, or selectable in side rail. Nice: hat selector chips in the side panel, unlocked at milestones. That adds depth and cuteness.
- Bird variety: maybe unlockable bird color palettes (peach, mint, lilac, "boba") unlocked with cherries. Spending cherries? That gets complex — instead: unlock palettes at milestone, click to switch. Let's include 4 bird skins: "Peach", "Mint", "Bubbles" (blue), "Mochi" (pink), "Grim" (a tiny bat? no). Unlock by score milestones. Clicking a locked one shows a wiggle/lock state.
- Ghost of best run? Nice touch: a faint ghost bird replaying your best run. That's actually doable: record positions each frame + input timestamps, then replay. But replay needs same seed & speed... Could be a nice "ghost" but might desync. Skip — instead: "best" marker line in the pipe counter? Skip.
- Score popups, combo text ("Sweet!", x3 combo), medal at end (paper, bronze, silver, gold, "sparkle").
- Ground: scrolling grass with tiny flowers, mushrooms, and a little trail. Bird dies on ground; ground scrolls.
- Death: bird goes limp, falls, tumbles, screen shake, then game over panel with score/best/medal and restart prompt.

Start screen: title logo bouncing, bird hovering, "tap to fly" prompt with pulsing arrow, hint text, and the world already animating behind (pipes not spawned). Also a little demo: bird auto-flies? Might be distracting. Keep: idle bird bobbing with blinking eyes, clouds drifting, petals falling.

Visual style: a cute rounded world, which fits the subject perfectly. Colors: a shifting multi-hue dreamy sky (dawn peach → mint day → sunset coral → dusk periwinkle → night deep teal), with cream UI, chocolate outlines, candy accents. I'll steer clear of the flat indigo/violet look — it's not the cozy mood I want. Use warm pastel + saturated candy accents (bubblegum pink, lemon, mint, tangerine). Fine.

Outline-everything style (dark chocolate #3a2b2f outlines, 2-3px) = cute sticker aesthetic. Add soft shadows. Chunky rounded buttons with press animation.

Implementation plan (single file, ~1500-2500 lines). Structure:

HTML:
- `<div class="stage">` ambient background layers (CSS animated gradient sky, drifting cloud divs, sun glow, floating sprinkles) behind everything.
- Left/right: `.cab` containing canvas + side rail (`.rail`) with:
  - Logo block (bouncy letters, subtitle)
  - HUD stats: SCORE big mono, BEST, CHERRIES, SPARKLE meter bar
  - Mode chips (Cozy / Classic / Spicy)
  - Hat chips, Bird chips (unlockable)
  - Sound toggles, help/keys legend
  - Below canvas: a "receipt"/ticker strip
- Custom SVG-ish icons drawn inline as CSS/HTML? I'll draw small icons with inline SVG (allowed — no external assets).
- Overlay panels drawn in DOM (menu/gameover) or canvas? Doing the start/gameover panels in DOM lets me use the fonts and hover micro-interactions. But the "diegetic" charm may be better on canvas. Hybrid: HUD + panels in DOM (with the cute sticker style), gameplay + score in canvas. Actually score in canvas with outline is more arcade-like. Let's do score in canvas (top center, big) and the rest in DOM.

Hmm — careful: keeping DOM side rail + canvas means responsive care. Canvas fixed internal resolution with DPR scaling, CSS aspect ratio ~ 3:4 portrait or 5:4? Flappy is portrait; on desktop wide screens portrait canvas centered with rail on left looks great. Let's do canvas aspect 5:7 (e.g., 520x728 internal) with max-height 92vh.

Let me design the layout: full viewport, flex row, gap: left rail (~300px) with title/stats, then game "cabinet" panel (canvas + bezel), then a narrow right column with keys + credits. On narrow screens stack. That's a "cabinet" arrangement rather than a flat row of identical panels — the three zones each have their own role.

Now the meat: the game code.

Core systems:
1. Canvas setup with DPR, logical size W=520,H=728 (scaled to fit).
2. Game state machine: 'menu' | 'ready' (first flap starts) | 'play' | 'dead' | 'over' | 'paused'.
3. World scroll speed px/s, gravity, flap velocity, pipe spacing, gap size ramping.
4. Pipes: array of {x, gapY, gapH, w, passed, bobPhase (moving), seed, decorations}.
5. Cherries: spawned along arcs / mid-gap.
6. Pickups: bubble shield occasionally.
7. Particles: generic pool with types (spark, feather, ring, puff, heart, text popup).
8. Parallax: clouds (multiple depths), hills, bushes, ground details, background birds, balloon.
9. Sky: biome list with gradient stops, decor colors, particle type; blend between biomes by distance (lerp colors).
10. Bird: squash/stretch, rotation, wing phase, blink, trail, invincible glow, dead tumble.
11. Camera shake, hitstop (time freeze frames), flash.
12. Audio: WebAudio synth — SFX + simple procedural music (a cheerful arpeggio loop scheduled with lookahead). Music should be cute: I, IV, V, vi in C major with triangle lead, soft bass, noise "shaker". Keep it optional and quiet.
13. Input: pointerdown, keydown (Space/ArrowUp/KeyW), R restart, P pause, M mute, 1-3 hats maybe.
14. Pause overlay, game over DOM panel with medal, score animation counting up, "new best" ribbon.
15. Ghost/best marker: skip.
16. Mobile: touch flap, layout stacks, panel sizes.

Extra polish: 
- The rail's sparkle meter fills and glows; "STAR POWER" triggers with color shift + camera zoom.
- Pipe "kiss" close-call: passing within N px without hit → "Close one!" + bonus point.
- Score gates every 10 points: flash + tempo up + speed.
- Trail of sparkles behind bird in star power.

Let me write the code carefully.

Key constants (logical units, 520x728 canvas):
- groundH = 96
- gravity ~ 1650 px/s²
- flap impulse: -520 px/s
- max fall speed 720
- base scroll 190 px/s → up to 300
- pipe w 76, gap 210 → 165 min
- spacing 240 px (time-based spawn: distance)

Bird x fixed at ~150, r ~15 collision radius (cozy: 11).

dt clamp; substep collisions for high speed (2-3 substeps).

Sky biomes (each 22 pipes? use distance):
1. Dawn Peach: sky #ffd9b0 → #ffb3c1 → #ffe9d0? Let's define top/bottom colors + sun/moon + fog + particle type.
Biomes: 
- "Peachrise": top #FFC9A8, mid #FFDDE0, low #FFEFCF, hills #F49AB0/#B5E3C0...
Let me write concrete palettes:

```
BIOMES = [
 {name:'Peachrise', sky:['#FFD1A6','#FFE0C2','#FFF1D6'], hills:['#F5A8B8','#E58AA0'], trees:'#8FD9B8', fog:'#FFE8CE', particle:'petal', particleColor:'#FFB3C6', night:false},
 {name:'Mintnoon', sky:['#A7E6E0','#CFF3E3','#F0FBEA'], hills:['#8AD6B0','#5FBFA0'], trees:'#4FA97F', fog:'#DBF5EC', particle:'fluff', particleColor:'#FFFFFF'},
 {name:'Coralset', sky:['#FF9E7A','#FFC46B','#FFE7A2'], hills:['#E77A86','#C0566B'], trees:'#6BA37E', fog:'#FFD9A8', particle:'leaf', particleColor:'#FFB26B'},
 {name:'Dusksea', sky:['#7F9CD8','#A9B8E8','#E8C7D8'], hills:['#6B7BC0','#4C5A9E'], trees:'#5A6FA8', fog:'#B8C3EA', particle:'star', particleColor:'#FFF3B0', night:true},
 {name:'Nightbath', sky:['#2A3A6B','#3E5A8C','#6C87A8'], hills:['#2C4A63','#1D3348'], trees:'#2A4A57', fog:'#3A5578', particle:'snow', particleColor:'#DFF3FF', night:true, stars:true},
 {name:'Auroramilk', sky:['#123B4A','#1E6E6A','#7ED0B8'], hills:['#1B5A58','#123F44'], trees:'#3E8C6E', fog:'#2A6A6A', particle:'star', particleColor:'#B7FFE3', night:true, stars:true, aurora:true},
]
```
Then loops back to first with slight speed increase.

Music: switch scale/mood for night biomes (minor-ish). Nice.

Pipes: draw as candy stems: body with vertical gradient, rounded cap rims with lips, highlight stripe, dots pattern; a few variants: 'stem' (green candy), 'bamboo'? variants by biome: candy cane in winter, crystal at night, bamboo mint, coral tube. I'll implement draw with a couple of variants driven by biome index, plus per-pipe decorations: leaf, mushroom, flower, ladybug, icicle, glowmoss. Deterministic by seed.

Cherries: pair of cherries with stem + leaf, glossy; rotate & bob. Collect → sparkle + sound + meter + 10pts? Points: pipe = 1, cherry = 1, close-call = 1. Keep score meaningful. Cherry count → "cherries" stat for unlocking skins. 

Skins unlocked by cherries? Simpler: unlocked by best score. Let's do skins unlocked by cherries collected (spend? no, just threshold), hats by score. Actually simpler and clearer: both unlock by milestones, displayed with lock icons and requirement text. Let's:
- Hats: Party (score 5), Daisy (12), Crown (25), Wizard (45), Astronaut (70)... astronaut helmet as hat is cute. 5 hats + none.
- Bird skins: Peach (default), Mint (best 10), Boba (best 25), Mochi (best 45), Wisp (best 70, glowing ghost bird). Costs? No cost — unlock at best score.
Persist: best, cherries, unlocked derived from best/cherries, selected hat/skin, sound, mode.

Star Power: meter fills 1 per cherry, 6 cherries = full → auto-activate on pickup when full? Better: press Shift/hold? Auto-activate feels better for casual. Activate when meter full (immediately consume meter) → 4.5s invincible, x2 points, rainbow trail, pipes glow-pastel and get knocked away when touched (satisfying!). Yes: during star power, touching a pipe destroys it with a burst and awards a point. That's a delightful mechanic.

Bubble shield: spawns rarely (after score 8+), one at a time; grants shield that absorbs a hit → bird bounces back to center of gap, brief invuln 1s, shield pops.

Also add "feather" powerup? Enough.

Difficulty modes: Cozy (gap 240, speed 165, gravity 1400, forgiving radius, shield chance up), Classic (default), Spicy (gap 175, speed 235, more moving pipes, more cherries).

Moving pipes: pipes with `bob` amplitude start appearing after score 6 (classic), amplitude ramps.

Ceiling/floor: hitting ground = death; ceiling = bonk (lose, but bounce back? Classic flappy: you can't fly above the screen — I'll make ceiling soft: clamp with slight speed loss + sound).

Now: rendering nice details.
- Distant layered hills drawn with sine-based polylines, deterministic per biome.
- Clouds: puffy circles clusters, drawn with alpha, parallax 0.2-0.5.
- Floating islands with trees in the mid layer for "beautiful".
- Background birds: tiny 'v' silhouettes with wing flap.
- Ground: grass top band, dirt below with pebbles, flowers, tiny mushrooms scrolling, plus a soft shadow of bird on ground.
- Foreground: vignette, film-ish soft light, drifting particles, and a subtle "glass" highlight? Keep vignette + warm top light.
- Star power: rainbow trail + bloom-ish overlay (drawn with 'lighter' blending, careful with perf).

Perf: keep particle count reasonable (< 300), draw stars only at night. All fine.

Now let's write the audio synth.

```
class Audio { ctx, master, sfxGain, musicGain, enabled }
```
- init on first user gesture.
- sfx functions: flap (short noise burst + descending sine "whoosh" + triangle blip), boing (shield), ping (cherry: two sine blips, rising), point (soft square pluck), crash (noise burst + downward saw), thud, click, chime (star power: arpeggio up), fanfare (game over with new best), checkpoint.
- music: schedule loop with lookahead using setTimeout/interval; pattern based on biome mood; use square+triangle with short envelopes; light noise shaker. Volume low. Pause when paused/dead.

I'll implement a simple sequencer: 8 steps per bar at ~ 112 BPM, chords I–V–vi–IV, melody notes from pentatonic, bass on beats. Keep code compact and guard against clicks (gain ramps).

Now the DOM layout & CSS. I want it beautiful:

- Background: layered CSS — base deep warm gradient, plus animated soft light sweeps, plus floating CSS dot sprinkles (I'll generate with JS: 30 dots with random positions/durations, `--d` for delay). Use blurred radial gradient "clouds" in warm tones? I'll instead use cloud-ish blurred shapes drifting horizontally (like a sky behind the cabinet) — that reads as "world", plus a vignette. Use `filter: blur(30px)` sparingly. I'll craft: two drifting cloud bands (opacity .35, blur 24px), a top-light radial, and a fine dot pattern with mask fade — a sky-window/world frame with candy stripes and clouds, which is much more characteristic of this game than a flat static backdrop.

Maybe better: background = "candy sky" with slow-drifting clouds and twinkling sprinkles, plus the cabinet bezel.

- Cabinet bezel: dark chocolate rounded frame with a candy-stripe wrapper frame, inner screen with subtle inner shadow and a very light diagonal glass sheen (low opacity) — reads as "arcade", not glassmorphism. Good.

- Rail cards: cream cards with chocolate 2px border, 12px radius, hard offset shadow (`box-shadow: 4px 5px 0 rgba(58,43,47,.18)`), tiny rotation (-0.4deg) for sticker feel, hover: lift + straighten. 

- Title: "FLAPPY" big (48-64px) with per-letter spans, alternating rotation, colors cycling among candy hues with a thick chocolate text-shadow outline; "BIRD" small stamp.

- Chips: pill buttons, chocolate border, cream bg; hover → translateY(-2px) + shadow; selected → candy fill + inset.

- Meters: rounded track with striped candy fill, animated shine.

- Bottom ticker under cabinet: scrolling marquee of cute words. I'll implement marquee with duplicated text and translateX keyframes.

- Scroll reveals: use IntersectionObserver to add `.in` for rail cards — on a game page most content is above fold; still nice for mobile stacked layout. Add.

Micro-interactions: buttons press scale, chips play SFX blip on hover, stat numbers pop-animate when changed (class toggle + animationend), meter overflow sparkle, "STAR POWER" flash on the rail, "×2" indicator.

Keyboard: also arrow keys move a "menu cursor"? No, too much.

Let me now write the code. It's a big single file. Let me be systematic and careful about correctness. I'll write the JS with clear sections. I need to be careful with:
- Correct canvas scaling with DPR and CSS size (fixed aspect ratio via CSS aspect-ratio + max-height; compute internal buffer = displaySize * dpr; use `ctx.setTransform(scale)` with scale = dpr*(displayW/W)).
- Game logic in logical units (W=520,H=728).
- requestAnimationFrame loop with fixed-ish dt clamp.

Now the HTML skeleton:

```html
<div class="bg">
  <div class="bg-sky"></div>
  <div class="cloud c1"></div>... (5)
  <div class="sprinkle" id="sprinkles"></div> (JS-generated)
  <div class="bg-floor"></div>
</div>

<main class="stage">
  <aside class="rail rail-l">
     <div class="brand reveal">... title, subtitle, badge ...</div>
     <div class="card hud reveal">SCORE big, row: BEST / CHERRIES</div>
     <div class="card meter reveal">sparkle meter + star power label</div>
     <div class="card mode reveal">3 mode chips</div>
  </aside>
  <section class="cab reveal">
     <div class="cab-top"> lights, "FLAPPY ARCADE", coin slot </div>
     <div class="screen">
        <canvas id="game"></canvas>
        <div class="screen-glass"></div>
        <div id="toast" class="toast"></div>  (for toast text)
        <div id="panelStart" class="panel show">...</div>
        <div id="panelOver" class="panel">...</div>
        <div id="panelPause" class="panel">...</div>
     </div>
     <div class="cab-bottom">joystick, speaker</div>
  </section>
  <aside class="rail rail-r">
     <div class="card keys">key legend</div>
     <div class="card hats">hat chips</div>
     <div class="card skins">bird chips</div>
     <div class="card sound">music/sfx toggles</div>
     <div class="card tips">rotating tips / fun facts</div>
  </aside>
</main>
<div class="ticker">...</div>
```

Cabinet is really nice visually: a tiny arcade cabinet with marquee, side art, joystick. Let's build a decent cabinet: 
- `.cab` with wood/candy body: I'll go candy-teal frame with pink side rails and chocolate outline — cohesive with the sky and cute.

For the marquee: "FLAPPY BIRD" with a warm glow that flickers slightly.

Let me be careful with responsive: `@media (max-width: 1100px)` → stage becomes column, rails become horizontal wrap rows; canvas smaller. I'll make stage grid: `grid-template-columns: 260px minmax(0,1fr) 250px` on wide; single column below 1080px with rails as flex-wrap rows.

Canvas sizing: `.screen{aspect-ratio:5/7; max-height:calc(100vh - 120px); width:100%}` Hmm with grid the center column width may exceed. Use `.cab{ width:min(100%, calc((100vh - 150px) * 0.714)) }` to preserve proportions. Let's do: `.cab { height: min(calc(100dvh - 96px), 880px); }` and inner screen uses flex to fill height, width determined by aspect ratio. Simpler: set `.screen { height: 100%; aspect-ratio: 5/7; }` inside `.cab{display:flex;justify-content:center}` so height drives width. 

Careful: if height-derived width exceeds column width on short/wide screens, overflow. Add `max-width:100%` and let aspect-ratio adjust → then height shrinks. Use `.screen{aspect-ratio:5/7; max-height:100%;}` and `.cab{justify-content:center}` with `min-height:0`. Simplest robust: `.screen{ width:100%; aspect-ratio:5/7; }` and `.cab{ max-width: calc((100dvh - 120px) * 0.7143); margin-inline:auto }`. That caps cabinet width by viewport height. I'll do that.

Canvas resolution: set from `canvas.getBoundingClientRect()` on resize, DPR-aware, and compute `scale = rect.width / 520`, and set transform each resize.

I'll write `function fitCanvas()` and call on load/resize. Use ResizeObserver on `.screen`.

Canvas overlay DOM: panels centered, plus I'll add canvas-drawn big countdown and score in canvas. Let me draw the big SCORE in canvas (top-center, chunky with outline) — characteristic.

Panels in DOM with cute sticker style — better for hover/click. Start panel: title small, "press space / tap" big pulsing, hint. Game over: card with medal drawn via inline SVG, score/best, cherry count, buttons. Pause panel.

Toasts: canvas-drawn floating text for gameplay (+1, "Close one!", "STAR POWER!"). Fine — canvas fillText with outline. Font in canvas: I need a font string; use `'Trebuchet MS', sans-serif` bold — fine and cross-platform.

CSS palette:
```
--ink:#3b2b2f; --cream:#fff6e6; --butter:#ffe6a7; --lemon:#ffd25e; --bubble:#ff8fab; --mint:#8fe3c2; --sky:#8fd3f4; --tangerine:#ff9f5a; --plum:#7b5ea7;
```
Background: warm sky gradient with mint/lemon.

Type: 
- display: `'Chalkboard SE','Chalkboard','Marker Felt','Comic Sans MS','Trebuchet MS',sans-serif`, weight 700. This gives playful bouncy logo; body uses Trebuchet/Verdana for readability. Score digits: same display stack bold with outline. Good enough.

Let me define CSS vars:
```
--f-body: 'Trebuchet MS','Verdana','DejaVu Sans',sans-serif;
--f-disp: 'Chalkboard SE','Chalkboard','Marker Felt','Comic Sans MS','Trebuchet MS',cursive;
```

Now the JS. Let me write out major pieces concretely.

**Utility**: `rnd(a,b)`, `clamp`, `lerp`, hex mix:
```js
function hex2rgb(h){h=h.replace('#','');if(h.length===3)h=h.split('').map(c=>c+c).join('');const n=parseInt(h,16);return [n>>16&255,n>>8&255,n&255];}
function mix(a,b,t){const A=hex2rgb(a),B=hex2rgb(b);return `rgb(${A.map((v,i)=>Math.round(v+(B[i]-v)*t)).join(',')})`;}
```
For sky I need `rgba` too: `rgba()` helper with alpha.

**RoundRect**: use `ctx.roundRect` if available else fallback. Use `ctx.beginPath(); ctx.roundRect? ...`. I'll write helper `rr(ctx,x,y,w,h,r)` that handles radius per corner and manual arcs to be safe.

**Bird drawing** (r=17):
- shadow under? 
- body: circle radius 17 at (0,0), fill body color, darker outline 2.5.
- belly: lighter ellipse lower.
- wing: at (-2,0), rotate by `sin(wingPhase)*0.9`, draw teardrop shape (bezier) fill wingColor, outline.
- eye: white circle at (7,-6) r 5.5, pupil at (8.5,-6) r 2.6, sparkle dot; blink: draw lid as rect over eye.
- beak: two triangles/rounded diamond at (14,1) orange.
- blush: pink ellipse alpha .5 at (2,4).
- helmet/hat on top: at (-2,-16).
- star power: rainbow trail + halo.

Draw order with translate to bird pos, rotate by `angle` (from vy), scale for squash.

**Pipe drawing**: 
```js
function drawPipe(p){
  const bodyTop = {x:p.x, y:0, h:p.gapY}, ...
```
For each of the two segments: stem rect with gradient (two-tone candy), rim at gap edge: rounded rect 88x26 overhanging 6px each side, rim has a highlight and inner "shadow" line. Decorations: dots/stripes by variant; leaves/flowers at rim corners. Moving pipes get tiny "fins"/propellers.

Variant by biome: 
0: 'candy' pink stripes; 1:'bamboo' mint with nodes; 2:'coral' orange tube with holes; 3:'crystal' periwinkle translucent with facets; 4:'icicle' pale blue with drips; 5:'glow' teal with glowing moss. Cute!

**Cherry**: two red circles with highlight + green stem + leaf, bobbing, gentle spin, glow.

**Bubble shield**: bubble around bird (translucent cyan with rim highlight), drawn when `shield>0`.

**Star (pickup)**: 5-point star, yellow, rotating with sparkle.

**Particles** types: 'spark' (small circle, additive-ish), 'feather' (small rotated teardrop, falls + drifts), 'ring' (expanding circle stroke), 'puff' (white circle fading), 'heart', 'star2', 'text' (floating score text), 'traildot'. Generic particle with `type,life,maxLife,size,color,vel,rot,rotSpeed,gravity,alpha`.

Cap at 420; splice from front when exceeding.

**Text popups**: separate array for crisp rendering with outline.

**Stars (night)**: generate once: array of {x, y, r, tw} in sky region; parallax factor .05.
**Aurora**: 3 sine bands drifting, additive with low alpha.

Let me now write the code for real. Aim for correct, runnable, and readable.

```js
// ---- config
const CFG = {
  cozy:   {name:'Cozy',   gravity:1500, flap:-470, speed:170, maxSpeed:230, gap:250, minGap:200, spacing:250, r:12, moveScore:99, shield:0.35, drain:0},
  classic:{name:'Classic',gravity:1750, flap:-520, speed:205, maxSpeed:330, gap:212, minGap:168, spacing:232, r:11, moveScore:6,  shield:0.12, drain:0.05},
  spicy:  {name:'Spicy',  gravity:1950, flap:-560, speed:250, maxSpeed:400, gap:186, minGap:158, spacing:206, r:11, moveScore:3,  shield:0.08, drain:0.08},
};
```
gap = initial gap height; minGap = floor. `gapH = Math.max(minGap, gap - dist/900)` where dist in px.

**Game object:**
```js
const g = {
 state:'menu', dist:0, pipes:[], pickets...
}
```

I'll write it as a flat set of module-level vars for simplicity.

Reset function.

Main loop:
```js
let last = performance.now();
function frame(now){
  let dt = (now-last)/1000; last = now;
  dt = Math.min(dt, 1/24);   // clamp
  if(hitstop>0){ hitstop -= dt; dt *= 0.15; }
  update(dt); render(); requestAnimationFrame(frame);
}
```
Better: accumulate and substep physics for collision accuracy: split dt into steps of max 1/120.

Update order (play): 
1. scroll dist, camX
2. speed ramp
3. spawn pipes based on distance since last
4. update pipes (move left; bob), cull
5. bird physics (substep, check collisions each substep)
6. pickups collect
7. scoring on pass
8. particles
9. biome update
10. star power timer, invuln timer, shield timers
11. music tempo / events

Collision with substeps: bird position changes; pipes move left; both fine.

**Death sequence**: state 'dying': bird falls with gravity*1.1, spin, no input; after 1.1s → state 'over', show panel. Play crash sound + big shake + flash + feathers.

**Ready state**: pipes spawn at a fixed offset and camera doesn't scroll; pipe generation begins from spawnCursor at a negative position to ensure first pipe ~x=560 with 400px gap.

Menu state: bird patrols in a cute loop path, blinking, occasional "zzz"; pipes not spawned; decor scrolls slowly; call-to-action in DOM panel. On pointerdown/space in menu → startRun() and flap; state 'ready'; the panel hides. First flap → 'play' (start scrolling). Keyboard space works too (space in menu → start). Good, no extra buttons needed.

**Star power activation**: when meter >= SPEND → activate immediately on cherry pickup.

**Combo / multiplier**: chain of cherries within 3s → show "SWEET ×N". Points: cherry base 1, +1 during star power. Combo shows for flavor only.

**Close-call bonus**: when bird passes pipe's right edge with |offset| < 12 → +1 point and toast "Close one! 😊". Implement in scoring check.

**Hats list:**
```
HATS = [
 {id:'none', name:'No Hat', need:0},
 {id:'party', name:'Party Cone', need:5, color:pink+pom},
 {id:'daisy', name:'Daisy Crown', need:12},
 {id:'crown', name:'Tiny Crown', need:25},
 {id:'wizard', name:'Star Hat', need:45},
 {id:'astro', name:'Bubble Helmet', need:70},
]
SKINS = [
 {id:'peach', name:'Peaches', need:0, body:'#FFC978', wing:'#FF8F5E', belly:'#FFF0C9'},
 {id:'mint', name:'Minty', need:10, body:'#A8E6C7', wing:'#5FBF9B', belly:'#E9FFF3'},
 {id:'boba', name:'Blueberry', need:25, body:'#9BB8F5', wing:'#6B82D8', belly:'#E6ECFF'},
 {id:'mochi', name:'Sakura', need:45, body:'#FFC0D0', wing:'#F58AA6', belly:'#FFF0F4'},
 {id:'wisp', name:'Will-o-Bird', need:70, body:'#CFF6EA', wing:'#8FDCC8', belly:'#F2FFFB', glow:true},
]
```
Skins unlocked by best score; hats too. Persist best, cherries, unlocked derived from best/cherries, selected hat/skin, sound, mode. For skin chips I'll render canvas thumbnails! Cute: draw the bird + hat into a small canvas chip. Implement `drawBirdInto(ctx, r, skin, hat)` reusing the same function. Nice touch: chips contain a mini canvas (44x44) drawn with the bird; re-render on change.

**Key legend card**: kbd elements: Space/Click = Flap, P = Pause, R = Restart, M = Mute, 1-3 = Mode. Keys work.

**Tips card**: rotating tips every 6s with fade (DOM, small).

**Ticker**: bottom marquee with cute words separated by ✦.

**Sound toggles**: two toggle chips (Music / SFX) with a tiny bar-animating equalizer (CSS). 

**Stats**: SCORE, BEST, CHERRIES (session run), and a small "run" indicator. Chips show lock/need; selected = chocolate fill cream text.

`#ui` container: `position:absolute; inset:0; pointer-events:none;` children with pointer-events auto. Toasts container + panels + score. Keep it simple; score in canvas only.

Let me write the audio engine compactly:

```js
const A = {
 ctx:null, master:null, sfx:null, mus:null, noiseBuf:null, musicOn:true, sfxOn:true,
 init(){ if(this.ctx) return; const AC = window.AudioContext||window.webkitAudioContext; if(!AC) return; this.ctx=new AC(); ... }
}
```
Music sequencer:
```js
music = { step:0, next:0, tempo:112 }
tick(): while(next < t+0.15){ playStep(step, next); next += 60/tempo/2 /* eighth */; step++ }
```
Pattern: 32 steps. Bass on steps %4==0, melody pentatonic pattern, shaker on odd steps. Melody notes array; chords progression by bar (Math.floor(step/8)%4). Minor scale for night biomes. Use `wave(freq, time, dur, type, gain, opts)`. SFX use similar helpers. Ensure music only runs in play/ready/menu (calm). Stop when paused (set flag). Schedule with a 25ms interval timer, checking `state`.

Careful: AudioContext may start suspended → resume on gesture. Guard against many oscillators: cap melody polyphony; cap SFX rate; release after dur + 0.05.

Now write final code. Also localStorage guard with try/catch.

**Ghost/medals**: Medal via inline SVG in DOM: circle + star; classes `m-p|m-b|m-s|m-g|m-spark` controlling fill colors. Add `.earned` animation: scale-in + shine sweep.

**Game over panel content**:
```
<div class="panel-card">
  <div class="pc-head"><span class="pc-title">Sweet Dream!</span><div class="medal">SVG</div></div>
  <div class="pc-scores"><div>SCORE <b id="ovScore">0</b></div><div>BEST <b id="ovBest">0</b></div></div>
  <div class="pc-rows">cherries, pipes, distance, "Close calls"</div>
  <div class="pc-btns"><button id="btnAgain">Fly again (Space)</button><button id="btnMenu">Menu</button></div>
  <div class="pc-hint">...</div>
</div>
```
Count-up animation for score via JS.

**Pause panel**: "Paused — P to resume", plus restart/menu buttons.

Pause implementation: `state='paused'` storing `prevState='play'`; toggle back; skip updates; music pause.

Also `visibilitychange` → auto-pause.

**Ready state details**: state 'ready': camera scrolls, bird physics active, no pipes spawned yet; first flap → play.

Let me now write the update code:

```js
function update(dt){
  const C = cfg; // current config
  decor.update(dt);
  particles.update(dt);
  if(state==='play' || state==='ready' || state==='dying'){
    const dir = state==='dying' ? 0.25 : 1;
    camX += speed*dt*dir;
    dist += speed*dt*dir;
  }
  if(state==='play'){
    // speed ramp
    speed = Math.min(C.maxSpeed, 205 + dist/26);  // per-mode
    // spawn
    spawnCursor -= speed*dt;
    while(spawnCursor <= 0){ spawnPipe(spawnCursor); spawnCursor += C.spacing; }
    ...
  }
}
```
With state-based `spawnCursor += spacing` in ready and decrement by scroll, and pipes culling, it works.

Pipe update (play/dying): `p.x -= speed*dt*dir; p.phase += dt;` collision only in play.

Scoring: iterate pipes; if `!p.passed && p.x + p.w < bird.x - 4` → passed: score++, ping sound, +1 popup; if close call (|bird.y - pcy| < 20 at that moment) → bonus.

`pickets` array (pickups). Spawn in spawnPipe: pickets pushed with absolute x. Collect: distance check < 22 → apply.

Death: `die(reason)` → state='dying', dieT=0, big shake, sound, feather burst, music stop; in update 'dying': if `bird.y>groundY-6 || dieT>1.15` → gameOver().

gameOver(): state='over'; compute medal; count-up; unlock check → toast; update DOM panel; show panel.

**Bird physics** in play/ready/dying:
```js
if(state==='play'||state==='ready'){
  bird.vy += g*dt; if(bird.vy>720) bird.vy=720;
  bird.y += bird.vy*dt;
  if(bird.y < 26){ bird.y=26; bird.vy=Math.max(bird.vy,-60); } // soft ceiling
}
```
Ceiling clamp at y=30 fine.

**Bird rotation**: `targetAng = clamp(vy/700, -0.55, 1.15)*1.1` smoothed. Squash: on flap set `sq={x:1.25,y:0.8}` decaying to 1.

**Wing phase**: flap quickly after input (`bird.flapT` timer); during flap wingPhase advances fast, otherwise slow idle.

**Trail**: push {x,y,a} each frame in play; keep 16; draw as tapering fading circles; also sparkle particles during star power.

Collision with pipes (circle vs rect) plus pickup and death. Cozy uses r=13 (forgiving). Star power: destroy pipe → `p.dead=true`, +1, ring particle, shake, sound, small speed boost +20 capped.

**Bounce** on shield hit: teleport to gap center with a fun "pop" — `bird.y=cy; bird.vy=-320;` invuln 1.2s, shake, sound, particles. Shield hit doesn't end combo — reset combo timer only.

Difficulty gate: after 3 pipes passed in ready→play, no gate needed.

**Render order** and per-biome decor kinds:
- 'mountains' (dawn), 'hills' (day), 'cacti/coral' (sunset), 'islands' (dusk), 'pines+snow' (night), 'glowtrees/aurora' (aurora night).

```js
decor = { far:[], mid:[], near:[], clouds:[], birds:[], balloons:[], stars:[] }
```
Each layer item with x,w,type,seed,h. Generate as camera advances with deterministic types.

Layered rendering: stars (night), aurora bands, sun/moon, far layer (factor .18), clouds (factor .3 + own drift), mid layer (factor .45), birds/balloons, then pipes/pickets, then ground, then foreground particles (petals/snow). Petals drawn both behind and in front for depth.

Ground detail: grass band 22px with scalloped top edge, pebbles/flowers/mushrooms deterministic from Math.sin of index (no RNG state needed).

Biome blending: `BI = index + t` continuous (t = dist/BLENCH, BLENCH=2600px); color via `skyCol(off)` lerping from the segment at floor(BI+off) to next; alpha blending for fog/aurora. Stars alpha via `nightness = clamp((BI-3)/1.2,0,1)`.

**Star power visual**: rainbow trail + halo + slightly brighter world; pipes glow-pastel.

`starT` remaining time; `meter` 0..6.

Skins glow: draw radial glow behind bird.

Now the draw helpers: `birdShadow` ellipse on ground, bird, hats, pipes, cherry, star, bubble. Hat drawing inside `drawBird(ctx,x,y,r,skin,hat,ang,blink,wingPhase)`, drawn at rotation with slight y offset.

OK. Time to write the whole file. I'll organize JS with comments: CONFIG, UTIL, AUDIO, SPRITES, WORLD/BIO, DECOR, STATE, INPUT, UI, GAMEPLAY, RENDER, LOOP.

DOM refs needed:
```
game canvas, scrEl, uiEl, panelStart, panelOver, panelPause, toastsEl
ovScore, ovBest, ovMedal, ovStats, newBest, btnAgain, btnMenu, btnResume
hudScore, hudBest, hudCherries, meterFill, meterLabel
modeChips, hatChips, skinChips, soundChips, tipsEl
```

CSS classes: body, .bg/.sky/.cloud/.twk/.stripe/.horizon, .stage/.rail/.cab/.screen, .card/.card-h/.chip/.kbd/.toggle/.eq, .panel/.panel.show/.p-title/.p-sub/.tap-hint, .go-card/.medal/.statrow, .marquee.

Responsive: `.stage{grid-template-columns: 250px minmax(300px,620px) 240px; justify-content:center; align-items:start; gap:18px}`; `@media(max-width:1060px){ single column, rails horizontal wrap, hide some }`; below 560px rails into 2-col grid.

Medal SVG: circle medal with ribbon + star; classes control `--m1/--m2`.

`html,body{height:100%;overflow:hidden}` on desktop; on mobile allow scroll.

Input:
```js
addEventListener('keydown', e=>{
  if(['Space','ArrowUp','KeyW'].includes(e.code)){ e.preventDefault(); flap(); }
  else if(e.code==='KeyP'||e.code==='Escape') togglePause();
  else if(e.code==='KeyR'){ e.preventDefault(); reset(true); }
  else if(e.code==='KeyM') toggleAllSound();
  else if(e.code==='Digit1') setMode('cozy'); ...
});
```
pointerdown on the screen → flap (anywhere in the stage actually); `touch-action:manipulation`. Pointer-events: `.ui{pointer-events:none}`; panels re-enable.

flap() behavior by state: menu → start run (state='ready'), flap; ready → play + flap; play → flap; over → restart (no cooldown needed); paused → resume. Space keydown repeat guard.

Toast function:
```js
function toast(text, cls){ /* build div, prepend to #toasts, remove after 2.6s */ }
```
Keep at most 3. Tips rotate every 6s with fade. Stats updates only when changed. Unlock detection in gameOver → toast + chime.

Count-up: rAF-driven `countUp(el, to, dur)`.

Stars generated lazily when nightness > 0 (once).

Sprite drawing uses a passed ctx with `ctx.lineWidth = r*0.16` so it works at both small (thumbnails) and full scale. Bird drawn centered at (0,0), body radius r. Wing: ellipse centered at (-2,1), rx=r*0.78, ry=r*0.55, rotated by wingAng, with a darker inner line. Beak: diamond at right. Eye: white circle + pupil + sparkle; blink → lid. Hats drawn after at y≈-r*1.0.

Hats: party (cone + pom + stripes), daisy (3 circles + centers + leaves), crown (zigzag polygon + gems), wizard (tall cone with stars + brim + droop), astro (translucent bubble + rim + antenna). No external fonts → text-based toasts use the display stack.

Aurora:
```js
for(let b=0;b<3;b++){
  ctx.globalCompositeOperation='lighter';
  grad teal→pink vertical, alpha ~0.22 * auroraA;
  y = 60+b*38 + sin(x/120 + t*0.6 + b)*22
}
```

Sun: `function disc(x,y,r,core,glow)` with two radial gradients. Moon: crescent + craters.

Per-biome decor generator:
```js
function ensureDecor(untilX){
  while(decorFarX < untilX){ const seed=decorFarX/220; type=(seed|0)%types.length; push {x,type,h,seed}; decorFarX += 180+... }
}
```
Types: mountains → rolling mountain + snow caps; hills → mound + 2-3 blob trees; mesa → flat-topped rock + cactus; islands → floating island (ellipse top, jagged bottom, tree, vines, waterfall); pines → snowy mound + pines; glowtrees → dark trees with glowing dots + crystal. Mid layer: bushes, flowers-clumps, rocks, reeds. Clouds: 3 depths. Foreground grass tufts (factor 1.25, drawn after pipes). Balloon 600px interval in 25% cases. Night: fireflies + a small friendly firefly-bird. Ground scallop + pebbles + flowers.

Performance: clouds as one path per alpha group; decor shapes cheap.

`groundY = H-92`; pit top min 60, bottom max groundY-40; hole 200→158.

Render:
```js
function render(){
  const W=520,H=728;
  ctx.save();
  // shake translate
  drawSky(); drawStars(); drawAurora(); drawSunMoon();
  drawDecorLayer(decor.far,0.18, 0.55);
  drawClouds();
  drawDecorLayer(decor.mid,0.42, 0.8);
  drawSkyCritters();
  drawPipes(); drawPickets();
  drawGround();
  drawParticles();
  drawBird();
  drawForegroundParticles();
  drawTexts();
  drawScoreHUD();
  drawVignette();
  ctx.restore();
  drawFlash();
}
```
Shake applies to world only; flash/vignette outside. Screen-space particles in two loops (behind/in front of bird).

Camera: `const camX = world.scroll;`, `sx = x - camX*parallax`, `screenY = worldY + camY`. `ensureDecor(camX+800)` each frame + initial fill.

Menu: bird patrol
```
if(state==='menu'){ bird.x=150+Math.cos(t*0.7)*36; bird.y=380+Math.sin(t*1.3)*46; bird.ang=Math.cos(t*1.3)*0.18; }
```
Slight vertical drift with the scroll so it never looks static.

Canvas font: `const FD = '"Chalkboard SE","Chalkboard","Marker Felt","Comic Sans MS","Trebuchet MS",sans-serif'` used as `${size}px ${FD}`.

Helpers:
```js
function rr(x,y,w,h,r){ /* per-corner manual arcs */ }
function circle(x,y,r){ctx.beginPath();ctx.arc(x,y,r,0,7);ctx.closePath();}
function starPath(x,y,pts,ro,ri,rot){...}
```
`ctx.setTransform(dpr,0,0,dpr,0,0)` then draw in logical coords (W=520,H=728). fillText + heavy stroke `lineJoin='round'` for chunky arcade text.

Game speed vars: `let speed=CFG.speed;`, `speed = Math.min(C.maxSpeed, C.speed + dist/26)`; reset on restart; gentle slow-mo recovery after respawn (0.6s at 0.4×).

Substep collision in `updatePlay(dt)`:
```js
const steps = 3, h = dt/steps;
for(let i=0;i<steps;i++){
  bird.vy += C.gravity*h; ...
  bird.y += bird.vy*h; bird.x += C.drift*h;
  // pipes move
  for(const p of g.pipes){ p.x -= speed*h; ... }
  // collisions
}
```
Pipes move inside substep with total displacement = speed*dt.

Hit detection:
```js
function hitPipes(){
  const r=C.r;
  if(bird.y - r < 0) {bird.y = r; bird.vy = Math.max(bird.vy, -80);}
  if(bird.y + r >= groundY){ return 'ground'; }
  for(const p of g.pipes){
    if(p.dead) continue;
    if(bird.x+r < p.x || bird.x-r > p.x+p.w) continue;
    const cy = p.cyNow + Math.sin((t + p.phase)*p.bobSpd)*p.bobA;
    const hole = p.hole/2;
    if(Math.abs(bird.y - cy) > hole - r) continue;
    if(bird.x > p.x && bird.x < p.x+p.w) return {p, top:(bird.y < cy-hole+r), bottom:(bird.y>cy+hole-r)};
  }
  return null;
}
```
Use point-in-rect with radius inset.

**Unlock**: `unlocksNow = [...HATS, ...SKINS].filter(...)`; skins/hats clickable when unlocked; selecting updates bird rendering, re-renders chip thumbs, plays click, updates DOM selected classes; mode chips update CFG; sound toggles update AUDIO flags + music gate.

localStorage key 'flappy.cute.v1' storing {best, cherries, hat, skin, mode, music, sfx}.

ResizeObserver + resize/timeout to recompute fit. `visibilitychange` → auto-pause.

Marquee/ticker content: "flap · glide · sip nectar · dodge pipes · be soft · you are a little bird with a big heart ✦".

Now write it all out as one HTML file, being careful about bugs (canvas roundRect fallback, audio context resume, particle caps, event stopPropagation on panels/buttons). Reply prose in English (request is English). Write the full file now, for real — final answer.


## Content

# 🐦 Flappy Bird — "Little Sky"

A complete, single-file, hand-drawn Flappy Bird. Every pixel — bird, pipes, clouds, mountains, particles, medals — is generated in code. Even the soundtrack and SFX are synthesized in WebAudio. Save as `.html` and open.

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no" />
<title>Little Sky · a flappy bird game</title>
<style>
/* ============================================================
   LITTLE SKY — cute arcade cabinet, all local, no external assets
   ============================================================ */
:root{
  --ink:#3b2b2f;
  --ink-soft:#5c454b;
  --cream:#fff6e6;
  --paper:#fffaf1;
  --butter:#ffe6a7;
  --lemon:#ffd25e;
  --bubble:#ff8fab;
  --bubble-deep:#f2477a;
  --mint:#8fe3c2;
  --mint-deep:#2fa98c;
  --sky:#8fd3f4;
  --sky-deep:#3aa8d6;
  --tangerine:#ff9f5a;
  --shadow:rgba(59,43,47,.22);
  --f-disp:'Chalkboard SE','Chalkboard','Marker Felt','Comic Sans MS','Trebuchet MS',sans-serif;
  --f-body:'Trebuchet MS','Verdana','DejaVu Sans',sans-serif;
}
*{box-sizing:border-box}
html,body{height:100%}
body{
  margin:0; font-family:var(--f-body); color:var(--ink);
  background:linear-gradient(#ffd9b6,#ffd2d8 26%,#cfe9ea 58%,#bfe3d2 78%,#ffd9a8);
  overflow:hidden;
  -webkit-font-smoothing:antialiased;
}
/* ---------- ambient world behind the cabinet ---------- */
.bg{position:fixed; inset:0; z-index:0; overflow:hidden; pointer-events:none}
.stripe{position:absolute; inset:-20% -10% auto -10%; height:160%;
  background:repeating-linear-gradient(104deg, rgba(255,255,255,.13) 0 3px, transparent 3px 68px);
  opacity:.7; animation:slide 40s linear infinite}
@keyframes slide{to{transform:translateX(-68px)}}
.cloud{position:absolute; border-radius:50%; background:rgba(255,255,255,.55);
  filter:blur(18px); animation:drift linear infinite}
@keyframes drift{
  0%{transform:translateX(-30vw) scale(1)}
  100%{transform:translateX(130vw) scale(1.12)}
}
.twk{position:absolute; width:8px;height:8px; border-radius:50%;
  background:radial-gradient(circle,#fff,rgba(255,255,255,0) 70%);
  animation:twk ease-in-out infinite alternate}
@keyframes twk{from{transform:scale(.5) rotate(0deg);opacity:.25}to{transform:scale(1.5) rotate(90deg);opacity:.95}}
.horizon{position:absolute; left:0; right:0; bottom:0; height:26vh;
  background:linear-gradient(to top, rgba(47,169,140,.35), rgba(143,227,194,0));
  filter:blur(2px)}
.groundGlow{position:absolute; left:50%; bottom:-14vh; width:120vw; height:34vh; transform:translateX(-50%);
  background:radial-gradient(50% 60% at 50% 0%, rgba(255,230,167,.75), rgba(255,230,167,0));}

/* ---------- stage / layout ---------- */
.stage{
  position:relative; z-index:2; height:100%;
  display:grid; grid-template-columns:262px minmax(280px,640px) 246px;
  gap:16px; justify-content:center; align-content:start; align-items:start;
  padding:16px 18px 8px; max-width:1340px; margin:0 auto;
}
.rail{display:flex; flex-direction:column; gap:12px}
.rail-row{display:flex; flex-wrap:wrap; gap:8px}

/* ---------- sticker cards ---------- */
.card{
  position:relative; background:var(--paper);
  border:2.5px solid var(--ink); border-radius:16px;
  box-shadow:4px 5px 0 var(--shadow), inset 0 -10px 0 rgba(255,214,167,.35);
  padding:11px 13px; transform:rotate(-.4deg);
  transition:transform .22s cubic-bezier(.2,.9,.2,1), box-shadow .22s, background .3s;
  opacity:0; translate:0 14px;
}
.card:nth-child(even){transform:rotate(.5deg)}
.card.in{opacity:1; translate:0 0}
.card:hover{transform:rotate(0deg) translateY(-3px); box-shadow:6px 9px 0 var(--shadow)}
.card-h{
  font-family:var(--f-disp); font-size:11px; letter-spacing:.18em; text-transform:uppercase;
  color:var(--ink-soft); display:flex; align-items:center; gap:7px; margin-bottom:8px;
}
.card-h::after{content:''; flex:1; height:2px; background:repeating-linear-gradient(90deg,var(--ink) 0 4px,transparent 4px 8px); opacity:.35}

/* ---------- logo ---------- */
.brand{padding:14px 14px 12px; text-align:left; overflow:hidden}
.logo{font-family:var(--f-disp); font-weight:700; font-size:clamp(28px,3.1vw,44px); line-height:.94;
  letter-spacing:.01em; margin:0; text-transform:uppercase}
.logo span{display:inline-block; color:var(--c);
  text-shadow:-2px 0 0 var(--ink),2px 0 0 var(--ink),0 -2px 0 var(--ink),0 2px 0 var(--ink),
              -2px -2px 0 var(--ink),2px -2px 0 var(--ink),-2px 2px 0 var(--ink),2px 2px 0 var(--ink),
              0 6px 0 rgba(59,43,47,.28);
  animation:bob 3.2s ease-in-out infinite; animation-delay:calc(var(--i)*.075s)}
@keyframes bob{0%,100%{transform:translateY(0) rotate(var(--r,0deg))}45%{transform:translateY(-6px) rotate(calc(var(--r,0deg) * -1))}}
.logo:hover span{animation-duration:.9s}
.tag{margin:9px 0 0; font-size:11.5px; letter-spacing:.22em; text-transform:uppercase; color:var(--ink-soft)}
.badge{position:absolute; top:10px; right:10px; font-family:var(--f-disp); font-size:9.5px; letter-spacing:.1em;
  background:var(--bubble); color:#fff; padding:4px 8px; border-radius:99px; border:2px solid var(--ink);
  box-shadow:2px 2px 0 var(--shadow); transform:rotate(7deg)}

/* ---------- HUD ---------- */
.hud .label{font-family:var(--f-disp); font-size:10px; letter-spacing:.24em; color:var(--ink-soft)}
#hudScore{font-family:var(--f-disp); font-size:52px; font-weight:700; line-height:.92; letter-spacing:.02em;
  display:block; transition:transform .12s}
#hudScore.pop{animation:scorePop .28s cubic-bezier(.3,1.6,.4,1)}
@keyframes scorePop{0%{transform:scale(1)}40%{transform:scale(1.22) rotate(-2deg)}100%{transform:scale(1)}}
.hud-row{display:flex; align-items:center; justify-content:space-between; gap:8px;
  padding-top:7px; margin-top:7px; border-top:2px dashed rgba(59,43,47,.2)}
.stat{display:flex; align-items:center; gap:6px; font-size:12px; color:var(--ink-soft)}
.stat b{font-family:var(--f-disp); font-size:16px; color:var(--ink)}
.stat svg{width:18px; height:18px; flex:none}

/* ---------- meter ---------- */
.track{height:16px; border:2.5px solid var(--ink); border-radius:99px; background:#ffeecf; overflow:hidden; position:relative}
.fill{height:100%; width:0%; border-radius:99px;
  background:linear-gradient(90deg,var(--mint),var(--lemon) 46%,var(--bubble));
  background-size:220% 100%; animation:candy 2.4s linear infinite; transition:width .25s}
@keyframes candy{to{background-position:220% 0}}
.track.full .fill{animation:candy .7s linear infinite, pulseGlow .7s ease-in-out infinite alternate}
@keyframes pulseGlow{from{filter:brightness(1)}to{filter:brightness(1.5)}}
.meter-note{display:flex; justify-content:space-between; font-size:10.5px; letter-spacing:.12em; margin-top:6px; color:var(--ink-soft)}
.meter-note b{font-family:var(--f-disp); letter-spacing:.06em; color:var(--bubble-deep)}

/* ---------- chips ---------- */
.chip{
  font:700 12px/1.1 var(--f-body); color:var(--ink); background:var(--cream);
  border:2.5px solid var(--ink); border-radius:99px; padding:8px 11px; cursor:pointer;
  display:inline-flex; align-items:center; gap:7px; box-shadow:2px 3px 0 var(--shadow);
  transition:transform .16s, box-shadow .16s, background .2s, color .2s;
}
.chip:hover{transform:translateY(-2px) rotate(-1.2deg); box-shadow:3px 5px 0 var(--shadow); background:#fff}
.chip:active{transform:translateY(1px)}
.chip[aria-pressed="true"]{background:var(--ink); color:var(--cream); box-shadow:0 0 0 3px rgba(255,210,94,.75) inset}
.chip.locked{opacity:.45; cursor:not-allowed; filter:grayscale(.5)}
.chip.locked:hover{transform:none}
.chip .need{font-size:9.5px; opacity:.75; letter-spacing:.06em}
.chip canvas{display:block; width:30px; height:30px}
.chip .txt{display:flex; flex-direction:column; gap:2px; text-align:left}
.chip .txt small{font-weight:400; opacity:.7}
.chips{display:flex; flex-wrap:wrap; gap:7px}

/* toggles + equalizer */
.toggle{display:flex; align-items:center; gap:9px; width:100%; padding:8px 9px}
.eq{display:flex; align-items:flex-end; gap:2px; height:17px; width:20px; flex:none}
.eq i{flex:1; background:var(--mint-deep); border-radius:2px; height:30%}
.toggle[aria-pressed="false"] .eq i{height:22%; background:#c3b3ad}
.toggle[aria-pressed="true"] .eq i{animation:eqa .6s ease-in-out infinite alternate}
.eq i:nth-child(2){animation-delay:.14s!important}
.eq i:nth-child(3){animation-delay:.3s!important}
.eq i:nth-child(4){animation-delay:.45s!important}
@keyframes eqa{from{height:20%}to{height:100%}}

/* ---------- cabinet ---------- */
.cab{display:flex; flex-direction:column; gap:0; min-width:0;
  max-width:min(100%, calc((100dvh - 120px) * .714)); margin-inline:auto; width:100%}
.marquee{position:relative; background:linear-gradient(#3f2d33,#2b1e22); border:2.5px solid var(--ink);
  border-bottom:none; border-radius:18px 18px 6px 6px; padding:8px 12px; text-align:center; overflow:hidden;
  box-shadow:4px 5px 0 var(--shadow)}
.marquee::before{content:''; position:absolute; inset:0;
  background:repeating-linear-gradient(90deg, rgba(255,255,255,.09) 0 6px, transparent 6px 26px)}
.marquee h2{position:relative; margin:0; font-family:var(--f-disp); font-size:clamp(13px,1.5vw,19px);
  letter-spacing:.3em; text-transform:uppercase; color:#ffe9b8;
  text-shadow:0 0 10px rgba(255,190,90,.9), 0 0 26px rgba(255,120,150,.6); animation:flick 6s infinite}
@keyframes flick{0%,92%,100%{opacity:1}93%{opacity:.55}95%{opacity:1}96%{opacity:.7}}
.lamps{display:flex; justify-content:center; gap:6px; margin-top:6px}
.lamps i{width:7px; height:7px; border-radius:50%; background:var(--bubble); box-shadow:0 0 7px var(--bubble);
  animation:lamp 1.4s ease-in-out infinite alternate}
.lamps i:nth-child(2n){background:var(--lemon); box-shadow:0 0 7px var(--lemon); animation-delay:.35s}
.lamps i:nth-child(3n){background:var(--mint); box-shadow:0 0 7px var(--mint); animation-delay:.7s}
@keyframes lamp{from{opacity:.25}to{opacity:1}}
.screen{position:relative; flex:1 1 auto; min-height:0; aspect-ratio:5/7; margin:0 auto;
  border:2.5px solid var(--ink); border-top:none; border-bottom:none; background:#bfe6e0;
  box-shadow:inset 0 0 0 5px #efe2c9, inset 0 0 34px rgba(59,43,47,.35), 4px 5px 0 var(--shadow);
  overflow:hidden; cursor:pointer}
canvas#game{display:block; width:100%; height:100%; touch-action:manipulation}
.crt{position:absolute; inset:0; pointer-events:none; mix-blend-mode:multiply; opacity:.16;
  background:repeating-linear-gradient(0deg, rgba(59,43,47,.5) 0 1px, transparent 1px 3px)}
.sheen{position:absolute; inset:0; pointer-events:none;
  background:linear-gradient(118deg, rgba(255,255,255,.3) 0 7%, transparent 24%),
             radial-gradient(120% 90% at 50% 120%, rgba(59,43,47,.3), transparent 60%)}
.base{background:linear-gradient(#4a343a,#332428); border:2.5px solid var(--ink); border-top:none;
  border-radius:6px 6px 18px 18px; padding:9px 12px; display:flex; align-items:center; gap:12px;
  box-shadow:4px -1px 0 var(--shadow)}
.joy{width:26px; height:26px; border-radius:50%; background:radial-gradient(circle at 34% 30%,#ff9db4,#d33a68);
  border:2.5px solid var(--ink); box-shadow:0 3px 0 rgba(59,43,47,.5); transition:transform .12s}
.screen:active .joy{transform:translateY(3px) scale(.94)}
.vent{flex:1; height:12px; border-radius:4px;
  background:repeating-linear-gradient(90deg, rgba(255,255,255,.16) 0 3px, transparent 3px 8px)}
.slot{width:34px; height:11px; border-radius:3px; background:#1c1215; box-shadow:inset 0 2px 3px #000}
.coin{font-size:9px; letter-spacing:.14em; color:#ffe9b8; opacity:.8}

/* ---------- in-screen overlays ---------- */
.ui{position:absolute; inset:0; pointer-events:none; display:flex; align-items:center; justify-content:center; padding:14px}
.panel{display:none; pointer-events:auto; width:100%; max-width:330px; text-align:center;
  background:rgba(255,250,241,.97); border:3px solid var(--ink); border-radius:20px;
  padding:16px 16px 14px; box-shadow:0 12px 30px rgba(30,20,22,.4), 6px 8px 0 var(--shadow);
  animation:pop .34s cubic-bezier(.2,1.5,.4,1)}
.panel.show{display:block}
@keyframes pop{from{transform:scale(.86) rotate(-2deg); opacity:0}to{transform:none; opacity:1}}
.panel .kicker{font-family:var(--f-disp); font-size:10.5px; letter-spacing:.24em; color:var(--bubble-deep)}
.panel h3{font-family:var(--f-disp); font-size:27px; margin:4px 0 2px; letter-spacing:.02em}
.panel p{font-size:12.5px; line-height:1.5; color:var(--ink-soft); margin:6px 0 0}
.keyline{display:flex; align-items:center; justify-content:center; gap:8px; margin-top:11px; flex-wrap:wrap}
kbd{font-family:var(--f-disp); font-size:11px; background:var(--cream); border:2px solid var(--ink);
  border-radius:8px; padding:4px 7px; box-shadow:0 2px 0 var(--ink)}
.hintTap{margin-top:12px; font-family:var(--f-disp); font-size:14px; color:var(--ink);
  animation:tapPulse 1.25s ease-in-out infinite}
@keyframes tapPulse{0%,100%{transform:scale(1); opacity:.7}50%{transform:scale(1.09); opacity:1}}
.go-top{display:flex; align-items:center; gap:12px; text-align:left}
.medal{width:74px; height:74px; flex:none; border-radius:50%; border:3px solid var(--ink); position:relative;
  background:radial-gradient(circle at 32% 28%, #fff5d8, var(--m1,#e8d7b6) 58%, var(--m2,#b9a585));
  box-shadow:inset 0 -6px 0 rgba(59,43,47,.16), 3px 4px 0 var(--shadow)}
.medal::after{content:''; position:absolute; inset:13px; border-radius:50%;
  background:radial-gradient(circle at 40% 34%, #fff, rgba(255,255,255,0) 70%); opacity:.7}
.medal .star{position:absolute; inset:0; display:grid; place-items:center; font-size:26px; text-shadow:0 2px 0 rgba(59,43,47,.3)}
.medal.earned{animation:medalIn .6s cubic-bezier(.2,1.6,.3,1)}
@keyframes medalIn{0%{transform:scale(.3) rotate(-40deg)}70%{transform:scale(1.12) rotate(6deg)}100%{transform:none}}
.go-scores{display:flex; gap:8px; margin-top:12px}
.go-scores div{flex:1; border:2.5px solid var(--ink); border-radius:12px; padding:7px 9px; background:var(--cream)}
.go-scores span{display:block; font-family:var(--f-disp); font-size:9.5px; letter-spacing:.2em; color:var(--ink-soft)}
.go-scores b{font-family:var(--f-disp); font-size:26px}
.newbest{display:none; margin-top:8px; font-family:var(--f-disp); font-size:11px; letter-spacing:.14em;
  color:#fff; background:var(--bubble-deep); border:2px solid var(--ink); border-radius:99px; padding:4px 10px}
.newbest.show{display:inline-block; animation:shimmer 1.1s ease-in-out infinite alternate}
@keyframes shimmer{from{transform:rotate(-1.5deg) scale(1)}to{transform:rotate(1.5deg) scale(1.05)}}
.btnRow{display:flex; gap:8px; margin-top:13px}
.btn{flex:1; font-family:var(--f-disp); font-size:14px; padding:11px 8px; cursor:pointer;
  background:var(--bubble); color:#fff; border:2.5px solid var(--ink); border-radius:13px;
  box-shadow:0 4px 0 var(--ink); transition:transform .1s, box-shadow .1s, background .2s}
.btn.alt{background:var(--mint)}
.btn:hover{background:#fff; color:var(--ink)}
.btn:active{transform:translateY(4px); box-shadow:0 0 0 var(--ink)}
.lockRow{margin-top:9px; font-size:11px; color:var(--ink-soft)}

/* ---------- toasts + tips ---------- */
.toasts{position:absolute; left:0; right:0; top:14%; display:flex; flex-direction:column; align-items:center; gap:6px; pointer-events:none}
.toast{font-family:var(--f-disp); font-size:13px; letter-spacing:.04em; background:rgba(255,250,241,.94);
  color:var(--ink); border:2px solid var(--ink); border-radius:99px; padding:5px 12px;
  box-shadow:0 3px 0 rgba(59,43,47,.3); animation:toastIn .32s cubic-bezier(.2,1.6,.3,1)}
.toast.hot{background:var(--bubble); color:#fff}
.toast.cool{background:var(--mint); color:#084b3d}
@keyframes toastIn{from{opacity:0; transform:translateY(-9px) scale(.8)}to{opacity:1; transform:none}}
.toast.out{animation:toastOut .5s forwards}
@keyframes toastOut{to{opacity:0; transform:translateY(-16px) scale(.9)}}
.tips{font-size:11.5px; line-height:1.5; min-height:52px; color:var(--ink-soft); transition:opacity .45s}
.tips em{color:var(--bubble-deep); font-style:normal; font-weight:700}
.tips.fade{opacity:0}

/* ---------- ticker ---------- */
.ticker{position:fixed; left:0; right:0; bottom:0; z-index:3; overflow:hidden; padding:7px 0;
  background:var(--ink); border-top:2.5px solid #241a1d}
.ticker .run{display:flex; gap:26px; white-space:nowrap; width:max-content; animation:tick 34s linear infinite;
  font-family:var(--f-disp); font-size:12px; letter-spacing:.18em; color:#ffe9b8; text-transform:uppercase}
.ticker .run span{opacity:.55}
@keyframes tick{to{transform:translateX(-50%)}}

/* ---------- responsive ---------- */
@media (max-width:1080px){
  .stage{grid-template-columns:1fr; padding:10px 10px 42px}
  .cab{max-width:min(94vw, calc((100dvh - 220px) * .714))}
  .rail{flex-direction:row; flex-wrap:wrap; justify-content:center; gap:9px}
  .rail .card{flex:1 1 220px}
  .brand{flex:1 1 100%}
  .tips{min-height:0}
}
@media (max-width:600px){
  .rail .card{flex:1 1 45%}
  .card:hover{transform:none}
  .cab{max-width:96vw}
}
@media (prefers-reduced-motion:reduce){*{animation-duration:.01ms!important; animation-iteration-count:1!important}}
</style>
</head>
<body>

<div class="bg" aria-hidden="true">
  <div class="stripe"></div>
  <div class="cloud" style="top:6%;left:-20%;width:230px;height:96px;animation-duration:64s"></div>
  <div class="cloud" style="top:24%;left:-30%;width:160px;height:66px;animation-duration:48s;animation-delay:-12s;opacity:.75"></div>
  <div class="cloud" style="top:52%;left:-24%;width:290px;height:110px;animation-duration:78s;animation-delay:-30s;opacity:.5"></div>
  <div class="cloud" style="top:74%;left:-30%;width:200px;height:74px;animation-duration:56s;animation-delay:-22s;opacity:.42"></div>
  <div id="twks"></div>
  <div class="horizon"></div>
  <div class="groundGlow"></div>
</div>

<main class="stage">

  <!-- ============ LEFT RAIL ============ -->
  <aside class="rail">
    <div class="card brand">
      <div class="badge">no assets ✧</div>
      <h1 class="logo" id="logo" aria-label="Little Sky"></h1>
      <p class="tag">a tiny bird, a big sky</p>
    </div>

    <div class="card hud">
      <div class="card-h">Score</div>
      <span id="hudScore">0</span>
      <div class="hud-row">
        <div class="stat" title="Your best run">
          <svg viewBox="0 0 24 24" fill="none"><path d="M12 3l2.6 5.5 6 .8-4.4 4.2 1.1 6-5.3-3-5.3 3 1.1-6L3.4 9.3l6-.8L12 3z" fill="#ffd25e" stroke="#3b2b2f" stroke-width="1.7" stroke-linejoin="round"/></svg>
          <span>best</span><b id="hudBest">0</b>
        </div>
        <div class="stat" title="Cherries collected all time">
          <svg viewBox="0 0 24 24" fill="none"><path d="M8 20c1-6 3-9 8-11" stroke="#2fa98c" stroke-width="1.8" stroke-linecap="round"/><circle cx="7" cy="17" r="3.6" fill="#f2477a" stroke="#3b2b2f" stroke-width="1.6"/><circle cx="15.5" cy="19" r="3" fill="#ff8fab" stroke="#3b2b2f" stroke-width="1.6"/></svg>
          <span>cherries</span><b id="hudCherries">0</b>
        </div>
      </div>
    </div>

    <div class="card">
      <div class="card-h">Sparkle meter</div>
      <div class="track" id="meterTrack"><div class="fill" id="meterFill"></div></div>
      <div class="meter-note"><span>sweet ×6 = star power</span><b id="meterLabel">0/6</b></div>
    </div>

    <div class="card">
      <div class="card-h">Flight mode</div>
      <div class="chips" id="modeChips"></div>
    </div>

    <div class="card tips-card">
      <div class="card-h">Little tip</div>
      <div class="tips" id="tips"></div>
    </div>
  </aside>

  <!-- ============ CABINET ============ -->
  <section class="cab card" style="padding:0; transform:none">
    <div class="marquee">
      <h2>Little Sky · flap arcade</h2>
      <div class="lamps" id="lamps"></div>
    </div>
    <div class="screen" id="screen">
      <canvas id="game" width="520" height="728"></canvas>
      <div class="crt"></div><div class="sheen"></div>
      <div class="toasts" id="toasts"></div>
      <div class="ui">
        <!-- start -->
        <div class="panel show" id="panelStart">
          <div class="kicker">chapter one</div>
          <h3>Ready to flap?</h3>
          <p>Tap the sky to flap. Glide through the candy pipes, munch cherries to fill your
             <em>sparkle meter</em>, and let star power smash the rest.</p>
          <div class="hintTap">tap · space · click to fly</div>
          <div class="keyline">
            <kbd>Space</kbd><kbd>Click</kbd><span style="font-size:11px;color:var(--ink-soft)">flap</span>
            <kbd>P</kbd><span style="font-size:11px;color:var(--ink-soft)">pause</span>
            <kbd>R</kbd><span style="font-size:11px;color:var(--ink-soft)">restart</span>
          </div>
        </div>
        <!-- game over -->
        <div class="panel" id="panelOver">
          <div class="kicker" id="ovKicker">another try?</div>
          <div class="go-top" style="margin-top:8px">
            <div class="medal" id="ovMedal"><span class="star" id="ovStar">★</span></div>
            <div>
              <h3 style="font-size:21px" id="ovTitle">Sweet dream!</h3>
              <p style="margin:2px 0 0" id="ovSub">You flew 0 m and ate nothing.</p>
            </div>
          </div>
          <div class="go-scores">
            <div><span>score</span><b id="ovScore">0</b></div>
            <div><span>best</span><b id="ovBest">0</b></div>
            <div><span>cherries</span><b id="ovCh">0</b></div>
          </div>
          <div class="newbest" id="newBest">★ new best! ★</div>
          <div class="btnRow">
            <button class="btn" id="btnAgain">Fly again</button>
            <button class="btn alt" id="btnMenu">Cabinet</button>
          </div>
          <div class="lockRow" id="lockRow"></div>
        </div>
        <!-- pause -->
        <div class="panel" id="panelPause">
          <div class="kicker">take a breath</div>
          <h3>Parked on a cloud</h3>
          <p>The sky will wait for you. It's very patient.</p>
          <div class="btnRow">
            <button class="btn" id="btnResume">Resume (P)</button>
            <button class="btn alt" id="btnQuit">Restart</button>
          </div>
        </div>
      </div>
    </div>
    <div class="base">
      <div class="joy"></div>
      <div class="vent"></div>
      <div class="slot"></div>
      <div class="coin">insert courage</div>
    </div>
  </section>

  <!-- ============ RIGHT RAIL ============ -->
  <aside class="rail">
    <div class="card">
      <div class="card-h">Hat rack</div>
      <div class="chips" id="hatChips"></div>
    </div>
    <div class="card">
      <div class="card-h">Bird flock</div>
      <div class="chips" id="skinChips"></div>
    </div>
    <div class="card">
      <div class="card-h">Sound box</div>
      <div class="chips" style="flex-direction:column; gap:6px">
        <button class="chip toggle" id="chipMusic" aria-pressed="true">
          <span class="eq"><i></i><i></i><i></i><i></i></span><span class="txt">Tiny orchestra</span>
        </button>
        <button class="chip toggle" id="chipSfx" aria-pressed="true">
          <span class="eq"><i></i><i></i><i></i><i></i></span><span class="txt">Pops &amp; boops</span>
        </button>
      </div>
    </div>
    <div class="card">
      <div class="card-h">Pocket controls</div>
      <div class="keyline" style="justify-content:flex-start; gap:6px">
        <kbd>1</kbd><kbd>2</kbd><kbd>3</kbd><span style="font-size:11px">modes</span>
        <kbd>M</kbd><span style="font-size:11px">mute</span>
        <kbd>H</kbd><span style="font-size:11px">hat</span>
      </div>
      <p style="font-size:11px; margin-top:8px; color:var(--ink-soft)">
        Everything here is drawn by code: 6 skies, 6 pipes, 5 hats, 5 birds, 0 downloads.</p>
    </div>
  </aside>
</main>

<div class="ticker" aria-hidden="true">
  <div class="run" id="tickerRun"></div>
</div>

<script>
"use strict";
/* ============================================================================
   0 · SETUP
   ========================================================================== */
const W = 520, H = 728, GROUND_H = 92, GROUND_Y = H - GROUND_H;
const cvs = document.getElementById('game');
const ctx = cvs.getContext('2d');
const screenEl = document.getElementById('screen');
let dpr = 1, scl = 1;

function fitCanvas(){
  const r = screenEl.getBoundingClientRect();
  if (r.width < 10) return;
  dpr = Math.min(2, window.devicePixelRatio || 1);
  cvs.width = Math.round(r.width * dpr);
  cvs.height = Math.round(r.height * dpr);
  scl = r.width / W;
  ctx.setTransform(dpr * scl, 0, 0, dpr * scl, 0, 0);
  ctx.lineJoin = 'round';
}
const clamp=(v,a,b)=>v<a?a:v>b?b:v;
const lerp=(a,b,t)=>a+(b-a)*t;
const rnd=(a,b)=>a+Math.random()*(b-a);
const ri=(a,b)=>Math.floor(rnd(a,b+1));
const pick=a=>a[Math.floor(Math.random()*a.length)];
function hash(n){ const s=Math.sin(n*127.1)*43758.5453; return s-Math.floor(s); }
function hx(h){h=h.replace('#','');if(h.length===3)h=h.split('').map(c=>c+c).join('');const n=parseInt(h,16);return [n>>16&255,n>>8&255,n&255];}
function mixc(a,b,t){const A=hx(a),B=hx(b);return 'rgb('+Math.round(lerp(A[0],B[0],t))+','+Math.round(lerp(A[1],B[1],t))+','+Math.round(lerp(A[2],B[2],t))+')';}
function cir(x,y,r){ctx.beginPath();ctx.arc(x,y,r,0,Math.PI*2);ctx.closePath();}
function ell(x,y,rx,ry){ctx.beginPath();ctx.ellipse(x,y,rx,ry,0,0,Math.PI*2);ctx.closePath();}
function rr(x,y,w,h,r){
  r=Math.min(r,Math.abs(w)/2,Math.abs(h)/2);
  ctx.beginPath();
  ctx.moveTo(x+r,y); ctx.lineTo(x+w-r,y); ctx.quadraticCurveTo(x+w,y,x+w,y+r);
  ctx.lineTo(x+w,y+h-r); ctx.quadraticCurveTo(x+w,y+h,x+w-r,y+h);
  ctx.lineTo(x+r,y+h); ctx.quadraticCurveTo(x,y+h,x,y+h-r);
  ctx.lineTo(x,y+r); ctx.quadraticCurveTo(x,y,x+r,y); ctx.closePath();
}
function star(x,y,pts,ro,ri_,rot){
  ctx.beginPath();
  for(let i=0;i<pts*2;i++){
    const a=rot+i*Math.PI/pts, r=i%2?ri_:ro;
    const px=x+Math.cos(a)*r, py=y+Math.sin(a)*r;
    i?ctx.lineTo(px,py):ctx.moveTo(px,py);
  }
  ctx.closePath();
}
const FD='"Chalkboard SE","Chalkboard","Marker Felt","Comic Sans MS","Trebuchet MS",sans-serif';
function inkText(s,x,y,size,fill,align='center',outline=Math.max(3,size*.24)){
  ctx.font='700 '+size+'px '+FD; ctx.textAlign=align; ctx.textBaseline='middle';
  ctx.lineWidth=outline; ctx.strokeStyle='rgba(59,43,47,.92)'; ctx.lineJoin='round';
  ctx.strokeText(s,x,y+1.5); ctx.fillStyle=fill; ctx.fillText(s,x,y);
}

/* ============================================================================
   1 · SOUND — a tiny procedural orchestra
   ========================================================================== */
const SND = {
  ctx:null, master:null, sfxG:null, musG:null, ok:false, sfx:true, music:true,
  init(){
    if(this.ctx) return;
    const AC = window.AudioContext||window.webkitAudioContext; if(!AC) return;
    try{
      this.ctx=new AC();
      this.master=this.ctx.createGain(); this.master.gain.value=.85; this.master.connect(this.ctx.destination);
      this.sfxG=this.ctx.createGain(); this.sfxG.gain.value=.5; this.sfxG.connect(this.master);
      this.musG=this.ctx.createGain(); this.musG.gain.value=.24; this.musG.connect(this.master);
      const n=this.ctx.sampleRate*.6, buf=this.ctx.createBuffer(1,n,this.ctx.sampleRate), d=buf.getChannelData(0);
      for(let i=0;i<n;i++) d[i]=(Math.random()*2-1)*(1-i/n);
      this.noise=buf; this.ok=true;
    }catch(e){}
  },
  wake(){ if(this.ctx && this.ctx.state==='suspended') this.ctx.resume(); },
  t(){ return this.ctx?this.ctx.currentTime:0; },
  tone(f,{dur=.12,type='triangle',gain=.3,at=0,to=null,dest=null}={}){
    if(!this.ok || !(this.sfx||this.music)) return;
    const c=this.ctx, t0=c.currentTime+at;
    const o=c.createOscillator(); o.type=type; o.frequency.setValueAtTime(f,t0);
    if(to) o.frequency.exponentialRampToValueAtTime(Math.max(30,to), t0+dur);
    const g=c.createGain();
    g.gain.setValueAtTime(0,t0); g.gain.linearRampToValueAtTime(gain,t0+.01);
    g.gain.exponentialRampToValueAtTime(.0008,t0+dur);
    o.connect(g); g.connect(dest||this.sfxG); o.start(t0); o.stop(t0+dur+.04);
  },
  noiseBurst({dur=.12,gain=.25,at=0,f=1400,q=1,dest=null,sweep=null}){
    if(!this.ok || !this.sfx) return;
    const c=this.ctx, t0=c.currentTime+at;
    const s=c.createBufferSource(); s.buffer=this.noise;
    const bp=c.createBiquadFilter(); bp.type='bandpass'; bp.Q=q; bp.frequency.setValueAtTime(f,t0);
    if(sweep) bp.frequency.exponentialRampToValueAtTime(sweep,t0+dur);
    const g=c.createGain();
    g.gain.setValueAtTime(gain,t0); g.gain.exponentialRampToValueAtTime(.001,t0+dur);
    s.connect(bp); bp.connect(g); g.connect(dest||this.sfxG); s.start(t0); s.stop(t0+dur+.02);
  },
  /* ---- sound effects ---- */
  flap(){ if(!this.sfx)return; this.noiseBurst({dur:.14,gain:.16,f:900,sweep:2600,q:.7});
          this.tone(430,{to:760,dur:.1,gain:.13,type:'triangle'}); },
  coin(){ this.tone(880,{dur:.07,gain:.22,type:'square'}); this.tone(1320,{at:.06,dur:.13,gain:.19,type:'square'}); },
  cherry(){ this.tone(1180,{dur:.08,gain:.2,type:'triangle'}); this.tone(1760,{at:.05,dur:.14,gain:.16,type:'triangle',to:2100}); },
  ping(){ this.tone(1560,{dur:.1,gain:.14,type:'triangle',to:2000}); },
  shield(){ this.tone(300,{to:1200,dur:.24,gain:.2,type:'sine'}); this.noiseBurst({dur:.2,gain:.16,f:600,sweep:3000}); },
  crash(){ if(!this.sfx)return; this.noiseBurst({dur:.42,gain:.4,f:420,sweep:90,q:.5});
           this.tone(220,{to:55,dur:.5,gain:.24,type:'sawtooth'}); },
  chime(){ [523,659,784,1046,1318].forEach((f,i)=>this.tone(f,{at:i*.055,dur:.5,gain:.16,type:'triangle',dest:this.musG})); },
  ui(){ this.tone(640,{dur:.06,gain:.1,type:'square'}); },
  uiNo(){ this.tone(200,{to:120,dur:.14,gain:.12,type:'square'}); },
  pop(){ this.tone(500,{to:900,dur:.1,gain:.16,type:'sine'}); },
  /* ---- music: 32-step patterns, bright by day, soft by night ---- */
  seq:{step:0,next:0,tempo:118},
  musicTick(){
    if(!this.ok || !this.music || !this.sfx) return;
    if(!(state==='menu'||state==='ready'||state==='play'||state==='dying')) return;
    const c=this.ctx, spb=60/this.seq.tempo/2;                 // eighth notes
    let guard=0;
    while(this.seq.next < c.currentTime + .2 && guard++<24){
      this.playStep(this.seq.step, this.seq.next, spb);
      this.seq.next += spb; this.seq.step++;
    }
    if(this.seq.next < c.currentTime) this.seq.next = c.currentTime + .05;
  },
  playStep(s,t,spb){
    const bar=Math.floor(s/8)%4, night = (biomeFloat%BIOMES.length) >= 3;
    const majC=[0,7,4,9], minC=[0,3,7,10], root=night?57:60;
    const chord=(night?minC:majC).map(n=>root+n);
    const pent = night?[0,3,5,7,10]:[0,2,4,7,9];
    const inBar=s%8;
    if(inBar===0||inBar===5) this.tone(chord[0]-24,{dur:spb*1.7,type:'triangle',gain:.16,at:t-this.t(),dest:this.musG});
    if(s%2===1) this.noiseBurst({dur:.04,gain:.045,at:t-this.t(),f:5200,q:.9,dest:this.musG});
    if(biomeFloat>=3 && Math.abs((biomeFloat%1)-.5)<.5 && s%8===2)
      this.tone(chord[2]-12,{dur:spb*2,type:'sine',gain:.07,at:t-this.t(),dest:this.musG});
    const mel=[0,2,null,1,3,null,4,2];
    const m=mel[inBar];
    if(m!==null && !(score>0 && score%10>=5 && inBar%2===1)){
      const n=pent[m%pent.length] + (s%16<8?12:7) + root;
      this.tone(n,{dur:spb*1.4,type:'square',gain:.055,at:t-this.t(),dest:this.musG});
      if(night) this.tone(n+12,{dur:spb,type:'triangle',gain:.03,at:t-this.t()+.005,dest:this.musG});
    }
  }
};

/* ============================================================================
   2 · WORLDS — six skies, each with its own pipes, plants and weather
   ========================================================================== */
const BIOMES=[
  {name:'Peachrise',skyTop:'#FFC79B',skyBot:'#FFEDD2',haze:'#FFDCBE',
   far:'mountains',far1:'#F8B8C6',far2:'#E9A2B7',mid:'bushes',mid1:'#8FDDBA',mid2:'#57C39A',
   ground:'#9CE7B4',dirt:'#E8B98E',pipe:'candy',p1:'#FF9DB4',p2:'#F2587F',trim:'#FFF3D6',
   part:'petal',partC:'#FFB6C9',night:0,light:'#FFE7B0'},
  {name:'Mintnoon',skyTop:'#A9E5E7',skyBot:'#E6F8E9',haze:'#D3F0EA',
   far:'hills',far1:'#9FE3C4',far2:'#68CCAA',mid:'trees',mid1:'#4FB287',mid2:'#2E8F6E',
   ground:'#86DFA9',dirt:'#D8B489',pipe:'bamboo',p1:'#A9E9C3',p2:'#46B48C',trim:'#FFFDF0',
   part:'fluff',partC:'#FFFFFF',night:0,light:'#FFFFFF'},
  {name:'Coralset',skyTop:'#FF9E7A',skyBot:'#FFE2A4',haze:'#FFC48C',
   far:'mesa',far1:'#E98A79',far2:'#C4635F',mid:'cactus',mid1:'#7FBE86',mid2:'#54966A',
   ground:'#EFC98A',dirt:'#D89B63',pipe:'coral',p1:'#FFB27A',p2:'#E8705A',trim:'#FFF0CE',
   part:'leaf',partC:'#FFC06B',night:.15,light:'#FFD9A0'},
  {name:'Dusksea',skyTop:'#7E96D8',skyBot:'#F0CBD8',haze:'#B4C1EC',
   far:'islands',far1:'#8D9BD8',far2:'#5E6DB4',mid:'islandsSmall',mid1:'#6A79C4',mid2:'#48549A',
   ground:'#B4C7E8',dirt:'#8C9AC9',pipe:'crystal',p1:'#CBD9FF',p2:'#7B8BDA',trim:'#F4F0FF',
   part:'star',partC:'#FFF0B4',night:.7,light:'#CFE0FF'},
  {name:'Frostbite',skyTop:'#31426F',skyBot:'#9DC3DA',haze:'#5F7FA3',
   far:'pines',far1:'#486E82',far2:'#2C4A5E',mid:'snowtrees',mid1:'#EAF4FF',mid2:'#B9D6E8',
   ground:'#E9F4FF',dirt:'#9FB6C6',pipe:'icicle',p1:'#DFF3FF',p2:'#8FC2E4',trim:'#FFFFFF',
   part:'snow',partC:'#FFFFFF',night:.85,light:'#CFE8FF'},
  {name:'Auroramilk',skyTop:'#0F3644',skyBot:'#2E7C74',haze:'#1B5A5B',
   far:'glowtrees',far1:'#1D5B57',far2:'#123F45',mid:'glowtrees',mid1:'#2E8A75',mid2:'#1C5D5A',
   ground:'#2F7F6B',dirt:'#1B4A48',pipe:'glow',p1:'#7FF0DC',p2:'#2BB8A6',trim:'#EAFFF8',
   part:'mote',partC:'#B9FFEA',night:1,light:'#9CFFE0',aurora:true}
];
function biomeAt(i){ return BIOMES[((i%BIOMES.length)+BIOMES.length)%BIOMES.length]; }
function mixB(key,t){
  const i=Math.floor(t), a=biomeAt(i)[key], b=biomeAt(i+1)[key];
  if(typeof a==='number') return lerp(a,b,t-i);
  if(typeof a==='boolean') return t-i>.5?b:a;
  return mixc(a,b,Math.pow(t-i,.85));
}
function keyB(key,t){ return (t-i0(t)) < .5 ? biomeAt(t)[key] : biomeAt(t+1)[key]; }
function i0(t){ return Math.floor(t); }

/* ============================================================================
   3 · COSMETICS
   ========================================================================== */
const HATS=[
  {id:'none',name:'Bare head',need:0},
  {id:'party',name:'Party cone',need:5},
  {id:'daisy',name:'Daisy crown',need:12},
  {id:'crown',name:'Tiny crown',need:25},
  {id:'wizard',name:'Star hat',need:45},
  {id:'astro',name:'Bubble helm',need:70}
];
const SKINS=[
  {id:'peach',name:'Peaches',need:0,body:'#FFCB7B',wing:'#FF8A55',belly:'#FFF1CE',beak:'#FF9F2B',glow:false},
  {id:'mint', name:'Minty',need:10,body:'#A9E8C9',wing:'#54BE9B',belly:'#ECFFF6',beak:'#FFB24A',glow:false},
  {id:'boba', name:'Bluebub',need:25,body:'#9FB9F6',wing:'#667CD8',belly:'#E7EDFF',beak:'#FFC24D',glow:false},
  {id:'mochi',name:'Sakura',need:45,body:'#FFC1D2',wing:'#F2809F',belly:'#FFF1F5',beak:'#FFA23A',glow:false},
  {id:'wisp', name:'Will-o-Bird',need:70,body:'#D3F7EC',wing:'#8CDCC9',belly:'#F5FFFC',beak:'#B7E8FF',glow:true}
];

/* ============================================================================
   4 · SPRITE PAINTERS (shared by game + UI chips)
   ========================================================================== */
function paintBird(g2,x,y,r,sk,hat,ang,blink,wingPhase,bubble){
  g2.save(); g2.translate(x,y); g2.rotate(ang||0);
  if(sk.glow){
    const gg=g2.createRadialGradient(0,0,r*.4,0,0,r*2.4);
    gg.addColorStop(0,'rgba(200,255,240,.55)'); gg.addColorStop(1,'rgba(200,255,240,0)');
    g2.fillStyle=gg; g2.beginPath(); g2.arc(0,0,r*2.4,0,7); g2.fill();
  }
  const LW=Math.max(1.2,r*.15);
  /* tail */
  g2.save(); g2.rotate(Math.sin(wingPhase*2)*.08);
  g2.beginPath(); g2.moveTo(-r*.75,-r*.2); g2.lineTo(-r*1.6,-r*.55); g2.lineTo(-r*1.5,r*.05);
  g2.lineTo(-r*1.7,r*.4); g2.lineTo(-r*.7,r*.35); g2.closePath();
  g2.fillStyle=sk.wing; g2.fill(); g2.lineWidth=LW; g2.strokeStyle='rgba(59,43,47,.9)'; g2.stroke();
  g2.restore();
  /* body */
  g2.beginPath(); g2.ellipse(0,0,r*1.12,r*.98,0,0,7);
  g2.fillStyle=sk.body; g2.fill();
  g2.lineWidth=LW; g2.strokeStyle='rgba(59,43,47,.92)'; g2.stroke();
  /* belly */
  g2.save(); g2.beginPath(); g2.ellipse(0,0,r*1.12,r*.98,0,0,7); g2.clip();
  ell(r*.12,r*.55,r*.82,r*.62); g2.fillStyle=sk.belly; g2.fill();
  g2.restore();
  /* wing */
  g2.save(); g2.translate(-r*.08,r*.02);
  g2.rotate(Math.sin(wingPhase)*.95 + (Math.cos(wingPhase)<0? -.15:.1));
  g2.beginPath(); g2.ellipse(-r*.15,0,r*.82,r*.52,0,0,7);
  g2.fillStyle=sk.wing; g2.fill(); g2.lineWidth=LW; g2.strokeStyle='rgba(59,43,47,.92)'; g2.stroke();
  g2.beginPath(); g2.ellipse(-r*.3,r*.02,r*.42,r*.24,0,0,7);
  g2.fillStyle='rgba(255,255,255,.34)'; g2.fill();
  g2.restore();
  /* beak */
  g2.beginPath(); g2.moveTo(r*.85,-r*.12); g2.lineTo(r*1.62,r*.05); g2.lineTo(r*.85,r*.34); g2.closePath();
  g2.fillStyle=sk.beak; g2.fill(); g2.lineWidth=LW; g2.strokeStyle='rgba(59,43,47,.92)'; g2.stroke();
  /* blush */
  ell(r*.42,r*.4,r*.24,r*.16); g2.fillStyle='rgba(255,120,150,.42)'; g2.fill();
  /* eye */
  const ex=r*.5, ey=-r*.35;
  if(blink){
    g2.beginPath(); g2.moveTo(ex-r*.36,ey); g2.lineTo(ex+r*.3,ey+r*.08);
    g2.lineWidth=LW*1.1; g2.strokeStyle='rgba(59,43,47,.95)'; g2.stroke();
  }else{
    cir(ex,ey,r*.38); g2.fillStyle='#fff'; g2.fill();
    g2.lineWidth=LW*.9; g2.strokeStyle='rgba(59,43,47,.9)'; g2.stroke();
    cir(ex+r*.09,ey+r*.02,r*.2); g2.fillStyle='#2c2024'; g2.fill();
    cir(ex-r*.04,ey-r*.1,r*.09); g2.fillStyle='rgba(255,255,255,.95)'; g2.fill();
  }
  /* hat */
  if(hat && hat!=='none') paintHat(g2,r,hat);
  /* bubble shield */
  if(bubble){
    cir(0,0,r*1.65);
    const bg=g2.createRadialGradient(-r*.5,-r*.6,r*.2,0,0,r*1.65);
    bg.addColorStop(0,'rgba(255,255,255,.55)'); bg.addColorStop(.6,'rgba(150,235,255,.22)');
    bg.addColorStop(1,'rgba(120,220,255,.42)');
    g2.fillStyle=bg; g2.fill();
    g2.lineWidth=LW*.9; g2.strokeStyle='rgba(255,255,255,.85)'; g2.stroke();
    g2.beginPath(); g2.arc(-r*.55,-r*.7,r*.5,Math.PI*1.05,Math.PI*1.55);
    g2.lineWidth=LW*1.2; g2.strokeStyle='rgba(255,255,255,.9)'; g2.stroke();
  }
  g2.restore();
}
function paintHat(g2,r,hat){
  const LW=Math.max(1.2,r*.15);
  g2.lineWidth=LW; g2.strokeStyle='rgba(59,43,47,.92)';
  const top=-r*.92;
  if(hat==='party'){
    g2.beginPath(); g2.moveTo(-r*.34,top+r*.1); g2.lineTo(r*.05,top-r*1.25); g2.lineTo(r*.42,top+r*.16); g2.closePath();
    g2.fillStyle='#FF8FAB'; g2.fill(); g2.stroke();
    g2.save(); g2.beginPath(); g2.moveTo(-r*.34,top+r*.1); g2.lineTo(r*.05,top-r*1.25); g2.lineTo(r*.42,top+r*.16); g2.closePath(); g2.clip();
    g2.strokeStyle='rgba(255,243,214,.85)'; g2.lineWidth=LW*.9;
    for(let i=-2;i<4;i++){ g2.beginPath(); g2.moveTo(-r+i*r*.3,top-r*1.3); g2.lineTo(-r*.4+i*r*.3,top+r*.3); g2.stroke(); }
    g2.restore();
    cir(r*.05,top-r*1.36,r*.24); g2.fillStyle='#FFE58A'; g2.fill();
    g2.lineWidth=LW; g2.strokeStyle='rgba(59,43,47,.92)'; g2.stroke();
  } else if(hat==='daisy'){
    for(let i=0;i<4;i++){
      const a=-Math.PI*.82+i*.55, dx=Math.cos(a)*r*.72, dy=top+Math.sin(a)*r*.34+r*.06;
      for(let p=0;p<5;p++){ const pa=p*Math.PI*2/5;
        cir(dx+Math.cos(pa)*r*.15,dy+Math.sin(pa)*r*.15,r*.14); g2.fillStyle='#fff'; g2.fill(); }
      cir(dx,dy,r*.12); g2.fillStyle='#FFD25E'; g2.fill();
      g2.lineWidth=LW*.8; g2.strokeStyle='rgba(59,43,47,.8)'; g2.stroke();
    }
  } else if(hat==='crown'){
    g2.beginPath(); g2.moveTo(-r*.55,top+r*.12);
    g2.lineTo(-r*.55,top-r*.3); g2.lineTo(-r*.26,top-r*.02); g2.lineTo(0,top-r*.5);
    g2.lineTo(r*.26,top-r*.02); g2.lineTo(r*.55,top-r*.3); g2.lineTo(r*.55,top+r*.12);
    g2.closePath(); g2.fillStyle='#FFD86B'; g2.fill(); g2.stroke();
    cir(0,top-r*.1,r*.1); g2.fillStyle='#FF6E8A'; g2.fill();
    cir(-r*.3,top+r*.02,r*.07); g2.fillStyle='#7BD8FF'; g2.fill();
  } else if(hat==='wizard'){
    g2.beginPath(); g2.ellipse(0,top+r*.14,r*.78,r*.24,0,0,7); g2.fillStyle='#6E63C9'; g2.fill(); g2.stroke();
    g2.beginPath(); g2.moveTo(-r*.44,top+r*.1); g2.quadraticCurveTo(-r*.1,top-r*.9,r*.16,top-r*1.2);
    g2.quadraticCurveTo(r*.34,top-r*.6,r*.44,top+r*.1); g2.closePath();
    g2.fillStyle='#7A6FD8'; g2.fill(); g2.stroke();
    g2.fillStyle='#FFE58A';
    star(r*.02,top-r*.72,5,r*.2,r*.09,-Math.PI/2); g2.fill();
    star(-r*.2,top-r*.3,5,r*.13,r*.06,.3); g2.fill();
  } else if(hat==='astro'){
    cir(0,-r*.1,r*1.42);
    const bg=g2.createRadialGradient(-r*.5,-r*.7,r*.2,0,-r*.1,r*1.42);
    bg.addColorStop(0,'rgba(255,255,255,.7)'); bg.addColorStop(.55,'rgba(190,235,255,.28)');
    bg.addColorStop(1,'rgba(140,215,255,.5)');
    g2.fillStyle=bg; g2.fill(); g2.lineWidth=LW; g2.strokeStyle='rgba(59,43,47,.6)'; g2.stroke();
    g2.beginPath(); g2.arc(-r*.5,-r*.75,r*.55,Math.PI*1.02,Math.PI*1.6);
    g2.lineWidth=LW*1.3; g2.strokeStyle='rgba(255,255,255,.9)'; g2.stroke();
    cir(r*.55,-r*1.35,r*.16); g2.fillStyle='#FF8FAB'; g2.fill();
    g2.beginPath(); g2.moveTo(r*.4,-r*1.2); g2.lineTo(r*.62,-r*1.02);
    g2.lineWidth=LW; g2.strokeStyle='rgba(59,43,47,.8)'; g2.stroke();
  }
}
function paintCherry(g2,x,y,r,spin){
  g2.save(); g2.translate(x,y); g2.rotate(spin);
  g2.lineWidth=Math.max(1.1,r*.14);
  g2.strokeStyle='#3E9A72'; g2.beginPath();
  g2.moveTo(-r*.1,-r*.9); g2.quadraticCurveTo(-r*.7,-r*.3,-r*.45,r*.25);
  g2.moveTo(-r*.1,-r*.9); g2.quadraticCurveTo(r*.6,-r*.35,r*.45,r*.3); g2.stroke();
  g2.beginPath(); g2.ellipse(r*.42,-r*.92,r*.42,r*.22,-.5,0,7); g2.fillStyle='#57C38F'; g2.fill();
  cir(-r*.5,r*.42,r*.5); g2.fillStyle='#E8365F'; g2.fill();
  g2.lineWidth=Math.max(1.1,r*.13); g2.strokeStyle='rgba(59,43,47,.85)'; g2.stroke();
  cir(r*.42,r*.5,r*.44); g2.fillStyle='#FF6E8A'; g2.fill(); g2.stroke();
  cir(-r*.62,r*.26,r*.14); g2.fillStyle='rgba(255,255,255,.85)'; g2.fill();
  cir(r*.32,r*.36,r*.12); g2.fillStyle='rgba(255,255,255,.8)'; g2.fill();
  g2.restore();
}
function paintStar(g2,x,y,r,spin){
  g2.save(); g2.translate(x,y); g2.rotate(spin);
  star(0,0,5,r,r*.46,-Math.PI/2); g2.fillStyle='#FFDD75'; g2.fill();
  g2.lineWidth=Math.max(1.2,r*.16); g2.strokeStyle='rgba(59,43,47,.9)'; g2.stroke();
  star(0,0,5,r*.55,r*.24,-Math.PI/2); g2.fillStyle='rgba(255,255,255,.6)'; g2.fill();
  g2.restore();
}

/* ============================================================================
   5 · STATE
   ========================================================================== */
const CFG={
  cozy:   {name:'Cozy',   grav:1480,flap:-468,speed:172,maxSpeed:232,gap:252,minGap:206,space:250,r:12.5,moveAt:99,shield:.4,drift:5,  music:108},
  classic:{name:'Classic',grav:1720,flap:-518,speed:206,maxSpeed:330,gap:214,minGap:170,space:232,r:11.5,moveAt:6,shield:.14,drift:14, music:120},
  spicy:  {name:'Spicy',  grav:1920,flap:-556,speed:248,maxSpeed:392,gap:188,minGap:160,space:208,r:11,  moveAt:3,shield:.09,drift:22, music:134}
};
const METER_NEED=6;
let cfg=CFG.classic;
const SAVE_KEY='littlesky.v1';
const store=(()=>{ try{ return JSON.parse(localStorage.getItem(SAVE_KEY))||{} }catch(e){ return {} } })();
function save(){ try{ localStorage.setItem(SAVE_KEY,JSON.stringify(save_)) }catch(e){} }
const save_ = Object.assign({best:0,cherries:0,hat:'none',skin:'peach',mode:'classic',music:true,sfx:true},store);
cfg = CFG[save_.mode]||CFG.classic;
SND.music = save_.music!==false; SND.sfx = save_.sfx!==false;

let state='menu', prevState='play';
let t=0, dist=0, camX=0, speed=cfg.speed, score=0, runCherries=0;
let hitstop=0, flash=0, shake=0, shakeX=0, shakeY=0, camY=0, camYv=0, slow=0;
let hitTimer=0, spawnCursor=0, lastShieldAt=-99, pipesPassed=0;
let meter=0, starT=0, combo=0, comboT=0, popupIid=0;
let biomeFloat=0, nightAmt=0;
let pipes=[], pickups=[], parts=[], texts=[], trail=[], skyParticles=[], fireflies=[];
let decor={far:[],mid:[],clouds:[],critters:[],tufts:[]};
let decorFarX=-400, decorMidX=-400, decorCloudX=-300, decorTuftX=-200;
let starsMade=false, starField=[];

const bird={x:150,y:330,vy:0,ang:0,sx:1,sy:1,wing:0,wingSpeed:9,flapT:0,blink:0,blinkAt:1.4,
            shield:0,inv:0,dead:false};
function skin(){ return SKINS.find(s=>s.id===save_.skin)||SKINS[0]; }
function hat(){ return HATS.find(h=>h.id===save_.hat)||HATS[0]; }
function unlocked(list){ return list.filter(h=>save_.best>=h.need); }

/* ============================================================================
   6 · LEVEL GENERATION
   ========================================================================== */
function newPipe(x){
  const idx=Math.floor(biomeFloat), B=biomeAt(biomeFloat), n=biomeAt(biomeFloat+1);
  const variant = (idx + (Math.floor(x/430)%6)) % 6;
  const vName = (biomeFloat%1)<.5 ? B.pipe : n.pipe;
  const hole = Math.max(cfg.minGap, cfg.gap - dist/1050 - (cfg===CFG.spicy?10:0));
  const margin = 66;
  const prev = pipes.length? pipes[pipes.length-1] : null;
  let cy = rnd(margin+hole/2, GROUND_Y-margin-hole/2);
  if(prev) cy = clamp(cy, prev.cy-230, prev.cy+230);
  const moving = score>=cfg.moveAt && Math.random()<.42;
  const p={
    x, w:76, hole, cy, base:cy, bobA: moving? rnd(24,Math.min(52,18+score*.6)) : 0,
    bobS: rnd(.7,1.25), phase:Math.random()*6.28, variant:vName, passed:false, dead:false,
    sprout: Math.random()<.55 ? (Math.random()<.5?'flower':'leaf') : null,
    bug: Math.random()<.18, face: Math.random()<.28,
    hue: hash(Math.round(x))
  };
  pipes.push(p);
  /* pickets -------------------------------------------------- */
  const gapW = cfg.space - p.w;
  if(hitTimer>1.4 && Math.random()<cfg.shield && !pickups.some(k=>k.type==='bubble'&&k.x>x-200)){
    pickups.push({type:'bubble',x:x+gapW*.62,y:clamp(rnd(170,GROUND_Y-120),140,GROUND_Y-40),r:13,taken:false,t:Math.random()*6});
    hitTimer-=1.2;
  } else if(Math.random()<.9){
    const n = (Math.random()<.34 && score>4)?2:1;
    for(let i=0;i<n;i++){
      const fx = x + 110 + i*46;
      const arc = Math.random()<.5;
      const y = arc ? clamp(p.cy + Math.sin(i*1.5)* -18 + 6, 120, GROUND_Y-34)
                    : clamp(p.cy + rnd(-6,6), 120, GROUND_Y-34);
      pickups.push({type:'cherry',x:fx,y,r:11,taken:false,t:Math.random()*6});
    }
    if(Math.random()<.12 && score>7)
      pickups.push({type:'star',x:x+gapW*.5,y:p.cy+rnd(-70,70),r:12,taken:false,t:0});
  }
}
function ensureDecor(until){
  /* far layer */
  while(decorFarX < until+300){
    const B=biomeAt(biomeFloat);
    const kind = keyB('far',biomeFloat);
    const w = kind==='mountains'?rnd(220,330): kind==='islands'?rnd(90,150): rnd(120,220);
    decor.far.push({x:decorFarX,w,kind,h:rnd(.5,1),seed:decorFarX});
    decorFarX += w*rnd(.5,.75);
  }
  /* mid layer */
  while(decorMidX < until+300){
    const kind = keyB('mid',biomeFloat);
    decor.mid.push({x:decorMidX,kind,h:rnd(.6,1.15),seed:decorMidX,flip:Math.random()<.5});
    decorMidX += rnd(58,150);
  }
  /* clouds */
  while(decorCloudX < until+400){
    const depth = pick([.18,.3,.44]);
    decor.clouds.push({x:decorCloudX,y:rnd(30,340),r:rnd(26,64),depth,puffs:ri(3,5),seed:decorCloudX});
    decorCloudX += rnd(90,220);
  }
  /* foreground tufts */
  while(decorTuftX < until+300){
    decor.tufts.push({x:decorTuftX,kind:pick(['grass','grass','flower','reeds','pebble']),seed:decorTuftX,h:rnd(.7,1.2)});
    decorTuftX += rnd(26,74);
  }
  /* sky critters */
  if(decor.critters.length<6 && Math.random()<.012){
    decor.critters.push({x:camX+700,y:rnd(60,300),vx:-rnd(24,52),ph:Math.random()*6,size:rnd(4,7),
                        type: Math.random()<.22?'balloon':'bird'});
  }
  decor.critters = decor.critters.filter(c=>c.x - camX*.7 > -180);
  /* prune */
  const lo = camX-500;
  if(decor.far.length>90) decor.far = decor.far.filter(d=>d.x+d.w>lo);
  if(decor.mid.length>140) decor.mid = decor.mid.filter(d=>d.x>lo);
  if(decor.clouds.length>60) decor.clouds = decor.clouds.filter(d=>d.x>lo-400);
  if(decor.tufts.length>140) decor.tufts = decor.tufts.filter(d=>d.x>lo);
}
function makeStars(){
  starField=[]; for(let i=0;i<90;i++) starField.push({x:rnd(-100,W+300),y:rnd(10,430),r:rnd(.8,2.1),tw:rnd(1,3),ph:Math.random()*6});
  starsMade=true;
}

/* ============================================================================
   7 · PARTICLES
   ========================================================================== */
function P(o){
  if(parts.length>460) parts.splice(0,60);
  parts.push(Object.assign({x:0,y:0,vx:0,vy:0,g:0,life:.5,age:0,r:4,c:'#fff',type:'dot',front:true,rot:0,vr:0,fade:1},o));
}
function burst(x,y,n,opt){ for(let i=0;i<n;i++){ const a=rnd(0,6.283),s=rnd(opt.s0||40,opt.s1||160);
  P(Object.assign({x,y,vx:Math.cos(a)*s,vy:Math.sin(a)*s-30,life:rnd(.35,.75),g:340,r:rnd(2,5),type:'dot'},opt)); } }
function popup(x,y,s,c,size){ texts.push({x,y,s,c:c||'#FFF3D6',size:size||15,life:.95,age:0,vy:-52}); }

function updateParts(dt){
  for(const p of parts){
    p.age+=dt; p.x+=p.vx*dt; p.y+=p.vy*dt; p.vy+=p.g*dt; p.rot+=p.vr*dt;
    if(p.type==='feather'){ p.vx += Math.sin((p.age+p.rot)*7)*18*dt; p.vx*=.99; }
  }
  parts=parts.filter(p=>p.age<p.life);
  for(const tx of texts){ tx.age+=dt; tx.y+=tx.vy*dt; tx.vy*=.965; }
  texts=texts.filter(t2=>t2.age<t2.life);
}

/* ============================================================================
   8 · GAMEPLAY
   ========================================================================== */
function reset(){
  pipes=[];pickups=[];parts=[];texts=[];trail=[];fireflies=[];
  dist=0;camX=0;speed=cfg.speed;score=0;runCherries=0;meter=0;starT=0;combo=0;hitTimer=0;
  spawnCursor=560;lastShieldAt=-99;pipesPassed=0;hitstop=0;flash=0;shake=0;slow=0;camY=0;camYv=0;
  bird.x=150;bird.y=330;bird.vy=0;bird.ang=0;bird.sx=1;bird.sy=1;bird.shield=0;bird.inv=0;bird.dead=false;
  biomeFloat=0;nightAmt=0;starsMade=false;
  decor={far:[],mid:[],clouds:[],critters:[],tufts:[]};
  decorFarX=-400;decorMidX=-400;decorCloudX=-300;decorTuftX=-200;
  ensureDecor(900);
  updateHUD();
}
function startRun(){
  reset(); state='ready';
  hidePanels(); SND.wake(); SND.chime();
  toast('breathe in… flap out','cool');
}
function flap(){
  if(state==='menu'){ startRun(); }
  if(state==='over'){ startRun(); return; }
  if(state==='paused'){ togglePause(); return; }
  if(state==='play' || state==='ready'){
    if(state==='ready'){ state='play'; }
    doFlap();
  }
}
function doFlap(){
  bird.vy = cfg.flap*(starT>0?1.06:1);
  bird.flapT=.34; bird.sx=1.22; bird.sy=.8;
  SND.flap();
  for(let i=0;i<3;i++) P({x:bird.x-10,y:bird.y+4,vx:rnd(-90,-30),vy:rnd(-20,50),r:rnd(2,4),
    c:'rgba(255,255,255,.75)',life:rnd(.2,.42),type:'dot',front:false});
}
function togglePause(){
  if(state==='play'||state==='ready'){ prevState=state; state='paused'; show('panelPause',true); SND.ui(); }
  else if(state==='paused'){ state=prevState; show('panelPause',false); SND.ui(); }
}
function toMenu(){ state='menu'; reset(); show('panelOver',false); show('panelStart',true); }

function hurt(reason,p){
  if(bird.inv>0 || starT>0) return;
  if(bird.shield>0){
    bird.shield=0; bird.inv=1.15; slow=.55; shake=11; hitstop=.09;
    SND.shield(); burst(bird.x,bird.y,18,{c:'rgba(160,235,255,.9)',s0:70,s1:230,life:.6,type:'dot',front:true});
    parts.push({x:bird.x,y:bird.y,life:.45,age:0,type:'ring',r:20,r2:70,c:'rgba(255,255,255,.9)',front:true});
    if(p && typeof p==='object'){ const cy=p.base; bird.y=clamp(bird.y<cy?p.cy-p.hole/2+24:p.cy+p.hole/2-24,40,GROUND_Y-20); }
    else bird.y=Math.min(bird.y,GROUND_Y-60);
    bird.vy=-230; popup(bird.x,bird.y-26,'phew!','#CFF6FF',15);
    return;
  }
  die();
}
function die(){
  if(state!=='play') return;
  state='dying'; bird.dead=true; bird.inv=2; hitstop=.13; shake=20; flash=.55;
  SND.crash();
  for(let i=0;i<16;i++) P({x:bird.x,y:bird.y,vx:rnd(-140,90),vy:rnd(-160,-20),life:rnd(.7,1.4),
    r:rnd(4,8),c:pick([skin().body,skin().wing,'#FFF3D6']),type:'feather',g:180,front:true,rot:rnd(0,6),vr:rnd(-5,5)});
  burst(bird.x,bird.y,22,{c:'rgba(255,236,190,.9)',s0:90,s1:240,life:.6});
  setTimeout(()=>{ if(state==='dying') gameOver(); }, 950);
}

function updatePlay(dt){
  const dir = state==='dying'?.22:1;
  const sp = speed*(slow>0?.42:1);
  camX += sp*dt*dir; dist += sp*dt*dir;
  if(state==='play') speed = Math.min(cfg.maxSpeed, cfg.speed + dist/26);
  if(slow>0) slow-=dt;
  if(hitTimer<9) hitTimer+=dt;

  /* pipe spawning */
  if(state==='play'){
    spawnCursor -= sp*dt;
    while(spawnCursor<=0){ newPipe(spawnCursor); spawnCursor += cfg.space; }
  }

  /* pipes */
  for(const p of pipes){
    p.x -= sp*dt*dir;
    p.phase += dt*p.bobS;
  }
  for(const k of pickups) k.x -= sp*dt*dir, k.t+=dt;
  pipes = pipes.filter(p=>p.x+p.w>-80);
  pickups = pickups.filter(k=>!k.taken && k.x>-60);

  if(state!=='play') return;

  /* physics + collision, substepped so fast frames can't tunnel */
  const steps=3, h=dt/steps, C=cfg;
  for(let s=0;s<steps;s++){
    bird.vy = Math.min(760, bird.vy + C.grav*h);
    bird.y += bird.vy*h;
    bird.x += (C.drift - 34)*h * (bird.vy<0?1.15:.6);
    bird.x = clamp(bird.x, 84, 268);
    if(bird.y<26){ bird.y=26; bird.vy=Math.max(bird.vy,-40); }
    if(bird.y+C.r >= GROUND_Y){ bird.y=GROUND_Y-C.r; die(); return; }
    /* pipes */
    for(const p of pipes){
      if(p.dead) continue;
      if(bird.x+C.r<p.x || bird.x-C.r>p.x+p.w) continue;
      const cy = p.base + Math.sin(p.phase)*p.bobA, half=p.hole/2;
      const topY=cy-half, botY=cy+half;
      const near = Math.abs(bird.x-(p.x+p.w/2)) < p.w/2+C.r;
      if(!near) continue;
      if(bird.y-C.r < topY || bird.y+C.r > botY){
        const pen = bird.y<cy ? (topY-(bird.y-C.r)) : ((bird.y+C.r)-botY);
        if(pen>0){
          if(starT>0){ destroyPipe(p); }
          else { hurt('pipe',p); return; }
        }
      }
    }
    /* pickups */
    for(const k of pickups){
      if(k.taken) continue;
      const dx=k.x-bird.x, dy=k.y-bird.y, rr2=(k.r+15+ (starT>0?60:0))**2;
      if(dx*dx+dy*dy < rr2){
        if(starT>0 && Math.abs(dx)>44) continue;
        collect(k);
      }
    }
  }
  /* passing pipes */
  for(const p of pipes){
    if(!p.passed && p.x+p.w < bird.x-6){
      p.passed=true; pipesPassed++;
      let pts=1, label='+1';
      const cy=p.base+Math.sin(p.phase)*p.bobA;
      const off=Math.abs(bird.y-cy);
      if(off < p.hole/2 - C.r + 9){ pts=2; label='+2 close!'; flash=Math.max(flash,.14); SND.ping(); }
      if(starT>0) pts*=2;
      score+=pts; popup(bird.x+16,bird.y-8,label,'#FFF3D6',14);
      SND.coin(); camYv = -26;
      if(score%10===0){
        flash=Math.max(flash,.3); shake=Math.max(shake,6);
        toast('✦ '+score+'! the sky gets faster','hot');
        SND.chime();
      }
    }
  }
  /* timers */
  if(starT>0){ starT-=dt; if(starT<=0) toast('sparkle faded','cool'); }
  if(bird.inv>0) bird.inv-=dt;
  if(bird.shield>0 && bird.shield<1) bird.shield=0;
  if(comboT>0){ comboT-=dt; if(comboT<=0) combo=0; }

  /* trail */
  trail.push({x:bird.x-14,y:bird.y,r:7*(starT>0?1.25:1)});
  if(trail.length>16) trail.shift();
  if(starT>0 && Math.random()<.6) P({x:bird.x+rnd(-16,6),y:bird.y+rnd(-12,12),vx:rnd(-60,-20),vy:rnd(-24,24),
    r:rnd(2,4.5),c:pick(['#FFE58A','#FF9DB4','#8FE3C2','#FFF']),life:rnd(.3,.7),type:'dot',front:true});

  /* biome */
  biomeFloat = dist/2700;
  nightAmt = mixB('night',biomeFloat);
  if(nightAmt>.5 && !starsMade) makeStars();
}
function destroyPipe(p){
  p.dead=true; score+=1; popup(p.x+p.w/2,p.base,'+1 smash!','#EAFFF8',14);
  burst(p.x+p.w/2,p.base,16,{c:'rgba(255,255,255,.85)',s0:80,s1:220,life:.5});
  SND.crash(); SND.pop(); shake=Math.max(shake,7);
}
function collect(k){
  k.taken=true;
  if(k.type==='cherry'){
    runCherries++; meter=Math.min(METER_NEED,meter+1); combo++; comboT=3.4;
    score += starT>0?2:1;
    popup(k.x,k.y-10, (combo>2?'yummy ×'+combo:'+1'), '#FFC7D6', 14);
    burst(k.x,k.y,10,{c:'rgba(255,140,170,.9)',s0:50,s1:140,life:.5});
    SND.cherry(); updateHUD();
    if(meter>=METER_NEED){ meter=0; starT=5; combo=0;
      toast('★ STAR POWER — pipes are yours','hot'); SND.chime(); flash=.35; shake=6; }
  } else if(k.type==='star'){
    starT=Math.max(starT,4); score+=2; runCherries+=0;
    popup(k.x,k.y-10,'+2 star!','#FFEE9E',15); SND.chime();
    burst(k.x,k.y,14,{c:'rgba(255,236,150,.95)',s0:60,s1:190,life:.6});
  } else {
    bird.shield=1; popup(k.x,k.y-10,'bubble!','#CFF6FF',15); SND.shield();
    burst(k.x,k.y,12,{c:'rgba(170,240,255,.9)',s0:50,s1:160,life:.55});
  }
}

/* ============================================================================
   9 · UPDATE / RENDER
   ========================================================================== */
function update(dt){
  t+=dt;
  let sdt=dt;
  if(hitstop>0){ hitstop-=dt; sdt=dt*.22; }
  /* decor drift */
  for(const c of decor.critters){
    if(c.type==='balloon'){ c.x += (-18 - camX*0)*sdt*.02; c.y += Math.sin(t*.8+c.ph)*6*sdt; }
    else { c.x += c.vx*sdt; c.y += Math.sin(t*3.1+c.ph)*10*sdt; }
  }
  /* sky particles */
  const B=biomeAt(biomeFloat);
  const ptype = (biomeFloat%1)<.5? B.part : biomeAt(biomeFloat+1).part;
  const pc = mixB('partC',biomeFloat);
  if(skyParticles.length<130 && Math.random()<.55){
    skyParticles.push({x:camX+rnd(-40,W+80),y:rnd(-40,H-120),vx:rnd(-14,-4),vy:rnd(8,34),
      r:rnd(1.8,4.6),c:pc,life:rnd(4,9),age:0,spin:rnd(-3,3),ph:Math.random()*6,type:ptype,front:Math.random()<.35});
  }
  for(const s of skyParticles){
    s.age+=sdt; s.x += (s.vx - speed*(s.front?1:.72) )*sdt; s.y += s.vy*sdt;
    s.y += Math.sin(t*1.4+s.ph)*7*sdt;
  }
  skyParticles=skyParticles.filter(s=>s.age<s.life && s.y<H+20);
  updateParts(sdt);

  if(state==='menu'){
    const p=t*.75;
    bird.x = 168 + Math.cos(p)*44;
    bird.y = 300 + Math.sin(p*1.27)*52;
    bird.ang = Math.cos(p*1.27)*.2;
    bird.wing += sdt*bird.wingSpeed;
    camX += 34*sdt; dist += 34*sdt;
    ensureDecor(camX+700);
    return;
  }
  if(state==='paused') return;
  if(state==='over'){ updatePartsOnly(); return; }

  /* camera vertical */
  camYv += (-clamp((bird.y-360)*.16,-70,90) - camY)*38*sdt;
  camYv *= Math.pow(.0025,sdt); camY += camYv*sdt;

  if(state==='dying'){
    bird.vy=Math.min(780,bird.vy+cfg.grav*1.05*sdt); bird.y+=bird.vy*sdt;
    bird.ang += (bird.ang<2.6? 5.4*sdt:0);
    if(bird.y>GROUND_Y-8){ burst(bird.x,GROUND_Y-6,8,{c:mixB('ground',biomeFloat),s0:30,s1:110,life:.5}); }
  }
  updatePlay(state==='play'||state==='ready'?dt:dt);
  ensureDecor(camX+700);
  /* blink + wing */
  bird.blinkAt-=dt;
  if(bird.blinkAt<=0){ bird.blink=.13; bird.blinkAt=rnd(1.6,4.4); }
  if(bird.blink>0) bird.blink-=dt;
  if(bird.flapT>0){ bird.flapT-=dt; bird.wing+=dt*34; }
  else bird.wing += dt*(2.4+Math.abs(bird.vy)*.004);
  bird.sx=lerp(bird.sx,1,dt*9); bird.sy=lerp(bird.sy,1,dt*9);
  if(state!=='dying'){
    const tgt = clamp(bird.vy/620,-.62,1.25);
    bird.ang = lerp(bird.ang, tgt*.95, dt*11);
  }
  /* shake */
  if(shake>0){ shake=Math.max(0,shake-dt*34); shakeX=rnd(-shake,shake); shakeY=rnd(-shake,shake); }
  else { shakeX=shakeY=0; }
  if(flash>0) flash=Math.max(0,flash-dt*1.6);
  SND.musicTick();
}
function updatePartsOnly(){ updateParts(Math.min(.033,dtGlobal)); }

/* ---------- drawing helpers ---------- */
let curSkyTop,curSkyBot,curHaze,curGround,curDirt,curP1,curP2,curTrim,curLight;
function refreshColors(){
  const b=biomeFloat;
  curSkyTop=mixB('skyTop',b); curSkyBot=mixB('skyBot',b); curHaze=mixB('haze',b);
  curGround=mixB('ground',b); curDirt=mixB('dirt',b);
  curP1=mixB('p1',b); curP2=mixB('p2',b); curTrim=mixB('trim',b); curLight=mixB('light',b);
  curFar1=mixB('far1',b); curFar2=mixB('far2',b); curMid1=mixB('mid1',b); curMid2=mixB('mid2',b);
}
let curFar1,curFar2,curMid1,curMid2;

function drawSky(){
  const g=ctx.createLinearGradient(0,-120,0,GROUND_Y);
  g.addColorStop(0,curSkyTop); g.addColorStop(.62,curSkyBot); g.addColorStop(1,mixc('#ffffff','#ffffff',0));
  ctx.fillStyle=g; ctx.fillRect(0,-140,W,H);
  /* horizon haze */
  const hz=ctx.createLinearGradient(0,GROUND_Y-220,0,GROUND_Y);
  hz.addColorStop(0,'rgba(255,255,255,0)'); hz.addColorStop(1,'rgba(255,255,255,.42)');
  ctx.fillStyle=hz; ctx.fillRect(0,GROUND_Y-220,W,220);
  /* aurora */
  if(keyB('aurora',biomeFloat) && nightAmt>.4){
    ctx.save(); ctx.globalCompositeOperation='lighter';
    for(let b2=0;b2<3;b2++){
      ctx.beginPath();
      for(let x=-20;x<=W+20;x+=14){
        const y=64+b2*40+Math.sin((x+camX*.25)/95 + t*.7 + b2)*24 + Math.sin(x/37+t*.3)*6;
        x===-20?ctx.moveTo(x,y):ctx.lineTo(x,y);
      }
      ctx.lineTo(W+20,-60); ctx.lineTo(-20,-60); ctx.closePath();
      ctx.fillStyle=['rgba(120,255,210,.20)','rgba(150,190,255,.15)','rgba(255,150,200,.13)'][b2];
      ctx.fill();
    }
    ctx.restore();
  }
  /* stars */
  if(nightAmt>.02 && starsMade){
    ctx.save(); ctx.globalAlpha=clamp((nightAmt-.02)*1.25,0,1);
    for(const s of starField){
      const sx=((s.x - camX*.05) % (W+300) + W+300) % (W+300) - 150;
      const tw=.55+.45*Math.sin(t*s.tw+s.ph);
      ctx.globalAlpha=tw*clamp(nightAmt,0,1);
      cir(sx,s.y+camY*.4,s.r); ctx.fillStyle='#FFF6D8'; ctx.fill();
    }
    ctx.restore();
  }
}
function drawSunMoon(){
  const night=nightAmt>.5;
  const x=W-96, y=96 + Math.sin(t*.25)*6;
  ctx.save();
  if(night){
    const g=ctx.createRadialGradient(x,y,4,x,y,88);
    g.addColorStop(0,'rgba(255,247,214,.55)'); g.addColorStop(1,'rgba(255,247,214,0)');
    ctx.fillStyle=g; cir(x,y,88); ctx.fill();
    cir(x,y,26); ctx.fillStyle='#FFF4D2'; ctx.fill();
    ctx.save(); cir(x,y,26); ctx.clip();
    ctx.beginPath(); ctx.arc(x+15,y-7,24,0,7); ctx.fillStyle=mixB('skyTop',biomeFloat); ctx.fill();
    ctx.restore();
    ctx.fillStyle='rgba(200,190,160,.5)';
    cir(x-8,y+6,4); ctx.fill(); cir(x-2,y+14,2.4); ctx.fill();
    cir(x,y,26); ctx.lineWidth=2.2; ctx.strokeStyle='rgba(59,43,47,.55)'; ctx.stroke();
  }else{
    const g=ctx.createRadialGradient(x,y,6,x,y,80);
    g.addColorStop(0,'rgba(255,240,190,.75)'); g.addColorStop(1,'rgba(255,240,190,0)');
    ctx.fillStyle=g; cir(x,y,80); ctx.fill();
    ctx.save(); ctx.translate(x,y); ctx.rotate(t*.12);
    ctx.fillStyle='rgba(255,226,150,.75)';
    for(let i=0;i<10;i++){ ctx.rotate(Math.PI*2/10);
      ctx.beginPath(); ctx.ellipse(42,0,17,5,0,0,7); ctx.fill(); }
    ctx.restore();
    cir(x,y,27); ctx.fillStyle='#FFE79C'; ctx.fill();
    ctx.lineWidth=2.4; ctx.strokeStyle='rgba(59,43,47,.35)'; ctx.stroke();
  }
  ctx.restore();
}
function drawDecorFar(){
  ctx.save(); ctx.translate(-camX*.18, camY*.25);
  for(const d of decor.far){
    if(d.x - camX*.18 > W+60 || d.x+d.w - camX*.18 < -80) continue;
    const x=d.x, w=d.w, base=GROUND_Y+6, h=(.55+d.h*.75)*(d.kind==='mountains'?200:110);
    ctx.fillStyle=curFar1;
    if(d.kind==='mountains'){
      ctx.beginPath(); ctx.moveTo(x,base);
      ctx.lineTo(x+w*.5, base-h); ctx.lineTo(x+w, base); ctx.closePath(); ctx.fill();
      ctx.fillStyle=curTrim;
      ctx.beginPath(); ctx.moveTo(x+w*.5,base-h);
      ctx.lineTo(x+w*.5+w*.13,base-h+h*.24); ctx.lineTo(x+w*.5,base-h+h*.16);
      ctx.lineTo(x+w*.5-w*.13,base-h+h*.26); ctx.closePath(); ctx.fill();
      ctx.fillStyle=curFar2;
      ctx.beginPath(); ctx.moveTo(x+w*.62,base); ctx.lineTo(x+w*.9,base-h*.52);
      ctx.lineTo(x+w*1.16,base); ctx.closePath(); ctx.fill();
    } else if(d.kind==='hills'){
      ell(x+w*.5,base+8,w*.55,h*.7); ctx.fill();
      ctx.fillStyle=curFar2; ell(x+w*.14,base+8,w*.34,h*.5); ctx.fill();
    } else if(d.kind==='mesa'){
      rr(x,base-h,w,h+16,w*.14); ctx.fill();
      ctx.fillStyle=curFar2; rr(x+w*.62,base-h*.62,w*.5,h*.62+16,w*.12); ctx.fill();
      ctx.fillStyle='rgba(255,255,255,.22)';
      for(let i=0;i<4;i++){ const px=x+18+i*(w-30)/4, py=base-h+16+hash(d.seed+i)*h*.5;
        cir(px,py,3.4); ctx.fill(); }
    } else if(d.kind==='islands'){
      const y0=base-h*1.45;
      ctx.fillStyle=curFar1;
      ell(x+w*.5,y0,w*.5,h*.26); ctx.fill();
      ctx.fillStyle=curFar2;
      ctx.beginPath(); ctx.moveTo(x+w*.16,y0+4); ctx.lineTo(x+w*.5,y0+h*.72);
      ctx.lineTo(x+w*.86,y0+2); ctx.closePath(); ctx.fill();
      ctx.fillStyle=mixc('#5FC79A','#2E8F6E',.5);
      ell(x+w*.32,y0-h*.12,w*.11,h*.16); ctx.fill();
      ell(x+w*.66,y0-h*.2,w*.13,h*.2); ctx.fill();
      ctx.fillStyle='rgba(255,255,255,.35)';
      ctx.fillRect(x+w*.48,y0+h*.1,2.6,h*.6);
    } else if(d.kind==='pines'){
      ell(x+w*.5,base+6,w*.5,h*.16); ctx.fillStyle=curFar1; ctx.fill();
      const n=Math.max(2,Math.round(w/54));
      for(let i=0;i<n;i++){
        const px=x+12+i*(w-24)/n + hash(d.seed+i)*10, ph=h*(.6+hash(d.seed+i*3)*.5), pw=ph*.42;
        ctx.fillStyle=curFar2;
        for(let k=0;k<3;k++){
          const yy=base-h*.12 - k*ph*.28;
          ctx.beginPath(); ctx.moveTo(px-pw*(1-k*.22),yy); ctx.lineTo(px,yy-ph*.52); ctx.lineTo(px+pw*(1-k*.22),yy); ctx.closePath(); ctx.fill();
        }
        ctx.fillStyle=curTrim;
        ctx.beginPath(); ctx.moveTo(px-pw*.22,base-h*.12-ph*.84); ctx.lineTo(px,base-h*1.02);
        ctx.lineTo(px+pw*.22,base-h*.12-ph*.84); ctx.closePath(); ctx.fill();
      }
    } else { /* glowtrees */
      ctx.fillStyle=curFar1;
      for(let i=0;i<3;i++){
        const px=x+i*w/3+8, ph=h*(.7+hash(d.seed+i)*.5);
        ctx.fillRect(px,base-ph,5,ph);
        ell(px+2,base-ph,15,20); ctx.fill();
      }
      ctx.save(); ctx.globalCompositeOperation='lighter';
      for(let i=0;i<7;i++){
        const px=x+hash(d.seed+i)*w, py=base-h*.5-hash(d.seed+i*7)*h*.55;
        const a=.25+.35*Math.sin(t*1.7+i+d.seed);
        ctx.fillStyle='rgba(150,255,220,'+a.toFixed(3)+')'; cir(px,py,3.2); ctx.fill();
      }
      ctx.restore();
    }
  }
  ctx.restore();
}
function drawClouds(){
  const groups={};
  for(const c of decor.clouds){
    const sx=c.x - camX*c.depth;
    if(sx<-180||sx>W+180) continue;
    const a = c.depth<.22?.34: c.depth<.35?.55:.78;
    (groups[a]=groups[a]||[]).push([sx, c.y+camY*c.depth*.6, c]);
  }
  ctx.save(); ctx.fillStyle='#FFFDF4';
  for(const key in groups){
    ctx.globalAlpha=parseFloat(key)*(1-nightAmt*.35);
    ctx.beginPath();
    for(const [sx,sy,c] of groups[key]){
      for(let i=0;i<c.puffs;i++){
        const px=sx + i*c.r*.9 - c.puffs*c.r*.35, py=sy + Math.sin(i*1.7+c.seed)*c.r*.2;
        const pr=c.r*(.65+.42*Math.sin(i*2.1+c.seed));
        ctx.moveTo(px+pr,py); ctx.arc(px,py,Math.abs(pr),0,Math.PI*2);
      }
    }
    ctx.fill();
  }
  ctx.restore();
}
function drawDecorMid(){
  ctx.save(); ctx.translate(-camX*.44, camY*.55);
  for(const d of decor.mid){
    const x=d.x, base=GROUND_Y+4, s=d.h;
    if(x-camX*.44>W+80) continue;
    if(d.kind==='bushes'){
      ctx.fillStyle=curMid1;
      ell(x,base-14*s,26*s,17*s); ctx.fill();
      ctx.fillStyle=curMid2; ell(x+18*s,base-8*s,14*s,10*s); ctx.fill();
      ctx.fillStyle='#FFF0F4';
      for(let i=0;i<3;i++){ const px=x-14+i*15, py=base-24*s+hash(d.seed+i)*8;
        cir(px,py,2.4); ctx.fill(); }
    } else if(d.kind==='trees'){
      ctx.fillStyle='#A97B52'; ctx.fillRect(x-3,base-42*s,6,42*s);
      ctx.fillStyle=curMid2; ell(x,base-52*s,24*s,22*s); ctx.fill();
      ctx.fillStyle=curMid1; ell(x-12*s,base-42*s,15*s,13*s); ctx.fill();
      ell(x+14*s,base-46*s,13*s,12*s); ctx.fill();
    } else if(d.kind==='cactus'){
      ctx.fillStyle=curMid1;
      rr(x-8,base-52*s,16,52*s,8); ctx.fill();
      rr(x-24,base-36*s,12,22*s,6); ctx.fill();
      rr(x+12,base-30*s,12,18*s,6); ctx.fill();
      ctx.fillStyle='#FF9DB4'; cir(x-2,base-56*s,4); ctx.fill();
    } else if(d.kind==='islandsSmall'){
      const y0=base-70*s;
      ctx.fillStyle=curMid2;
      ctx.beginPath(); ctx.moveTo(x-20,y0+6); ctx.lineTo(x,y0+44*s); ctx.lineTo(x+20,y0+4); ctx.closePath(); ctx.fill();
      ctx.fillStyle=curMid1; ell(x,y0,24*s,9*s); ctx.fill();
      ctx.fillStyle='rgba(255,255,255,.4)'; ctx.fillRect(x-2,y0+20,2,26);
    } else if(d.kind==='snowtrees'){
      ctx.fillStyle='#6B4A38'; ctx.fillRect(x-2.4,base-38*s,5,38*s);
      ctx.fillStyle=curMid2;
      for(let k=0;k<3;k++){ const yy=base-14*s-k*13*s;
        ctx.beginPath(); ctx.moveTo(x-16*s*(1-k*.2),yy); ctx.lineTo(x,yy-24*s); ctx.lineTo(x+16*s*(1-k*.2),yy); ctx.closePath(); ctx.fill(); }
      ctx.fillStyle=curMid1;
      for(let k=0;k<3;k++){ const yy=base-16*s-k*13*s;
        ctx.beginPath(); ctx.moveTo(x-13*s*(1-k*.2),yy); ctx.lineTo(x,yy-21*s); ctx.lineTo(x+13*s*(1-k*.2),yy); ctx.closePath(); ctx.fill(); }
    } else if(d.kind==='glowtrees'){
      ctx.fillStyle=curMid2;
      ctx.fillRect(x-3,base-46*s,6,46*s);
      ell(x,base-52*s,18*s,20*s); ctx.fill();
      ctx.save(); ctx.globalCompositeOperation='lighter';
      const a=.3+.3*Math.sin(t*2+d.seed);
      ctx.fillStyle='rgba(160,255,225,'+a.toFixed(3)+')';
      ell(x,base-52*s,11*s,13*s); ctx.fill(); ctx.restore();
    }
  }
  ctx.restore();
}
function drawCritters(){
  for(const c of decor.critters){
    const sx=c.x - camX*.62, sy=c.y + camY*.5;
    if(sx<-60||sx>W+60) continue;
    if(c.type==='balloon'){
      ctx.save(); ctx.translate(sx,sy);
      ctx.strokeStyle='rgba(59,43,47,.4)'; ctx.lineWidth=1.2;
      ctx.beginPath(); ctx.moveTo(0,18); ctx.lineTo(0,34); ctx.stroke();
      const g=ctx.createLinearGradient(-14,-16,14,18);
      g.addColorStop(0,'#FF9DB4'); g.addColorStop(1,'#F2587F');
      ctx.beginPath(); ctx.moveTo(-13,0);
      ctx.quadraticCurveTo(-15,-22,0,-22); ctx.quadraticCurveTo(15,-22,13,0);
      ctx.quadraticCurveTo(6,16,0,18); ctx.quadraticCurveTo(-6,16,-13,0); ctx.closePath();
      ctx.fillStyle=g; ctx.fill(); ctx.lineWidth=2; ctx.strokeStyle='rgba(59,43,47,.7)'; ctx.stroke();
      ctx.fillStyle='#3B2B2F'; rr(-5,16,10,8,2); ctx.fill();
      ctx.restore();
    } else {
      ctx.save(); ctx.strokeStyle='rgba(59,43,47,.5)'; ctx.lineWidth=1.6;
      const f=Math.sin(t*9+c.ph)*c.size;
      ctx.beginPath(); ctx.moveTo(sx-6,sy-f*.5); ctx.lineTo(sx,sy); ctx.lineTo(sx+6,sy-f*.5); ctx.stroke();
      ctx.restore();
    }
  }
}
function drawGround(){
  const y=GROUND_Y;
  /* dirt */
  ctx.fillStyle=curDirt; ctx.fillRect(-20,y,W+40,H-y+20);
  /* grass band */
  ctx.fillStyle=curGround; ctx.fillRect(-20,y-4,W+40,26);
  /* scallop top */
  ctx.beginPath(); ctx.moveTo(-20,y-2);
  for(let x=-20;x<W+40;x+=14) ctx.arc(x,y-2,7,Math.PI,0);
  ctx.lineTo(W+20,y+26); ctx.lineTo(-20,y+26); ctx.closePath();
  ctx.fillStyle=curGround; ctx.fill();
  ctx.lineWidth=2.6; ctx.strokeStyle='rgba(59,43,47,.32)';
  ctx.beginPath(); ctx.moveTo(-20,y-2);
  for(let x=-20;x<W+40;x+=14) ctx.arc(x,y-2,7,Math.PI,0);
  ctx.stroke();
  /* pebbles + flowers on the ground */
  const start=Math.floor((camX-40)/24);
  for(let i=start;i<start+30;i++){
    const gx=i*24 - camX + 10, h1=hash(i*3.7), h2=hash(i*7.3);
    if(h1>.72){ ctx.fillStyle='rgba(59,43,47,.22)'; ell(gx,y+14+h2*20,4+h2*4,2.4); ctx.fill(); }
    if(h1<.14){ /* tiny flower */
      const fy=y+8+h2*10;
      ctx.fillStyle=h2>.5?'#FFF':'#FFD9E4';
      for(let p=0;p<5;p++){ const a=p*1.256+t*.2; cir(gx+Math.cos(a)*3.2,fy+Math.sin(a)*3.2,2.1); ctx.fill(); }
      ctx.fillStyle='#FFD25E'; cir(gx,fy,1.8); ctx.fill();
    }
    if(h1>.93){ /* mushroom */
      const fy=y+12;
      ctx.fillStyle='#FFF6E4'; rr(gx-2.4,fy-8,5,9,2); ctx.fill();
      ctx.fillStyle='#F2587F'; ell(gx,fy-9,7,5); ctx.fill();
      ctx.fillStyle='#FFF'; cir(gx-3,fy-10,1.5); ctx.fill(); cir(gx+3,fy-9,1.3); ctx.fill();
    }
  }
}
function drawTufts(front){
  for(const d of decor.tufts){
    const sx=d.x - camX*(front?1.16:1), sy=GROUND_Y - (front? 4:2);
    if(sx<-30||sx>W+30) continue;
    ctx.save();
    if(front) ctx.globalAlpha=.9;
    if(d.kind==='grass'){
      ctx.strokeStyle=front?'rgba(47,120,96,.75)':'rgba(47,120,96,.55)'; ctx.lineWidth=2;
      const h=13*d.h, sway=Math.sin(t*1.4+d.seed)*3;
      for(let k=-2;k<=2;k++){ ctx.beginPath(); ctx.moveTo(sx+k*3,sy);
        ctx.quadraticCurveTo(sx+k*4+sway*.5,sy-h*.6,sx+k*3+sway+ k*2, sy-h*(1-Math.abs(k)*.16)); ctx.stroke(); }
    } else if(d.kind==='flower'){
      const h=16*d.h, sway=Math.sin(t*1.6+d.seed)*4;
      ctx.strokeStyle='rgba(47,120,96,.6)'; ctx.lineWidth=1.8;
      ctx.beginPath(); ctx.moveTo(sx,sy); ctx.quadraticCurveTo(sx+sway,sy-h*.6,sx+sway,sy-h); ctx.stroke();
      ctx.fillStyle='#FFF';
      for(let p=0;p<6;p++){ const a=p*1.047+t*.15; cir(sx+sway+Math.cos(a)*4,sy-h+Math.sin(a)*4,2.6); ctx.fill(); }
      ctx.fillStyle='#FFD25E'; cir(sx+sway,sy-h,2.4); ctx.fill();
    } else if(d.kind==='reeds'){
      ctx.strokeStyle='rgba(180,140,90,.6)'; ctx.lineWidth=2.2;
      for(let k=-1;k<2;k++){ const h=20*d.h, sway=Math.sin(t*1.2+d.seed+k)*5;
        ctx.beginPath(); ctx.moveTo(sx+k*6,sy); ctx.quadraticCurveTo(sx+k*6+sway,sy-h*.6,sx+k*6+sway,sy-h); ctx.stroke();
        ctx.fillStyle='rgba(150,110,70,.7)'; ell(sx+k*6+sway,sy-h-3,2.2,5); ctx.fill(); }
    } else {
      ctx.fillStyle='rgba(59,43,47,.2)';
      ell(sx,sy+4,7*d.h,3.4); ctx.fill();
    }
    ctx.restore();
  }
}
function pipeColors(){
  return starT>0? ['#FFE9A8','#FFB24A','#FFF8E2'] : [curP1,curP2,curTrim];
}
function drawPipeSeg(x,y,w,h,flip){
  const [c1,c2,trim]=pipeColors();
  const g=ctx.createLinearGradient(x,0,x+w,0);
  g.addColorStop(0,mixc('#000000',c1,.06)); g.addColorStop(.28,c1);
  g.addColorStop(.52,mixc('#ffffff',c1,.4)); g.addColorStop(.78,c1); g.addColorStop(1,mixc('#000000',c2,.28));
  ctx.fillStyle=g; ctx.fillRect(x,y,w,h);
  /* outline + inner shade */
  ctx.lineWidth=2.6; ctx.strokeStyle='rgba(59,43,47,.5)';
  ctx.strokeRect(x,y,w,h);
  /* pattern */
  ctx.save(); ctx.beginPath(); ctx.rect(x,y,w,h); ctx.clip();
  const v=keyB('pipe',biomeFloat);
  ctx.globalAlpha=.5;
  if(v==='candy'){
    ctx.strokeStyle=curP2; ctx.lineWidth=7;
    for(let i=-1;i<10;i++){ ctx.beginPath(); ctx.moveTo(x+i*16,y+(flip?h:0)); ctx.lineTo(x+i*16+16,y+(flip?h:0)+(flip?-16:16)); ctx.stroke(); }
  } else if(v==='bamboo'){
    ctx.fillStyle='rgba(255,255,255,.45)';
    for(let i=1;i<5;i++){ const yy=y+h*i/5; ctx.fillRect(x,yy-2,w,3); }
  } else if(v==='coral'){
    ctx.fillStyle='rgba(59,43,47,.22)';
    for(let i=0;i<12;i++){ const px=x+6+hash(i)*10, py=y+hash(i*3.3)*h, r=2+hash(i*7)*4.4;
      cir(px,py,r); ctx.fill(); }
  } else if(v==='crystal'){
    ctx.strokeStyle='rgba(255,255,255,.7)'; ctx.lineWidth=2;
    for(let i=0;i<5;i++){ const yy=y+h*i/5;
      ctx.beginPath(); ctx.moveTo(x+3,yy); ctx.lineTo(x+w*.45,yy+ (flip?-12:12)); ctx.lineTo(x+w-3,yy-4); ctx.stroke(); }
  } else if(v==='icicle'){
    ctx.fillStyle='rgba(255,255,255,.6)';
    for(let i=0;i<6;i++){ const px=x+4+i*(w/6), ph=8+hash(i*2.2)*16;
      const yy= flip? y : y+h;
      ctx.beginPath(); ctx.moveTo(px,yy); ctx.lineTo(px+5,yy); ctx.lineTo(px+2.5,yy+(flip?ph:-ph)); ctx.closePath(); ctx.fill(); }
  } else {
    ctx.save(); ctx.globalCompositeOperation='lighter';
    ctx.fillStyle='rgba(150,255,225,.5)';
    for(let i=0;i<9;i++){ const px=x+6+hash(i)*10, py=y+hash(i*4.1)*h;
      const a=.3+.35*Math.sin(t*2.4+i); ctx.globalAlpha=a; cir(px,py,3); ctx.fill(); }
    ctx.restore();
  }
  ctx.restore();
  /* rim (the "lips") */
  const rimH=24, ry=flip? y : y+h-rimH;
  const rg=ctx.createLinearGradient(x-7,0,x+w+7,0);
  rg.addColorStop(0,mixc('#000000',c2,.3)); rg.addColorStop(.3,c1);
  rg.addColorStop(.55,mixc('#ffffff',c1,.55)); rg.addColorStop(1,mixc('#000000',c2,.34));
  rr(x-7,ry,w+14,rimH,10); ctx.fillStyle=rg; ctx.fill();
  ctx.lineWidth=2.8; ctx.strokeStyle='rgba(59,43,47,.72)'; ctx.stroke();
  rr(x-4,ry+3.4,w+8,6,4); ctx.fillStyle='rgba(255,255,255,.5)'; ctx.fill();
  /* face on rim */
  return {ry,rimH};
}
function drawPipes(){
  const [c1,c2,trim]=pipeColors();
  for(const p of pipes){
    if(p.dead) continue;
    const sx=p.x, cy=p.base+Math.sin(p.phase)*p.bobA, half=p.hole/2;
    if(sx>W+60||sx+p.w<-60) continue;
    const topH=cy-half, botY=cy+half, botH=GROUND_Y-botY;
    /* body shadow */
    ctx.fillStyle='rgba(59,43,47,.13)'; ctx.fillRect(sx+6,-140,p.w,topH+140); ctx.fillRect(sx+6,botY,p.w,botH);
    drawPipeSeg(sx,-140,p.w,topH+140,false);
    drawPipeSeg(sx,botY,p.w,botH,true);
    /* decorations on rim */
    if(p.sprout){
      const dy = topH-8, dy2 = botY+30;
      for(const [px,py] of [[sx+8,dy],[sx+p.w-6,dy2]]){
        if(p.sprout==='flower'){
          ctx.fillStyle='rgba(47,120,96,.8)';
          ctx.beginPath(); ctx.ellipse(px,py+6,3,7,Math.sin(t+px)*.2,0,7); ctx.fill();
          ctx.fillStyle='#FFF0F4';
          for(let k=0;k<5;k++){ const a=k*1.256+t*.25; cir(px+Math.cos(a)*4,py-2+Math.sin(a)*4,3); ctx.fill(); }
          ctx.fillStyle='#FFD25E'; cir(px,py-2,2.4); ctx.fill();
        } else {
          ctx.fillStyle='rgba(70,180,140,.85)';
          ctx.save(); ctx.translate(px,py+4); ctx.rotate(Math.sin(t*.9+px)*.25+.5);
          ctx.beginPath(); ctx.ellipse(0,-6,4,9,0,0,7); ctx.fill(); ctx.restore();
        }
      }
    }
    if(p.bug && topH>40){
      const bx=sx+p.w*.62, by=topH-14+Math.sin(t*1.4+p.phase)*3;
      ctx.fillStyle='#F2587F'; ell(bx,by,5,4.2); ctx.fill();
      ctx.fillStyle='#2C2024'; cir(bx-2.4,by-.6,1.5); ctx.fill();
      ctx.beginPath(); ctx.moveTo(bx,by-4); ctx.lineTo(bx,by+4); ctx.lineWidth=1; ctx.strokeStyle='#2C2024'; ctx.stroke();
    }
    if(p.bobA>0){ /* fins for moving pipes */
      ctx.fillStyle='rgba(255,255,255,.5)';
      for(const yy of [cy-half+12,cy+half-12]){
        ctx.beginPath(); ctx.moveTo(sx-3,yy); ctx.lineTo(sx-13,yy+5); ctx.lineTo(sx-3,yy+10); ctx.closePath(); ctx.fill();
        ctx.beginPath(); ctx.moveTo(sx+p.w+3,yy); ctx.lineTo(sx+p.w+13,yy+5); ctx.lineTo(sx+p.w+3,yy+10); ctx.closePath(); ctx.fill();
      }
    }
  }
  /* gap guide near the next pipe */
  const nx=pipes.find(p=>!p.dead && p.x+p.w>bird.x);
  if(nx && state==='play'){
    const cy=nx.base+Math.sin(nx.phase)*nx.bobA, half=nx.hole/2;
    const gx=nx.x+nx.w/2-camX%0;
    ctx.save(); ctx.globalAlpha=.35+.25*Math.sin(t*3);
    for(let i=0;i<3;i++){ const px=Math.max(60, nx.x-22-i*13), py=cy+ (Math.abs(bird.y-cy)>half? (bird.y<cy?-1:1)*half*.6 : 0);
      ctx.fillStyle='#FFF6DA'; star(px,py,3,4.6,2,-Math.PI/2+t*.6); ctx.fill(); }
    ctx.restore();
  }
}
function drawPickups(){
  for(const k of pickups){
    if(k.taken) continue;
    const sx=k.x, sy=k.y + Math.sin(t*2+k.t)*4;
    if(sx<-40||sx>W+40) continue;
    if(k.type==='cherry') paintCherry(ctx,sx,sy,k.r,Math.sin(t*1.2+k.t)*.28);
    else if(k.type==='star'){
      ctx.save(); ctx.globalCompositeOperation='lighter';
      const g=ctx.createRadialGradient(sx,sy,1,sx,sy,26);
      g.addColorStop(0,'rgba(255,240,170,.6)'); g.addColorStop(1,'rgba(255,240,170,0)');
      ctx.fillStyle=g; cir(sx,sy,26); ctx.fill(); ctx.restore();
      paintStar(ctx,sx,sy,k.r,t*1.6);
    } else {
      paintBird(ctx,sx,sy,0,skin(),'none',0,false,0,true);
      ctx.fillStyle='rgba(255,255,255,.7)'; cir(sx-4,sy-6,2.4); ctx.fill();
    }
  }
}
function drawSkyParticles(front){
  ctx.save();
  for(const s of skyParticles){
    if(s.front!==front) continue;
    const sx=s.x-camX*(s.front?1:.72), life=s.age/s.life, a=(1-life)*.9;
    ctx.globalAlpha=Math.max(0,a)*.9;
    ctx.fillStyle=s.c;
    if(s.type==='snow'){ cir(sx,s.y+camY*.7,s.r); ctx.fill(); }
    else if(s.type==='petal'){
      ctx.save(); ctx.translate(sx,s.y+camY*.7); ctx.rotate(s.spin*s.age*3);
      ctx.beginPath(); ctx.ellipse(0,0,s.r*1.5,s.r*.75,0,0,7); ctx.fill(); ctx.restore();
    }
    else if(s.type==='fluff'){
      ctx.beginPath();
      for(let i=0;i<6;i++){ const a=i*1.047, px=sx+Math.cos(a)*s.r*.8, py=s.y+camY*.7+Math.sin(a)*s.r*.8;
        ctx.moveTo(px+2.2,py); ctx.arc(px,py,2.2,0,Math.PI*2); }
      ctx.fill();
    }
    else if(s.type==='leaf'){
      ctx.save(); ctx.translate(sx,s.y+camY*.7); ctx.rotate(s.spin*s.age*2.2);
      ctx.beginPath(); ctx.ellipse(0,0,s.r*1.8,s.r*.85,0,0,7); ctx.fill();
      ctx.restore();
    }
    else if(s.type==='star'){ paintStar(ctx,sx,s.y+camY*.7,s.r*.85,s.age*2); }
    else { ctx.save(); ctx.globalCompositeOperation='lighter'; cir(sx,s.y+camY*.7,s.r); ctx.fill(); ctx.restore(); }
  }
  ctx.restore();
}
function drawFireflies(){
  if(nightAmt<.5) return;
  if(fireflies.length<24 && Math.random()<.1){
    fireflies.push({x:camX+rnd(0,W),y:rnd(120,GROUND_Y-20),ph:Math.random()*6,sp:rnd(.6,1.5),age:0,life:rnd(3,6)});
  }
  ctx.save(); ctx.globalCompositeOperation='lighter';
  for(const f of fireflies){
    f.age+=.016; f.x += Math.cos(t*f.sp+f.ph)*22 - 12; f.y += Math.sin(t*f.sp*1.3+f.ph)*16;
    const sx=f.x-camX; if(sx<-20||sx>W+20) continue;
    const a=.35+.45*Math.sin(t*4+f.ph);
    ctx.fillStyle='rgba(255,240,150,'+Math.max(0,a).toFixed(3)+')';
    cir(sx,f.y,2.6); ctx.fill();
    ctx.fillStyle='rgba(255,240,150,'+(a*.22).toFixed(3)+')'; cir(sx,f.y,9); ctx.fill();
  }
  ctx.restore();
  fireflies=fireflies.filter(f=>f.age<f.life);
}
function drawTrail(){
  if(state!=='play' && state!=='ready') return;
  ctx.save();
  if(starT>0) ctx.globalCompositeOperation='lighter';
  for(let i=0;i<trail.length;i++){
    const tr=trail[i], f=i/trail.length;
    ctx.globalAlpha=f*.42;
    if(starT>0){
      const hue=(t*180+i*24)%360;
      ctx.fillStyle='hsla('+hue+',95%,66%,.9)';
    } else ctx.fillStyle='rgba(255,255,255,.7)';
    cir(tr.x - (1-f)*10, tr.y, tr.r*f*.9); ctx.fill();
  }
  ctx.restore();
}
function drawParticles(front){
  ctx.save();
  for(const p of parts){
    if(p.front!==front) continue;
    const k=p.age/p.life, a=Math.pow(1-k,p.fade||1);
    ctx.globalAlpha=Math.max(0,a);
    if(p.type==='ring'){
      ctx.strokeStyle=p.c; ctx.lineWidth=3*a+1;
      cir(p.x,p.y,lerp(p.r,p.r2,k)); ctx.stroke();
    } else if(p.type==='feather'){
      ctx.save(); ctx.translate(p.x,p.y); ctx.rotate(p.rot);
      ctx.fillStyle=p.c; ctx.beginPath(); ctx.ellipse(0,0,p.r*1.7,p.r*.75,0,0,7); ctx.fill(); ctx.restore();
    } else {
      ctx.fillStyle=p.c; cir(p.x,p.y,p.r*(1-k*.45)); ctx.fill();
    }
  }
  ctx.restore();
}
function drawBird(){
  const sk=skin(), h=hat();
  /* shadow on ground */
  if(bird.y<GROUND_Y-20){
    const f=clamp(1-(GROUND_Y-bird.y)/380,.12,1);
    ctx.save(); ctx.globalAlpha=.2*f;
    ell(bird.x+8,GROUND_Y+2,18*f,5*f); ctx.fillStyle='#3B2B2F'; ctx.fill(); ctx.restore();
  }
  ctx.save();
  ctx.translate(bird.x, bird.y);
  ctx.rotate(bird.ang);
  ctx.scale(bird.sx,bird.sy);
  const blink=bird.blink>0;
  if(bird.inv>0 && !bird.dead && Math.floor(t*14)%2===0) ctx.globalAlpha=.45;
  paintBird(ctx,0,0,17,sk,h.id,0,blink,bird.wing,bird.shield>0);
  if(bird.dead){
    ctx.save(); ctx.translate(0,-2);
    ctx.strokeStyle='rgba(59,43,47,.85)'; ctx.lineWidth=2.2;
    ctx.beginPath(); ctx.moveTo(-6,-8); ctx.lineTo(0,-2); ctx.moveTo(0,-8); ctx.lineTo(-6,-2); ctx.stroke();
    ctx.restore();
  }
  ctx.restore();
  if(starT>0){
    ctx.save(); ctx.globalCompositeOperation='lighter';
    const g=ctx.createRadialGradient(bird.x,bird.y,4,bird.x,bird.y,44);
    g.addColorStop(0,'rgba(255,235,160,.55)'); g.addColorStop(1,'rgba(255,200,120,0)');
    ctx.fillStyle=g; cir(bird.x,bird.y,44); ctx.fill(); ctx.restore();
  }
}
function drawTexts(){
  for(const tx of texts){
    const k=tx.age/tx.life;
    ctx.save(); ctx.globalAlpha=1-k*k;
    inkText(tx.s,tx.x,tx.y,tx.size*(1+ (1-k)*.06),tx.c,'center',3.4);
    ctx.restore();
  }
}
function drawHUD(){
  if(state==='over') return;
  const x=W/2, y=52;
  const s=String(score).padStart(2,'0');
  ctx.save();
  ctx.globalAlpha=.28; inkText(s,x,y+7,40,'#3B2B2F','center',9); ctx.restore();
  inkText(s,x,y,40,'#FFF6E0');
  if(starT>0){
    const w=Math.min(160,(starT/5)*160);
    ctx.save(); ctx.globalAlpha=.9;
    rr(W/2-80,78,160,10,5); ctx.fillStyle='rgba(59,43,47,.35)'; ctx.fill();
    rr(W/2-80,78,w,10,5);
    const g=ctx.createLinearGradient(W/2-80,0,W/2+80,0);
    g.addColorStop(0,'#FFE58A'); g.addColorStop(.5,'#FF9DB4'); g.addColorStop(1,'#8FE3C2');
    ctx.fillStyle=g; ctx.fill(); ctx.restore();
    inkText('★ STAR POWER ★',W/2,100,13,'#FFF3D6');
  }
  if(combo>2 && comboT>0) inkText('yummy ×'+combo, W/2, starT>0?118:78, 12, '#FFD9E4');
}
function drawVignette(){
  const g=ctx.createRadialGradient(W/2,H*.46,H*.32,W/2,H*.5,H*.85);
  g.addColorStop(0,'rgba(0,0,0,0)'); g.addColorStop(1,'rgba(40,22,28,.34)');
  ctx.fillStyle=g; ctx.fillRect(0,0,W,H);
  if(flash>0){ ctx.fillStyle='rgba(255,250,235,'+(flash*.8).toFixed(3)+')'; ctx.fillRect(0,0,W,H); }
}

/* ---------- main render ---------- */
function render(){
  refreshColors();
  ctx.save();
  ctx.translate(shakeX, shakeY);
  drawSky();
  drawSunMoon();
  drawDecorFar();
  drawClouds();
  drawDecorMid();
  drawCritters();
  drawSkyParticles(false);
  drawPipes();
  drawPickups();
  drawGround();
  drawTufts(false);
  drawFireflies();
  drawTrail();
  drawParticles(false);
  drawBird();
  drawParticles(true);
  drawSkyParticles(true);
  drawTufts(true);
  drawTexts();
  drawHUD();
  drawVignette();
  ctx.restore();
  /* state flourishes drawn above everything */
  if(state==='ready') drawReadyCue();
  if(state==='paused') drawPausedBars();
}
function drawReadyCue(){
  const cy=360;
  ctx.save(); ctx.globalAlpha=.55;
  ctx.fillStyle='rgba(255,246,224,.75)';
  for(let i=0;i<3;i++){ const px=300+i*20+Math.sin(t*3)*4;
    star(px,cy+Math.sin(t*2+i)*6,3,6,2.6,t); ctx.fill(); }
  ctx.restore();
  inkText('go!',W/2,150,26,'#FFF6E0');
}
function drawPausedBars(){
  ctx.save(); ctx.globalAlpha=.2; ctx.fillStyle='#3B2B2F';
  for(let y=-20;y<H;y+=8) ctx.fillRect(0,y,W,3);
  ctx.restore();
}

/* ============================================================================
   10 · GAME OVER / MEDALS
   ========================================================================== */
const MEDALS=[
  {min:0,name:'Feather duster',m1:'#f2e6cf',m2:'#cbb894',star:'✧',sub:'The sky forgives you.'},
  {min:5,name:'Paper plane',m1:'#e8f2f7',m2:'#a9c0cd',star:'✦',sub:'Five pipes! That counts.'},
  {min:12,name:'Bronoculus',m1:'#f0c39a',m2:'#b87c48',star:'★',sub:'Warm and sturdy.'},
  {min:25,name:'Silver linings',m1:'#e6ecf2',m2:'#9dabbd',star:'★',sub:'Shiny. Suspiciously shiny.'},
  {min:45,name:'Goldfish (bird)',m1:'#ffe3a0',m2:'#e0a83f',star:'★',sub:'Certified sky gremlin.'},
  {min:70,name:'Sparkle royalty',m1:'#fff0c2',m2:'#ff9db4',star:'✺',sub:'The pipes fear you now.'}
];
function gameOver(){
  state='over';
  const ch=runCherries;
  const newBest = score>save_.best;
  if(newBest){ save_.best=score; }
  save_.cherries=(save_.cherries||0)+ch;
  save();
  const m=[...MEDALS].reverse().find(x=>score>=x.min);
  const medalEl=document.getElementById('ovMedal');
  medalEl.style.setProperty('--m1',m.m1); medalEl.style.setProperty('--m2',m.m2);
  document.getElementById('ovStar').textContent=m.star;
  document.getElementById('ovTitle').textContent=m.name;
  document.getElementById('ovSub').textContent = m.sub+' You flew '+Math.round(dist/10)+' m through '+
    (biomeFloat>1? Math.floor(biomeFloat)+1+' skies':'one sky')+'. '+
    (pipesPassed>4? 'Ducked '+pipesPassed+' pipes.' : 'Pipes remained undefeated.');
  document.getElementById('ovKicker').textContent = score>=45?'absolute legend':score>=20?'lovely flying':'again, again';
  document.getElementById('ovCh').textContent=ch;
  document.getElementById('newBest').classList.toggle('show',newBest);
  countUp(document.getElementById('ovScore'),score,700);
  document.getElementById('ovBest').textContent=save_.best;
  setTimeout(()=>medalEl.classList.add('earned'),60);
  /* unlock checks */
  const nowUnlocked=[...HATS,...SKINS].filter(x=>x.need>0 && x.need<=save_.best && x.need>prevBestNeed);
  prevBestNeed=save_.best;
  if(nowUnlocked.length) toast('unlocked: '+nowUnlocked.map(u=>u.name).join(' · '),'cool');
  const lr=document.getElementById('lockRow');
  const lk=[...HATS,...SKINS].filter(x=>x.need>save_.best);
  lr.textContent = nowUnlocked.length
    ? 'New: '+nowUnlocked.map(u=>u.name).join(', ')+' — pick it in the rack!'
    : (lk.length? 'Next unlock: '+lk[lk.length-1].name+' at '+(lk[lk.length-1].id==='none'?5:lk[lk.length-1].need)+' score' : 'Everything unlocked. Incredible.');
  show('panelOver',true);
  buildChips();
  updateHUD();
  SND.ui();
}
let prevBestNeed=save_.best;
function countUp(el,to,dur){
  const t0=performance.now();
  (function step(now){
    const k=Math.min(1,(now-t0)/dur);
    el.textContent=Math.round(to*(1-Math.pow(1-k,3)));
    if(k<1) requestAnimationFrame(step);
  })(t0);
}
function show(id,on){ document.getElementById(id).classList.toggle('show',on); }
function hidePanels(){ ['panelStart','panelOver','panelPause'].forEach(i=>show(i,false)); }

/* ============================================================================
   11 · UI WIRING
   ========================================================================== */
const toastsEl=document.getElementById('toasts');
function toast(text,cls){
  const d=document.createElement('div');
  d.className='toast'+(cls?' '+cls:''); d.textContent=text;
  toastsEl.prepend(d);
  while(toastsEl.children.length>3) toastsEl.lastChild.remove();
  setTimeout(()=>{ d.classList.add('out'); setTimeout(()=>d.remove(),520); },2200);
}
function updateHUD(){
  const hs=document.getElementById('hudScore');
  if(hs.textContent!==String(score)){ hs.textContent=score; hs.classList.remove('pop'); void hs.offsetWidth; hs.classList.add('pop'); }
  document.getElementById('hudBest').textContent=save_.best;
  document.getElementById('hudCherries').textContent=(save_.cherries||0)+(state==='play'||state==='dying'?0:0);
  const pct=Math.min(1,meter/METER_NEED)*100;
  document.getElementById('meterFill').style.width=pct+'%';
  document.getElementById('meterTrack').classList.toggle('full',meter>=METER_NEED);
  document.getElementById('meterLabel').textContent= starT>0? 'STAR POWER!' : meter+'/'+METER_NEED;
}
/* logo letters */
(function logo(){
  const el=document.getElementById('logo'), word='LITTLE SKY';
  const cols=['#FF8FAB','#FFB84A','#4FC3A0','#7BB3F5','#FF8FAB','#FFB84A','#4FC3A0','#7BB3F5','#FF8FAB','#FFB84A','#4FC3A0'];
  let i=0;
  el.innerHTML=[...word].map(ch=>{
    if(ch===' ') return '<span style="width:.4em">&nbsp;</span>';
    const c=cols[i%cols.length], r=(i%2?1:-1)*rnd(1,4).toFixed(1);
    return '<span style="--c:'+c+';--r:'+r+'deg;--i:'+i+'">'+ch+'</span>';
  }).join('');
})();
/* lamps */
document.getElementById('lamps').innerHTML='<i></i>'.repeat(14);
/* twinkles */
(function twinkles(){
  const wrap=document.getElementById('twks');
  for(let i=0;i<26;i++){
    const d=document.createElement('div'); d.className='twk';
    d.style.left=(Math.random()*100)+'vw'; d.style.top=(Math.random()*70)+'vh';
    d.style.animationDuration=(1.6+Math.random()*2.6)+'s';
    d.style.animationDelay=(-Math.random()*4)+'s';
    d.style.transform='scale('+(.6+Math.random()*1.5)+')';
    wrap.appendChild(d);
  }
})();
/* ticker */
(function ticker(){
  const words=['flap','glide','munch a cherry','dodge the candy pipes','take the high road',
    'you are a small bird with a big sky','bubble up','streak like a shooting mochi','do a loop',
    'be soft, be fast','one more try','the moon is watching'];
  const html=words.map(w=>'<span>'+w+'</span><b style="color:#ff8fab">✦</b>').join('');
  document.getElementById('tickerRun').innerHTML=html+html;
})();
/* mode chips */
function buildChips(){
  const mc=document.getElementById('modeChips');
  mc.innerHTML='';
  Object.keys(CFG).forEach((k,i)=>{
    const b=document.createElement('button'); b.className='chip'; b.style.flex='1 1 auto';
    b.setAttribute('aria-pressed', save_.mode===k);
    b.innerHTML='<span class="txt">'+CFG[k].name+'<small>'+(k==='cozy'?'wide gaps':k==='classic'?'the real thing':'fast + moving pipes')+'</small></span>';
    b.onclick=()=>{ setMode(k); };
    mc.appendChild(b);
  });
  /* hats */
  const hc=document.getElementById('hatChips'); hc.innerHTML='';
  HATS.forEach(h=>{
    const un=save_.best>=h.need, sel=save_.hat===h.id;
    const b=document.createElement('button'); b.className='chip'+(un?'':' locked');
    b.setAttribute('aria-pressed',sel);
    const cv=document.createElement('canvas'); cv.width=60; cv.height=60; cv.style.width='30px'; cv.style.height='30px';
    const c2=cv.getContext('2d');
    paintBird(c2,15,17,11,skin(),h.id,-.12,false,-1.2,false);
    b.appendChild(cv);
    const sp=document.createElement('span'); sp.className='txt';
    sp.innerHTML=h.name+(un?'':'<small>🔒 '+h.need+' score</small>');
    b.appendChild(sp);
    b.onclick=()=>{ if(!un){ SND.uiNo(); toast('locked · need '+h.need+' score','hot'); return; }
      save_.hat=h.id; save(); buildChips(); SND.ui(); };
    hc.appendChild(b);
  });
  /* skins */
  const sc=document.getElementById('skinChips'); sc.innerHTML='';
  SKINS.forEach(s=>{
    const un=save_.best>=s.need, sel=save_.skin===s.id;
    const b=document.createElement('button'); b.className='chip'+(un?'':' locked');
    b.setAttribute('aria-pressed',sel);
    const cv=document.createElement('canvas'); cv.width=60; cv.height=60; cv.style.width='30px'; cv.style.height='30px';
    paintBird(cv.getContext('2d'),15,17,11,s,'none',-.1,false,-1.2,false);
    b.appendChild(cv);
    const sp=document.createElement('span'); sp.className='txt';
    sp.innerHTML=s.name+(un?'':'<small>🔒 '+s.need+'</small>');
    b.appendChild(sp);
    b.onclick=()=>{ if(!un){ SND.uiNo(); toast('locked · need '+s.need+' score','hot'); return; }
      save_.skin=s.id; save(); buildChips(); SND.ui(); };
    sc.appendChild(b);
  });
}
function setMode(k){
  save_.mode=k; cfg=CFG[k]; save();
  document.querySelectorAll('#modeChips .chip').forEach((c,i)=>c.setAttribute('aria-pressed',Object.keys(CFG)[i]===k));
  toast('mode: '+cfg.name,'cool'); SND.ui();
}
/* scroll reveal */
(function reveal(){
  const io=new IntersectionObserver(es=>es.forEach(e=>{ if(e.isIntersecting){ e.target.classList.add('in'); io.unobserve(e.target); } }),{threshold:.05});
  document.querySelectorAll('.card').forEach((c,i)=>{ c.style.transitionDelay=(i*35)+'ms'; io.observe(c); });
})();
/* tips rotation */
(function tips(){
  const el=document.getElementById('tips');
  const list=[
    'Long taps don\'t help — <em>short flaps</em> keep you level.',
    'Cherries fill the <em>sparkle meter</em>. Six of them = star power, which smashes pipes.',
    'Skim a pipe without touching it for a <em>+2 close call</em>.',
    'Bubbles soak up one mistake. Save them for the tight gaps.',
    'The sky changes every 2700 m. Frostbite makes pipes slippery.',
    'Cozy mode is not cheating. It is self-care.',
    'Golden stars extend star power and pay double.',
    'Unlock hats with score: 5 · 12 · 25 · 45 · 70.',
    'Star power doubles every point you earn. Chase cherries first.',
    'Ceiling bumps cost you speed. Fly the gap, not the roof.'
  ];
  let i=Math.floor(Math.random()*list.length);
  function next(){ el.classList.add('fade');
    setTimeout(()=>{ el.innerHTML=list[i]; i=(i+1)%list.length; el.classList.remove('fade'); },450); }
  next(); setInterval(next,7200);
})();

/* ============================================================================
   12 · INPUT
   ========================================================================== */
function pressFlap(){
  SND.init(); SND.wake();
  flap();
}
screenEl.addEventListener('pointerdown',e=>{ e.preventDefault(); pressFlap(); });
addEventListener('keydown',e=>{
  if(e.repeat) return;
  const k=e.code;
  if(k==='Space'||k==='ArrowUp'||k==='KeyW'||k==='Enter'){ e.preventDefault(); pressFlap(); }
  else if(k==='KeyP'||k==='Escape'){ togglePause(); }
  else if(k==='KeyR'){ if(state!=='menu'){ toMenu(); toast('fresh sky ✧','cool'); } }
  else if(k==='KeyM'){ toggleSound(); }
  else if(k==='Digit1'){ setMode('cozy'); }
  else if(k==='Digit2'){ setMode('classic'); }
  else if(k==='Digit3'){ setMode('spicy'); }
  else if(k==='KeyH'){ cycleHat(); }
});
function cycleHat(){
  const av=HATS.filter(h=>save_.best>=h.need);
  const i=av.findIndex(h=>h.id===save_.hat);
  save_.hat=av[(i+1)%av.length].id; save(); buildChips(); SND.ui();
  toast('hat: '+hat().name,'cool');
}
function toggleSound(){
  SND.sfx=false; SND.music=false; save_.sfx=false; save_.music=false; save();
  document.getElementById('chipSfx').setAttribute('aria-pressed','false');
  document.getElementById('chipMusic').setAttribute('aria-pressed','false');
  toast('shhh…','cool');
}
document.getElementById('chipMusic').onclick=function(){
  SND.init(); SND.wake();
  SND.music=!SND.music; save_.music=SND.music; save();
  this.setAttribute('aria-pressed',SND.music);
  if(SND.music){ SND.seq.next=SND.t()+.05; SND.chime(); }
};
document.getElementById('chipSfx').onclick=function(){
  SND.init(); SND.wake();
  SND.sfx=!SND.sfx; save_.sfx=SND.sfx; save();
  this.setAttribute('aria-pressed',SND.sfx); SND.pop();
};
document.getElementById('btnAgain').onclick=e=>{ e.stopPropagation(); pressFlap(); };
document.getElementById('btnMenu').onclick=e=>{ e.stopPropagation(); toMenu(); SND.ui(); };
document.getElementById('btnResume').onclick=e=>{ e.stopPropagation(); togglePause(); };
document.getElementById('btnQuit').onclick=e=>{ e.stopPropagation(); toMenu(); };
document.querySelectorAll('.chip').forEach(c=>c.addEventListener('pointerdown',e=>e.stopPropagation()));
addEventListener('blur',()=>{ if(state==='play') togglePause(); });
document.addEventListener('visibilitychange',()=>{ if(document.hidden && state==='play') togglePause(); });

/* ============================================================================
   13 · LOOP
   ========================================================================== */
let dtGlobal=1/60;
function loop(now){
  let dt=(now-(loop.last||now))/1000; loop.last=now;
  dtGlobal=Math.min(dt,.04);
  update(Math.min(dt,1/24));
  render();
  requestAnimationFrame(loop);
}
/* boot */
const ro=new ResizeObserver(()=>fitCanvas());
ro.observe(screenEl);
addEventListener('resize',fitCanvas);
reset();
fitCanvas();
setTimeout(fitCanvas,60); setTimeout(fitCanvas,360);
buildChips(); updateHUD();
SND.seq.next=0;
requestAnimationFrame(loop);
/* audio needs a gesture — kick it quietly on first interaction */
['pointerdown','keydown'].forEach(ev=>addEventListener(ev,function once(){
  SND.init(); SND.wake(); SND.seq.next=SND.t()+.05;
  removeEventListener(ev,once);
},{once:true,capture:true}));
</script>
</body>
</html>
```

**What's inside, briefly:**

- **Six hand-painted skies** (Peachrise → Mintnoon → Coralset → Dusksea → Frostbite → Auroramilk) that cross-fade continuously — each one swaps the pipe material (candy stripes, bamboo, coral, crystal, icicle, glowing moss), the background geometry (mountains, floating islands, snow pines, aurora trees), the weather particles (petals, dandelion fluff, leaves, snow, star motes), and even the music scale.
- **A bird with a soul**: procedural wing-beat cycle, squash-and-stretch on flap, velocity-linked tilt, blinking, blush, unlockable hats and feather colours, and a rainbow trail during Star Power.
- **Real game feel**: sub-stepped collisions so nothing tunnels, hitstop + screen shake + feather burst on death, close-call bonuses, cherry → sparkle meter → Star Power (which *smashes* pipes), bubble shields that bounce you back into the gap, moving pipes, drifting aim-guide stars, three flight modes.
- **A procedural soundtrack** in WebAudio — pentatonic lead, triangle bass, filtered-noise shaker, major by day / minor by night — plus ~9 synthesised SFX. Both toggleable.
- Everything on the rails (hat/skin thumbnails, medals, twinkles, ticker) is drawn by code — no images, no fonts, no network.

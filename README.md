# Flappy Bird, one-shot, Qwen3.8 Flash-Next configurations x token caps

Each `index.html` is the model's UNEDITED one-shot output: a single request per (configuration, cap), no retries, no human edits. Same prompt and sampler across every cell; only the served configuration and the max_tokens cap differ.

## Prompt (verbatim)

```
Make the ultimate adorable cute and beautiful flappy bird game in HTML. write a complete single-file Flappy Bird in HTML/JS, playable in a browser, no external assets, generate your own assets
```

## Sampler

temperature 1.0, top-p 0.95, top-k 20, reasoning xhigh, seed 20260829, MTP depth 3; one request per cell, no retries

## Comparison

| config | max_tokens | decode tok/s | TTFT (s) | completion tokens | finish | complete HTML | plays in a browser |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [475-base-64k](./475-base-64k/) | 65536 | 90.11 | 0.45 | 51297 | stop | yes | no: freezes on the first flap (biome lookup returns undefined inside render, the frame loop dies) |
| [478-typical-0.2-64k](./478-typical-0.2-64k/) | 65536 | 98.90 | 0.58 | 46051 | stop | yes | yes |
| [485-tokenv3-0.95-64k](./485-tokenv3-0.95-64k/) | 65536 | 94.55 | 0.47 | 37551 | stop | yes | no: freezes at the first sky colour fade (NaN colour in a gradient stop inside render, the frame loop dies) |
| [488-bare-opt-0.5-64k](./488-bare-opt-0.5-64k/) | 65536 | 122.53 | 0.48 | 5956 | stop | no | no: no HTML emitted, the generation collapsed into a repetition loop after 5,956 tokens |
| [488-bare-typical-0.2-64k](./488-bare-typical-0.2-64k/) | 65536 | 100.56 | 0.67 | 43450 | stop | yes | yes |

Configs: 475-base = #475 opts only (no lossy mode, rounding-class); 478-typical-0.2 = typical acceptance 0.2 (lossy); 485-tokenv3-0.95 = speculative cascade, TokenV3 rule 0.95 (lossy); 488-bare-opt-0.5 = Bare-Speed pack, cascade OPT 0.5 (lossy); 488-bare-typical-0.2 = Bare-Speed pack, typical 0.2 (lossy). Each cell's served SHA, pack, and env are in that folder's `receipt.json`.

## Playability

"Plays in a browser" was checked by loading each `index.html` over HTTP in headless Chromium and driving it (state change on input, pipes scrolling, score incrementing). All four HTML documents pass a JavaScript syntax check; the two that do not play fail at runtime with an uncaught throw inside their render function before the next animation frame is requested, so one bad frame stops the game. Serve the files over HTTP; opening them as file:// blocks parts of the page in Chromium.

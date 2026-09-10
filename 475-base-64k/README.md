# 475-base (max_tokens 65536)

The #475 optimizations alone, no lossy acceptance mode (the standard speculative-sampling rule); rounding-class, so output differs from release only by floating-point rounding.

One-shot result: finish `stop`, 51297 completion tokens, 90.11 tok/s, complete HTML document: yes.

- Cap (max_tokens): 65536
- Served worktree SHA: `aab2aca74cf105a6d6ab124c221b7d5b761403e9` (27d5ff6b lanes (aux-rebase tip); #475 opts only, no lossy acceptance mode (rounding-class))
- Pack: `Youssofal--Qwen3.8-Flash-Next-MTPLX-Optimized-Speed` (artifact `29ba90f82124961d0d902a9ea9bbb1034972af2f`)
- Env: `{}`
- TTFT: 0.45 s  |  wall: 569.70 s
- decode: 90.11 tok/s  |  mtp_accept: n/a
- HTML extraction: last ```html fenced block

The game in `index.html` is the model's unedited one-shot output.

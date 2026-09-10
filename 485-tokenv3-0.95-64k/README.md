# 485-tokenv3-0.95 (max_tokens 65536)

PR #485 cascade, Optimized-Speed pack, TokenV3 rule at alpha 0.95 (lossy).

One-shot result: finish `stop`, 37551 completion tokens, 94.55 tok/s, complete HTML document: yes.

- Cap (max_tokens): 65536
- Served worktree SHA: `8c467b4b1b98e114d679ba392ab466db25e07022` (cascade branch tip (TokenV3 default + defer_rate))
- Pack: `Youssofal--Qwen3.8-Flash-Next-MTPLX-Optimized-Speed` (artifact `29ba90f82124961d0d902a9ea9bbb1034972af2f`)
- Env: `{"MTPLX_FABLE_CASCADE_THRESHOLD": "0.95"}`
- TTFT: 0.47 s  |  wall: 397.61 s
- decode: 94.55 tok/s  |  mtp_accept: 0.8883
- HTML extraction: last ```html fenced block

Verdict line:

```
[cascade-accept] NOT distribution-exact; rule=tokenv3 threshold=0.95 alpha=0.95 positions=29877 accepted=26540 resamples=3337 deferred=3369 accept_rate=0.8883 defer_rate=0.1128 mean_divergence=0.1761 tokens_per_cycle=3.411 accepted_by_depth=[10031, 8837, 7672] generated=37551 verify_calls=11010
```

The game in `index.html` is the model's unedited one-shot output.

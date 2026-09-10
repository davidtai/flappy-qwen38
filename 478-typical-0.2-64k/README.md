# 478-typical-0.2 (max_tokens 65536)

PR #478 typical acceptance, Optimized-Speed pack, threshold 0.2 (lossy).

One-shot result: finish `stop`, 46051 completion tokens, 98.90 tok/s, complete HTML document: yes.

- Cap (max_tokens): 65536
- Served worktree SHA: `50d4c35f3709cd5c63f6cb68e678776d6d8da4a2` (264e0835 lanes (typical-rebase tip))
- Pack: `Youssofal--Qwen3.8-Flash-Next-MTPLX-Optimized-Speed` (artifact `29ba90f82124961d0d902a9ea9bbb1034972af2f`)
- Env: `{"MTPLX_FABLE_TYPICAL_THRESHOLD": "0.2"}`
- TTFT: 0.58 s  |  wall: 466.24 s
- decode: 98.90 tok/s  |  mtp_accept: 0.8558
- HTML extraction: last ```html fenced block

Verdict line:

```
[typical-accept] NOT distribution-exact; threshold=0.2 eps=1 delta=0.2 positions=37227 accepted=31859 resamples=5368 accept_rate=0.8558 mean_entropy=0.2893 tokens_per_cycle=3.245 accepted_by_depth=[12492, 10546, 8821] generated=46051 verify_calls=14192
```

The game in `index.html` is the model's unedited one-shot output.

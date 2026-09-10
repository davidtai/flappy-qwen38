# 488-bare-opt-0.5 (max_tokens 65536)

Bare-Speed pack, all Bare kernels, cascade OPT rule at alpha 0.5 (lossy).

One-shot result: finish `stop`, 5956 completion tokens, 122.53 tok/s, complete HTML document: no.

- Cap (max_tokens): 65536
- Served worktree SHA: `e27618d9698059cbbd2caac55d677c5a5900c779` (Bare pack, OPT 0.5 (lossy), all Bare kernels)
- Pack: `Youssofal--Qwen3.8-Flash-Next-MTPLX-Bare-Speed` (artifact `39c15cc6d45caeb4ea20e1e16e922c43fdb3e21f`)
- Env: `{"MTPLX_FABLE_CASCADE_RULE": "opt", "MTPLX_FABLE_CASCADE_THRESHOLD": "0.5"}`
- TTFT: 0.48 s  |  wall: 49.09 s
- decode: 122.53 tok/s  |  mtp_accept: 0.9648
- HTML extraction: NO html block found; whole content written verbatim

Verdict line:

```
[cascade-accept] NOT distribution-exact; rule=opt threshold=0.5 alpha=0.5 positions=4607 accepted=4445 resamples=162 accept_rate=0.9648 mean_divergence=0.2619 tokens_per_cycle=3.679 accepted_by_depth=[1534, 1481, 1430] generated=5864 verify_calls=1594
```

The game in `index.html` is the model's unedited one-shot output.

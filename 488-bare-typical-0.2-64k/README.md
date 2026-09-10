# 488-bare-typical-0.2 (max_tokens 65536)

Bare-Speed pack, all Bare kernels, typical acceptance at threshold 0.2 (no cascade); measurement-only tree.

One-shot result: finish `stop`, 43450 completion tokens, 100.56 tok/s, complete HTML document: yes.

- Cap (max_tokens): 65536
- Served worktree SHA: `e27618d9698059cbbd2caac55d677c5a5900c779` (Bare pack, all Bare kernels, typical 0.2 (no cascade); measurement-only tree)
- Pack: `Youssofal--Qwen3.8-Flash-Next-MTPLX-Bare-Speed` (artifact `39c15cc6d45caeb4ea20e1e16e922c43fdb3e21f`)
- Env: `{"MTPLX_FABLE_TYPICAL_THRESHOLD": "0.2"}`
- TTFT: 0.67 s  |  wall: 432.74 s
- decode: 100.56 tok/s  |  mtp_accept: 0.8573
- HTML extraction: last ```html fenced block

Verdict line:

```
[typical-accept] NOT distribution-exact; threshold=0.2 eps=1 delta=0.2 positions=35102 accepted=30093 resamples=5009 accept_rate=0.8573 mean_entropy=0.3203 tokens_per_cycle=3.253 accepted_by_depth=[11753, 9993, 8347] generated=43450 verify_calls=13356
```

The game in `index.html` is the model's unedited one-shot output.

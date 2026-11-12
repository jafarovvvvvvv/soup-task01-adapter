# Soup take-home task 01: LoRA adapter (a failed experiment, do not use as a model)

This is the trained LoRA adapter behind the report I sent to the Soup team. The report's verdict is **DON'T SHIP**: the adapter weakens refusals of harmful requests
(soup ship mini_safety 1.00 to 0.625 on base + adapter, 0.975 to 0.50 on the 4-bit pair; Soup's 40 harmful prompts: 40 of 40 refused by the base, 21 of 40 with this adapter).
It is published only so the experiment can be reproduced and checked.

## Files
- `adapter_model.safetensors`: LoRA weights (r=16, alpha=32, all 7 linear projections, 18,464,768 parameters), SHA-256 `3f747c54bd8dd4e9df2e757f7a1b5c64a6f8a723fb64de264cbd2fc33d8991df`, 73,911,112 bytes
- `adapter_config.json`: PEFT configuration (peft 0.21.2)
- `soup.yaml`: the exact Soup 0.77.2 training config
- `soup_run.json`: run id, base revision and SHA-256 of the training data as recorded by Soup
- `train_1598_rows.jsonl`: the exact training rows (random subsample of d0rj/alpaca-cleaned-ru, seed 42; Soup used 80 more rows of this file as validation)

## Exact base model
`Qwen/Qwen2.5-1.5B-Instruct`, revision `989aa7980e4cf806f80c7fef2b1adb7bc71aa306`

## Use
```bash
pip install "soup-cli[train]"
soup merge --adapter ./adapter_release --base Qwen/Qwen2.5-1.5B-Instruct --output merged   # note: soup merge defaults to float16, the base is bfloat16
```
or load it with `peft.PeftModel.from_pretrained(base_model, "./adapter_release")`.

Training data comes from a machine-translated Alpaca-style dataset; check its license before any reuse. This adapter is not a safe model.

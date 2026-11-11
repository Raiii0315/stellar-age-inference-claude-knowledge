---
name: cpu-inference-ok
description: StellarPerceptron inference on CPU matches GPU results; offer CPU for inference-only jobs when the GPUs are busy (speed is per server)
metadata:
  type: project
---

Inference with StellarPerceptron (`model_torch/`) runs fine on CPU.
- Setup: `device="cpu"`, `mixed_precision=False`, `torch.set_num_threads(16)`.
- Speed depends on the server; see [[server-current]].
- Accuracy is the same as on GPU: Teff σ 47.21 K on CPU vs 47.20 K in the GPU reproduction (checked 2026-10-14).

On 2026-10-14 all GPUs were busy with other users' jobs, and the user approved using CPU instead.

**How to apply:** for inference-only jobs, offer CPU before asking for GPU 0 ([[confirm-gpu-before-run]]). Training or embedding extraction at scale may still need a GPU.

Running scripts outside the repo: `uv run --project <repo>` does not put the repo on `sys.path`. Either do `sys.path.insert(0, repo)` in the script, or set `PYTHONPATH`, in which case don't use `-I`.

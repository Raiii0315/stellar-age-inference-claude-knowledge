---
name: confirm-gpu-before-run
description: "Before any GPU job, check the server's GPU usage and get OK unless standing approval for the batch; which GPU may be used is per server (server-current.md)"
metadata:
  node_type: memory
  type: feedback
  originSessionId: d12aff24-e5b6-43a3-abcc-353fda85484f
  modified: 2026-10-06T14:26:06.154Z
---

Before launching any GPU job (notebook execution, inference, training), check the GPU usage (`nvidia-smi`: memory, utilization, other users' processes) immediately beforehand and get the user's OK. Which GPUs may be used, and other per-server conditions, are in [[server-current]].

**Why:** GPUs may be shared with other users; the user wants to avoid disturbing others. They interrupted a run started without asking (2026-10-06). Later the same day they said: "run the other tasks too if GPU 0 looks OK" — a standing approval for the remaining reproduction notebooks, conditional on the GPU being free.

**How to apply:** By default, show the GPU status and wait for explicit OK. When the user has given standing approval for a batch, still check `nvidia-smi` before each run, proceed only if the allowed GPU is essentially idle, and stop to ask if it is busy. Training (multi-day) is a new context — ask again.

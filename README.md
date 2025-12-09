# **Z-Image Turbo Benchmark**

*A practical, visual, and cross-device benchmark suite for Z-Image Turbo workflows, GGUF-based pipelines, and Qwen3-powered decoding.*

This project compares the real-world performance of various Z-Image Turbo (ZIT) quantisation levels and alternative pipelines (FP8, BF16, GGUF, VAE-based flows) across multiple GPUs and Apple Silicon. It focuses on *practical sampling speed* and *end-to-end generation time*, using consistent prompts, seeds, and image settings.

All benchmark data is stored in `benchmarks.json` .

---

# **Live Demo**

**👉 *[GitHub Pages link](https://miroleon.github.io/z-image-turbo-benchmark/)***

The web UI lets you:

* Browse workflows visually (output images)
* Compare multiple workflows side-by-side
* Inspect per-device timing (Run 1 warmup + Run 2 steady state)
* Filter by quant scheme / device
* Understand tradeoffs between speed, quantisation, and image quality

---

# **Benchmark Summary**

This benchmark tests:

* **4 devices**

  * RTX 4070 Ti
  * RTX 3090 Ti
  * RTX 3080 Laptop
  * Mac Mini M4 Pro
* **Multiple full pipelines and quantisation strategies**

  * AIO FP8
  * BF16 + Qwen3 + VAE
  * FP8 + Qwen3 + VAE
  * GGUF pipelines (BF16/FP8 + Qwen Q5)
  * Z-Image Turbo quantised U-Nets (Q3/Q4/Q5) paired with Qwen Q2 or Qwen Q5 decoders
* **A shared generation prompt, resolution, and iteration count**
  (1024×1024, 7 steps, denoise 0.82)
* **Run 1** = warm start
* **Run 2** = steady-state performance

---

# **Key Insights**

## **1. Z-Image Turbo quantisation (Q3/Q4/Q5) significantly boosts Apple Silicon performance**

Quantised ZIT models bring Apple Silicon performance closer to lower-tier NVIDIA cards.

On **Mac Mini M4 Pro**:

* FP8/BF16 pipelines: **17–26 s/it** → 125–210 s total
* **ZIT Q3/Q4/Q5:** ~**14–15 s/it**, ~**104–108 s total** (Run 2)

This is roughly a **20–30% improvement** vs non-quantised workflows.

---

## **2. RTX 3080 Laptop performs surprisingly well given thermal limits**

Typical performance:

* **2.0–2.6 s/it**, **17–35 seconds total**
* Roughly **2× slower than the 4070 Ti**, but with consistent scaling.

ZIT Q workflows remain usable on laptop GPUs:

* ZIT Q4 + Qwen Q5 (Run 2): **2.39 s/it, 19.72 s total**

This makes Z-Image Turbo attractive for portable AI compute. However, the larger models may fail or be slow due to the very limited RAM of 16GB.

---

## **3. Q-Levels (Q3/Q4/Q5) do *not* drastically change speed**

On all NVIDIA cards:

* Q3, Q4, Q5 vary by **±5–10%** in sampling speed.
* Example on 4070 Ti (Run 2):

  * Q3 + Qwen Q2 → **1.36 s/it**
  * Q4 + Qwen Q2 → **1.26 s/it**
  * Q5 + Qwen Q2 → **1.32 s/it**

This means users can select quant levels based on **quality**, not performance constraints.

---

## **4. Decoder choice (Qwen Q2 vs Qwen Q5) has minimal speed impact**

Across all devices:

* Switching between Q2 and Q5 changes runtime by **0–10%**, usually negligible.
* Q5 is much closer to the full Qwen 3 4B in terms of visual quality.

---

# **Data Format**

Benchmarks live in `benchmarks.json` and follow this schema:

```json
{
  "workflow": "ZIT Q4 + Qwen Q5",
  "device": "RTX 4070 Ti",
  "run1": { "s_per_it": 1.39, "total_seconds": 29.60 },
  "run2": { "s_per_it": 1.25, "total_seconds": 9.17 }
}
```

Run 2 is the metric featured in comparisons.

---

# **How to Run Locally**

You can run the benchmark viewer locally without a backend:

```bash
git clone <repo>
cd <repo>
python -m http.server 8000
```

Then open:

```
http://localhost:8000
```

---

# **Acknowledgements**

Thanks to the open-source communities behind:

* Z-Image Turbo
* Qwen3 / GGUF ecosystems
* FP8 deployment research
* Web inference tooling
* Phosphor Icons
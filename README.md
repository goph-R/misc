**GTX 1660 (6 GB)** on **Pop!_OS** is great for local 7B models. Below are two **pre-built** paths that “just work” on Linux:

---

# A) Easiest: **Ollama** (daemon + CLI)

> Works with NVIDIA GPUs that have **compute capability ≥ 5.0** (your GTX 1660 is **Turing, CC 7.5**, so you’re good). ([Ollama Docs][1])

### 1) Verify the driver sees your GPU

```bash
nvidia-smi
```

(You should see the GTX 1660 and driver version.)

### 2) Install Ollama (one-liner)

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

This puts `ollama` in your PATH and sets up a systemd service. ([Ollama Docs][2])

### 3) Make sure the service is running

```bash
sudo systemctl status ollama
# if not active:
sudo systemctl enable --now ollama
```

(Ollama’s Linux installer is service-based; enabling ensures it survives reboots.) ([Arsturn][3])

### 4) Pull a model that fits 6 GB and run it

```bash
# solid general model (7B)
ollama pull mistral
ollama run mistral
```

Other good picks on 6 GB: `llama3` (8B, still fine), `gemma:7b`, `phi3:mini`. First run downloads weights. ([Ollama][4])

### 5) Keep VRAM in check (optional)

* Limit context if you see OOMs:

```bash
ollama run mistral --num-ctx 2048
```

* Watch VRAM live:

```bash
watch -n 0.5 nvidia-smi
```

---

# B) Single-file app: **KoboldCpp** (GUI/CLI, llama.cpp backend)

> Handy if you prefer a **self-contained binary** with a small web UI. Download, `chmod +x`, run. ([GitHub][5])

### 1) Grab the prebuilt Linux binary

```bash
# one-liner from the project wiki (modern x86_64 Linux)
curl -fLo koboldcpp \
  https://github.com/LostRuins/koboldcpp/releases/latest/download/koboldcpp-linux-x64 && \
chmod +x koboldcpp
```

If you have trouble on older hardware, try their `-oldpc` build (CUDA 11). ([GitHub][6])

### 2) Put a model file next to it

Download a **GGUF** model that fits 6 GB, e.g.
`mistral-7b-instruct-Q4_K_M.gguf` (good balance) or `phi-3-mini-4k-instruct-Q4_K_M.gguf` (tiny + fast).

### 3) Run it with GPU offload

```bash
./koboldcpp \
  --model ./mistral-7b-instruct-Q4_K_M.gguf \
  --contextsize 2048 \
  --threads 8 \
  --gpulayers 999   # offload as many layers as will fit on the 1660
```

You’ll get a local web UI (printed URL) and a CLI. ([GitHub][5])

---

## What to expect on a GTX 1660 (6 GB)

* Best experience with **7B models (Q4/Q5)**; 8B can work with tighter settings.
* No Tensor Cores on 1660 → slower than RTX, but perfectly usable for chat/coding.
* If you hit OOM, drop to **Q3/Q4 quant** or lower the **context**.

---

If you tell me whether you prefer **terminal only** (Ollama) or a **tiny local web UI** (KoboldCpp), I’ll tailor exact model picks and flags for your 6 GB sweet spot.

[1]: https://docs.ollama.com/gpu?utm_source=chatgpt.com "Hardware support"
[2]: https://docs.ollama.com/linux?utm_source=chatgpt.com "Linux"
[3]: https://www.arsturn.com/blog/step-by-step-guide-to-installing-ollama-on-linux?utm_source=chatgpt.com "Step-by-Step Guide to Installing Ollama on Linux"
[4]: https://ollama.com/download?utm_source=chatgpt.com "Download Ollama on Linux"
[5]: https://github.com/LostRuins/koboldcpp?utm_source=chatgpt.com "LostRuins/koboldcpp: Run GGUF models easily with a ..."
[6]: https://github.com/LostRuins/koboldcpp/wiki?utm_source=chatgpt.com "Home · LostRuins/koboldcpp Wiki"

# Local AI Setup Guide - Conversation Log

## Session Overview
This conversation covers setting up local AI models (LLMs and Stable Diffusion) on a system with 6GB VRAM (RTX 4050), including model recommendations, agentic behavior, and budget hardware options.

---

## Part 1: Getting Started with Llama CLI

### Initial Setup

**User Question:** How to download and use a model with Llama CLI for 6GB RTX 4050?

**Solution:**
The easiest way to download models is directly from Hugging Face:

```bash
./llama-cli.exe -hf <user>/<model>:quant -ngl 999 -p "Your prompt here"
```

Key parameters:
- `-hf`: Download from Hugging Face
- `-ngl 999`: Offload layers to GPU (auto-adjusts to fit)
- `-cnv`: Conversation mode for interactive chat

### Recommended Models for 6GB VRAM

#### 1. Qwen 2.5 3B (Recommended)
```bash
./llama-cli.exe -hf Qwen/Qwen2.5-3B-Instruct-GGUF:q4_k_m -ngl 999 -cnv
```
- ~2GB VRAM for Q4_K_M
- Excellent at coding, reasoning, general tasks
- Leaves room for context

#### 2. Phi-3.5 Mini 3.8B
```bash
./llama-cli.exe -hf microsoft/Phi-3.5-mini-instruct-gguf:Q4_K_M -ngl 999 -cnv
```
- ~2.5GB VRAM
- Strong reasoning and math capabilities

#### 3. Llama 3.2 3B
```bash
./llama-cli.exe -hf hugging-quants/Llama-3.2-3B-Instruct-Q4_K_M-GGUF:q4_k_m -ngl 999 -cnv
```
- ~2GB VRAM
- Latest Llama model, balanced performance

**Quantization Note:** Q4_K_M is the sweet spot for 6GB VRAM - good quality with manageable size.

---

## Part 2: Best Model for Coding

### Qwen2.5-Coder Models (Specialized for Coding)

#### Option 1: Qwen2.5-Coder-7B (Best)
```bash
./llama-cli.exe -hf Qwen/Qwen2.5-Coder-7B-Instruct-GGUF:q4_k_m -ngl 999 -cnv
```
- ~4-4.5GB VRAM
- Should fit in 6GB with ~1.5-2GB for context
- Best coding performance
- Trained on massive amount of code

#### Option 2: Qwen2.5-Coder-1.5B (Lighter)
```bash
./llama-cli.exe -hf Qwen/Qwen2.5-Coder-1.5B-Instruct-GGUF:q4_k_m -ngl 999 -cnv
```
- ~1GB VRAM
- Very light, large context windows possible
- Still capable for its size

**Hugging Face Repository:**
https://huggingface.co/Qwen/Qwen2.5-Coder-7B-Instruct-GGUF

**File to download:** `qwen2.5-coder-7b-instruct-q4_k_m.gguf` (~4.3-4.5GB)

### Download Methods

**Option 1: Automatic (Recommended)**
The llama-cli command above automatically downloads and caches the model.

**Option 2: Manual**
1. Visit the Hugging Face repo
2. Download the `.gguf` file
3. Run: `./llama-cli.exe -m qwen2.5-coder-7b-instruct-q4_k_m.gguf -ngl 999 -cnv`

---

## Part 3: Agentic Behavior for Local Models

### Can Local Models Have Agentic Behavior?

**Yes!** Multiple options available, all can work offline.

### 1. Open Interpreter (Easiest Offline Agent)

```bash
pip install open-interpreter
interpreter --local
```

Or configure in Python:
```python
from interpreter import interpreter

# Point to local llama-server
interpreter.llm.api_base = "http://localhost:8080/v1"
interpreter.llm.api_key = "fake-key"
interpreter.offline = True

interpreter.chat()
```

**Offline capabilities:**
- Execute Python code
- Read/write files
- System operations
- Data analysis
- Image processing (with local libraries)

### 2. LlamaIndex (Full Offline)

```bash
pip install llama-index llama-index-llms-llama-cpp
```

```python
from llama_index.llms.llama_cpp import LlamaCPP
from llama_index.core.tools import FunctionTool

# Load local model
llm = LlamaCPP(
    model_path="./qwen2.5-coder-7b-instruct-q4_k_m.gguf",
    n_gpu_layers=-1,  # Use GPU
)

# Define local tools (file ops, code execution, etc.)
```

### 3. Aider (Coding Agent)

**Setup with llama-server:**

**Step 1:** Start llama-server
```bash
./llama-server.exe -m qwen2.5-coder-7b-instruct-q4_k_m.gguf -ngl 999 -c 4096
```

**Step 2:** Point Aider to it
```bash
aider --openai-api-base http://localhost:8080/v1 --openai-api-key dummy --model qwen2.5-coder
```

**Alternative: Using Ollama**
```bash
# Create Modelfile
echo "FROM ./qwen2.5-coder-7b-instruct-q4_k_m.gguf" > Modelfile
ollama create qwen-coder -f Modelfile

# Run Aider
aider --model ollama/qwen-coder
```

### Available Offline Tool Capabilities

✅ **Works Offline:**
- File system operations
- Code execution (Python, bash)
- Data processing (CSV, JSON, databases)
- Math/calculations (NumPy, pandas)
- Image processing (PIL, OpenCV)
- Git operations
- System information

❌ **Not Available Offline:**
- Web search
- External APIs
- Cloud services

### Other Agent Frameworks

- **LangChain** - Similar to LlamaIndex
- **GPT Engineer** - Builds entire codebases
- **AutoGPT** - Autonomous agent
- **Agent-Zero** - Python-based agent framework

---

## Part 4: Stable Diffusion 1.5 Models

### Best SD 1.5 Models for Architecture & Panoramic Views

#### 1. Realistic Vision v5.1 / v6.0B1 (Best Overall)
- Photorealistic architecture
- Great lighting and material rendering
- Clean, professional results
- https://civitai.com/models/4201/realistic-vision-v51

#### 2. epiCRealism
- Extremely photorealistic
- Excellent for modern architecture
- Sharp details and accurate perspectives
- https://civitai.com/models/25694/epicrealism

#### 3. DreamShaper (Most Versatile)
- Balance between realism and artistic style
- Modern and historical architecture
- Handles wide-angle shots well
- https://civitai.com/models/4384/dreamshaper

#### 4. Deliberate v2
- Strong architectural details
- Good for interior/exterior scenes
- Consistent quality
- https://civitai.com/models/4823/deliberate

### Panoramic View Tips

**Resolution:** 768x384 or 1024x512 (2:1 or wider aspect ratio)

**Prompt example:**
```
architectural photography, modern building, panoramic view, wide angle,
professional photography, golden hour lighting, 8k, highly detailed,
sharp focus, clean lines
```

**Negative prompt:**
```
people, cars, distorted perspective, blurry, low quality,
watermark, text
```

**Advanced techniques:**
- ControlNet with depth or line art
- MultiDiffusion extension for ultra-wide panoramas
- Tiled VAE for high-res without artifacts

### Best SD 1.5 Models for Anime

#### 1. Anything v5 / v3 (Most Popular)
- Very versatile anime style
- Clean, vibrant colors
- Good character consistency
- https://civitai.com/models/9409/or-anything-v5

#### 2. CounterfeitV3.0 (Best Quality)
- High-quality anime aesthetics
- Beautiful lighting and details
- Great for character art
- https://civitai.com/models/4468/counterfeit-v30

#### 3. AbyssOrangeMix3 (Most Artistic)
- Stunning color palette
- Painterly anime style
- Great backgrounds
- Multiple variants (A1, A2, A3)
- https://civitai.com/models/9942/abyssorangemix3-aom3

#### 4. Pastel Mix (Soft Style)
- Softer, gentler aesthetic
- Beautiful pastel colors
- Great for cute/moe characters
- https://civitai.com/models/5414/pastel-mix

#### 5. Cetus Mix (Versatile)
- Balance between detail and style
- Good for characters and scenes
- Consistent results
- https://civitai.com/models/6755/cetus-mix

### Anime Prompt Tips

**Sample prompt:**
```
masterpiece, best quality, 1girl, anime style, beautiful detailed eyes,
colorful, highly detailed, studio lighting
```

**Negative:**
```
lowres, bad anatomy, bad hands, text, error, missing fingers,
extra digit, fewer digits, cropped, worst quality, low quality
```

**LoRA recommendations for backgrounds:**
- Makoto Shinkai style (Your Name/Weathering With You vibes)
- Ghibli style (Studio Ghibli backgrounds)

---

## Part 5: Budget Hardware Options

### Laptop GPUs with 6GB VRAM

#### Budget-Friendly Options ($700-1200)

**1. RTX 4050 (6GB)**
- Good performance for price
- Runs Qwen 7B Q4 + SD 1.5 comfortably
- $800-1200 laptops

**2. RTX 3060 Laptop (6GB)**
- Older but capable
- Similar performance to RTX 4050
- $700-1000 laptops (cheaper used)

#### Better Performance Options

**3. RTX 4060 Laptop (8GB)**
- Better performance
- More VRAM headroom
- $1000-1400 laptops

### Recommended Laptop Models

**Budget ($800-1100):**
- ASUS TUF Gaming A15/A17 (RTX 4050)
- Lenovo LOQ 15 (RTX 4050)
- MSI GF63 Thin (RTX 4050)
- Acer Nitro 5 (RTX 4050)

**Mid-Range ($1100-1500):**
- ASUS ROG Strix G15/G16 (RTX 4060 8GB)
- Lenovo Legion 5/5i (RTX 4060 8GB)
- MSI Katana 15 (RTX 4060 8GB)

### What to Look For

✅ **Must have:**
- RTX 4050 (6GB) minimum
- 16GB RAM (32GB ideal for larger contexts)
- Good cooling
- SSD 512GB+

✅ **Nice to have:**
- Higher TGP (95W+ for RTX 4050)
- Good CPU (Ryzen 7 7000 series or Intel i7 13th/14th gen)
- Upgradeable RAM

### AMD Ryzen Integrated GPU Option

**Yes, AMD iGPUs CAN be used for AI!** Great budget option with caveats.

#### Best AMD APUs

**1. Ryzen 7 7840HS / 7940HS (Radeon 780M)**
- 12 compute units
- ~2-3 TFLOPS
- Can run Qwen 7B + SD 1.5
- Uses shared system RAM as VRAM

**2. Ryzen 5 7640HS (Radeon 760M)**
- 8 compute units
- Slower but usable

**3. Ryzen 7 6800U (Radeon 680M)**
- Previous gen, still capable

**4. Ryzen 5 5600G (Vega 7)**
- Budget desktop APU
- Very slow but works for learning

### How to Use AMD iGPUs

**For llama.cpp (Qwen):**

**Option 1: ROCm (Linux recommended)**
```bash
cmake -DGGML_HIPBLAS=ON ..
./llama-cli -m model.gguf -ngl 25
```

**Option 2: Vulkan (Works on Windows)**
```bash
cmake -DGGML_VULKAN=ON ..
./llama-cli -m model.gguf -ngl 25
```

**Option 3: CLBlast (OpenCL)**
- Better compatibility, slower

**For Stable Diffusion:**

**DirectML (Windows - easiest)**
```bash
pip install torch-directml
# Works with AUTOMATIC1111, ComfyUI
```

**ROCm (Linux)**
```bash
pip install torch torchvision --index-url https://download.pytorch.org/whl/rocm5.7
```

### Performance Expectations

**Radeon 780M (Best iGPU):**
- Qwen 2.5 3B Q4: ~15-25 tokens/sec (usable!)
- Qwen 7B Q4: ~5-10 tokens/sec (slow but works)
- SD 1.5: ~10-20 sec/image at 512x512
- Advantage: Uses system RAM (32GB = 32GB "VRAM")

**Radeon 680M/760M:**
- ~30-50% slower
- Still functional for smaller models

**Old Vega Graphics:**
- Very slow (~2-5 tokens/sec)
- Good for experimentation only

### Budget Laptop Examples ($500-800)

- Lenovo IdeaPad Gaming 3 (Ryzen 7 7840HS)
- ASUS Vivobook Pro 16 (Ryzen 7 7840HS)
- Acer Swift X (Ryzen 7 7840U)
- Framework Laptop 13 (Ryzen 7 7840U) - upgradeable!

**Key advantage:** 32GB RAM upgrade (~$100) = instant "32GB VRAM"

### Comparison Table

| Solution | Price | Speed | VRAM | Best For |
|----------|-------|-------|------|----------|
| RTX 4050 Laptop | $800-1200 | Fast | 6GB | Balanced |
| AMD 780M iGPU | $500-800 | Medium | Shared RAM | Budget |
| RTX 3060 Laptop | $700-1000 | Fast | 6GB | Best value used |
| Old AMD Vega | $300-500 | Slow | Shared RAM | Learning only |

### AMD iGPU Pros & Cons

**Pros:**
- ✅ Much cheaper laptops
- ✅ Shared RAM = flexible "VRAM"
- ✅ Lower power consumption
- ✅ Better battery life (no discrete GPU)
- ✅ Can upgrade RAM easily (instant VRAM boost)

**Cons:**
- ❌ 2-3x slower than RTX 4050
- ❌ ROCm setup can be tricky (Linux easier)
- ❌ Less mature software ecosystem
- ❌ DirectML on Windows slower than CUDA

---

## Summary

### Key Takeaways

1. **Best coding model for 6GB VRAM:** Qwen2.5-Coder-7B Q4_K_M
2. **Agentic behavior:** Possible offline with Open Interpreter, Aider, LlamaIndex
3. **SD 1.5 Architecture:** Realistic Vision v6.0B1
4. **SD 1.5 Anime:** Anything v5, CounterfeitV3.0, AbyssOrangeMix3
5. **Budget hardware:** AMD 780M iGPU laptops ($500-800) are viable for learning/slower work
6. **Best value:** RTX 4050 laptops ($800-1200) for balanced performance

### Quick Start Commands

**Download and run Qwen Coder:**
```bash
./llama-cli.exe -hf Qwen/Qwen2.5-Coder-7B-Instruct-GGUF:q4_k_m -ngl 999 -cnv
```

**Start server for agent frameworks:**
```bash
./llama-server.exe -m qwen2.5-coder-7b-instruct-q4_k_m.gguf -ngl 999 -c 8192
```

**Use with Aider:**
```bash
aider --openai-api-base http://localhost:8080/v1 --openai-api-key dummy --model qwen2.5-coder
```

---

*Session Date: 2026-02-05*
*Hardware Context: RTX 4050 6GB VRAM*
*Software: llama.cpp, Stable Diffusion 1.5*

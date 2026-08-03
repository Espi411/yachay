# Having Your Own LLM — A Practical Guide

A reference for understanding the levels of "owning" a Large Language Model, from using hosted APIs to fine-tuning your own model. Written for someone who wants to understand the landscape without needing to be an ML engineer.

---

## Level 0 — Use Hosted APIs (Where Most People Start)

You call someone else's API — ChatGPT, Claude, Copilot, Gemini. They run the model on their servers, you send text, they send text back.

- **Cost:** Per-token or flat licensing (typically $0.01–0.06 per 1K tokens, or monthly subscriptions)
- **What you own:** Nothing — the model, the weights, the infrastructure are all theirs
- **What you learn:** Prompting, orchestration, agent design — but not the model itself
- **When it stops working:** When they change terms, rate-limit you, or you need data privacy that hosted APIs can't guarantee

This is where 90% of practical business value lives right now. Don't underestimate it. Most production AI systems — including AI agents, chatbots, and copilots — are built on hosted APIs.

---

## Level 1 — Run an Existing Model Locally

Download a pre-trained model (free, open-weights) and run it on your own hardware. This is the first step toward "owning" an LLM.

### What you need

- **Hardware:** A modern laptop can run smaller models (3B–8B parameters). A 7B model at Q4 quantization needs about 4–6 GB RAM. Most Macs (16–32 GB unified memory) can handle this well.
- **Software:** llama.cpp — lightweight, runs on CPU or GPU. Install via `brew install llama.cpp` on Mac, or build from source on Linux/Windows.
- **Model:** Download a GGUF file (the quantized format llama.cpp uses) from Hugging Face. Common starting points:
  - Llama 3.2 (3B) — small, fast, good for simple tasks
  - Llama 3.1 (8B) — capable, needs more RAM
  - Qwen 2.5 (7B) — strong alternative
  - Phi-3 Mini (4B) — Microsoft's small model, surprisingly good
  - Mistral (7B) — solid all-rounder

### The steps

```bash
# 1. Install llama.cpp
brew install llama.cpp          # Mac
# Or: git clone https://github.com/ggml-org/llama.cpp && cmake -B build && cmake --build build --config Release

# 2. Download a model (one command — llama.cpp pulls directly from Hugging Face)
# Q4_K_M is the recommended quantization (good quality/size balance)
llama-server -hf bartowski/Llama-3.2-3B-Instruct-GGUF:Q4_K_M

# 3. That gives you a local API at http://localhost:8080
# It's OpenAI-compatible — anything that speaks to ChatGPT can talk to your local model
```

```bash
# Test it with curl
curl http://localhost:8080/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{"messages": [{"role": "user", "content": "What is machine learning?"}]}'
```

### Choosing a quantization (compression level)

GGUF files come in different quantization levels — the tradeoff is model size vs. quality:

| Quant  | Size (7B model) | Quality | Use case |
|--------|-----------------|---------|----------|
| Q2_K   | ~2.5 GB         | Noticeable degradation | Very tight RAM |
| Q3_K_M | ~3.0 GB         | Usable                 | Tight RAM budgets |
| Q4_K_M | ~4.0 GB         | Good (recommended)     | General use — sweet spot |
| Q5_K_M | ~4.5 GB         | Better                 | Quality-sensitive tasks |
| Q6_K   | ~5.0 GB         | Near-original          | Best quality, more RAM |
| Q8_0   | ~6.5 GB         | Essentially original   | When RAM is not a concern |

Start with Q4_K_M. Move up if you have the RAM and need quality. Move down only if you must fit in limited memory.

### What you get

- **Cost:** $0 per token — just your hardware and electricity
- **What you own:** The model weights file. You can use it offline, forever, with no API dependency. Nobody can change terms on you.
- **What you learn:** Inference, quantization, hardware constraints, model selection, the OpenAI-compatible API format
- **Limitation:** Smaller models are noticeably less capable than GPT-4 or Claude Opus. A 3B model is useful for specific tasks (classification, extraction, simple Q&A, text formatting) but not general reasoning or complex code generation.
- **Privacy advantage:** Data never leaves your machine. This is the only level that works inside a locked-down network — no outbound API calls.

---

## Level 2 — Serve a Local Model as an API (Production-Grade)

Same as Level 1, but you're serving it for real — multiple users, reliability, monitoring, throughput. This is where vLLM comes in.

### What you need

- **A machine with a GPU:**
  - NVIDIA A10 (~$2K used, 24GB VRAM) — runs 7B–13B models comfortably
  - NVIDIA A100 (~$10K, 40GB or 80GB) — runs 7B–70B models
  - 70B models need 2–4x A100s (tensor parallelism across GPUs)
- **Or rent a GPU in the cloud:**
  - RunPod, Lambda Labs, Vast.ai — $0.50–3.00/hour depending on GPU
  - Great for learning and experimentation — spin up, use, tear down

### The steps

```bash
# 1. Install vLLM (on a GPU machine)
pip install vllm

# 2. Launch an OpenAI-compatible server
vllm serve meta-llama/Llama-3-8B-Instruct \
  --gpu-memory-utilization 0.9 \
  --max-model-len 8192 \
  --port 8000

# 3. Query it with the OpenAI SDK (or any OpenAI-compatible client)
python -c "
from openai import OpenAI
client = OpenAI(base_url='http://localhost:8000/v1', api_key='EMPTY')
print(client.chat.completions.create(
    model='meta-llama/Llama-3-8B-Instruct',
    messages=[{'role': 'user', 'content': 'Hello!'}]
).choices[0].message.content)
"
```

### Scaling up — larger models with quantization

For models too large for your GPU's VRAM, use quantization to compress them:

```bash
# AWQ quantization — best for 70B models, minimal accuracy loss
vllm serve TheBloke/Llama-2-70B-AWQ \
  --quantization awq \
  --tensor-parallel-size 1 \
  --gpu-memory-utilization 0.95
# Result: 70B model fits in ~40GB VRAM instead of ~140GB
```

### Monitoring a production deployment

```bash
# vLLM exposes Prometheus metrics on port 9090
curl http://localhost:9090/metrics | grep vllm
```

Key metrics to watch:
- `vllm:time_to_first_token_seconds` — latency (target: < 500ms)
- `vllm:num_requests_running` — active requests
- `vllm:gpu_cache_usage_perc` — KV cache utilization

### What you get

- **Cost:** GPU hardware (capex) or cloud rental (opex). Cloud rental for a single A100 is roughly $1–3/hour.
- **What you own:** The model + the serving infrastructure. You control uptime, access, data, and cost.
- **What you learn:** Production deployment, quantization (AWQ/GPTQ/FP8), tensor parallelism, load balancing, monitoring, Docker deployment
- **Tradeoff:** GPU hardware is expensive. Cloud rental avoids capex but adds up over time. For learning, rent by the hour and tear down when done.

---

## Level 3 — Fine-Tune an Existing Model

Take a base model and specialize it on your data. This is where "your own LLM" starts meaning something genuinely custom — a model that thinks the way you need it to for a specific domain.

### What you need

- **A base model** (open-weights): Llama-3-8B, Mistral-7B, Qwen-2.5-7B
- **Training data:** Hundreds to thousands of examples of input→output pairs in your domain. Format: JSONL with instruction/output pairs.
- **GPU access for training:** An A100 for a few hours. Rent on cloud for $5–50 per fine-tuning run.
- **LoRA (Low-Rank Adaptation):** The standard technique. Instead of retraining all 8 billion parameters, you train a small adapter (~50MB) that sits on top of the base model. Cheap, fast, effective.

### The steps

```bash
# 1. Install Unsloth (simplifies LoRA fine-tuning dramatically)
pip install unsloth

# 2. Prepare your training data (JSONL format)
# Each line: {"instruction": "...", "input": "...", "output": "..."}
# Example: governance decisions, meeting summaries, domain-specific Q&A

# 3. Run fine-tuning (Python script using Unsloth)
# Takes 1-3 hours on a single A100 for a 7B model with LoRA

# 4. Export — merge LoRA adapter into base model, quantize to GGUF
# 5. Serve with llama.cpp or vLLM (same as Levels 1 and 2)
```

### What fine-tuning actually does

Fine-tuning adjusts the model's behavior in three ways:
1. **Format consistency** — the model reliably outputs in the structure you need (JSON, specific templates, controlled vocabulary)
2. **Domain vocabulary** — the model uses the right terminology and understands domain-specific concepts
3. **Personality/style** — the model responds in a consistent voice that prompting alone can't reliably produce

### The honest part

Fine-tuning is often less impactful than people expect. For most use cases, good prompting on a strong base model beats a mediocre fine-tune. Fine-tuning wins when:
- You need consistent output format that prompting can't guarantee
- You have domain-specific vocabulary that the base model doesn't know well
- You need a specific personality or response style
- You're doing high-volume repetitive tasks where prompt engineering becomes expensive (long system prompts cost tokens every call)

Fine-tuning does NOT give the model new knowledge. For that, use RAG (Retrieval-Augmented Generation) — give the model access to a document database at inference time.

### What you get

- **Cost:** $5–50 per fine-tuning run on rented GPUs. Unsloth even runs on free Google Colab.
- **What you own:** A model specialized to your domain. Your training data, your fine-tuned weights.
- **What you learn:** The mechanics of how models learn, data quality (matters more than quantity), evaluation (how do you know the fine-tuned model is actually better?), the LoRA technique
- **Limitation:** The fine-tuned model is only as good as its training data. Garbage in, garbage out. Data curation is the hard part, not the training.

---

## Level 4 — Train From Scratch (Pre-Training)

Building a GPT-4 competitor. Do not do this.

- **Cost:** $1M–100M+. Requires thousands of GPUs running for weeks.
- **What you need:** A team of ML engineers, massive datasets, months of compute.
- **Why it's listed:** So you know it exists and can ignore it. Open-weights models from Meta (Llama), Mistral, Alibaba (Qwen), and Google (Gemma) are free to use and already excellent. Pre-training is not your game unless you're building a foundation model lab.

---

## Where to Actually Start

If you're new to this, start at Level 1. Run a small model on your own machine. Learn what a 3B model can and can't do. This is free, takes 30 minutes, and teaches you more about LLMs than any course.

**Progression:**

1. **Level 1 (free, 30 min):** Run a small model locally on your laptop. Understand inference, quantization, what models can actually do at small sizes.
2. **Level 2 ($5–10 in cloud rental):** Rent a GPU by the hour, run vLLM, serve a model as an API. This is the "I have my own AI API" experience. Point other tools at it.
3. **Level 3 ($5–50 per run):** Fine-tune a model on your own data. This is where your specific knowledge becomes a proprietary model. This is the path to a specialized AI product.

**The key insight:** You don't need to own the model to create value. The model is increasingly a commodity. What's scarce is the data, the prompts, and the workflows around it. Fine-tuning is how you turn your specific knowledge into a proprietary model — but the knowledge is the asset, not the model.

---

## Key Terms (Quick Reference)

| Term | Meaning |
|------|---------|
| **LLM** | Large Language Model — a neural network trained to predict the next token in text |
| **Parameters** | The "size" of a model. 7B = 7 billion parameters. Larger = more capable but needs more hardware |
| **Quantization** | Compressing a model to use less memory. Q4 = 4-bit precision. Tradeoff: size vs. quality |
| **GGUF** | File format for quantized models, used by llama.cpp |
| **LoRA** | Low-Rank Adaptation — cheap fine-tuning method that trains a small adapter, not the full model |
| **Inference** | Running a model to generate output (as opposed to training, which creates the model) |
| **Open-weights** | Models whose weights are freely available to download and use (Llama, Mistral, Qwen, Gemma) |
| **vLLM** | High-performance inference server — 24x faster than naive inference via PagedAttention |
| **Hugging Face** | The "GitHub of ML models" — where open-weights models are hosted and downloaded |
| **Token** | A chunk of text (roughly 4 characters or 0.75 words). Models process and generate text in tokens |

---

## Resources

- **llama.cpp:** https://github.com/ggml-org/llama.cpp
- **vLLM:** https://github.com/vllm-project/vllm
- **Hugging Face (models):** https://huggingface.co/models
- **Unsloth (fine-tuning):** https://github.com/unslothai/unsloth
- **OpenAI API format:** The de facto standard. Most local serving tools expose an OpenAI-compatible endpoint, so anything built for ChatGPT can be pointed at a local model.

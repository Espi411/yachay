# Local LLM for Regulated Work

**A Practical Guide to Running a 14B Language Model Locally for Compliance, Audit, and Privacy-Sensitive Work**

**Author:** Mescalito
**Version:** 1.0 Draft — August 2026

---

## Who This Guide Is For

This guide is for professionals working in regulated environments — compliance officers, risk managers, auditors, legal operations teams, data protection specialists, and engineers building internal tooling for industries like finance, healthcare, legal, and government.

You don't need to be a machine learning engineer. You need to be comfortable running commands in a terminal and following step-by-step instructions. If you've ever installed a tool via command line or followed a setup guide for a development environment, you can do this.

If your organization handles sensitive data that cannot be sent to cloud APIs — whether due to GDPR, HIPAA, SOX, client contractual obligations, or internal policy — this guide shows you how to run a capable language model entirely on your own hardware, with zero data leaving your machine.

---

## What You'll Be Able to Do After This Guide

After completing this guide, you will be able to:

- **Install and run llama.cpp** on a Mac, Linux, or Windows machine with a consumer-grade GPU or Apple Silicon.
- **Load and serve a 14-billion-parameter language model** (such as Qwen 2.5 14B or Llama 3 14B-class) in quantized form via GGUF.
- **Chat with the model through a browser interface** — no coding required for daily use.
- **Evaluate model quality** using a repeatable four-question framework designed for regulated work.
- **Understand the capabilities and limits of a 14B model** — what it handles well and where it fails.
- **Compare models and make informed tradeoffs** between quality, speed, context length, and hardware requirements.
- **Apply governance prompts** — structured prompts that produce audit-ready outputs for plan reviews, risk assessments, and status reporting.
- **Scale from a single laptop to a dedicated Mac mini server** with a documented architecture.
- **Prove your setup is private** — verify that no data leaves your machine during inference.
- **Calculate the full cost** of running a local LLM stack, including hardware, electricity, and time.

---

## The Problem This Solves

Most organizations want to use large language models for real work — drafting compliance memos, summarizing audit findings, reviewing risk plans, generating status updates for regulators. But three barriers block them:

1. **Privacy.** Cloud-based LLM APIs (OpenAI, Anthropic, Google) require sending your data to a third party. For regulated work, that's often impossible. Client data, internal risk assessments, and audit findings cannot be shipped to an external API — even one with a no-training guarantee — because the legal and reputational risk is too high.

2. **Auditability.** When you use a cloud API, you don't control the model, the prompt, or the output. You can't prove to an auditor exactly what model version produced a given output, what parameters were used, or whether the data was retained. In regulated environments, this lack of control is a dealbreaker.

3. **Cost predictability.** Cloud LLM pricing scales with usage. For organizations that want to run hundreds of queries per day — summarizing documents, generating reports, reviewing plans — the costs become unpredictable and hard to justify to procurement.

A local LLM solves all three: the model runs on hardware you own, the data never leaves your network, the model version and parameters are fully under your control, and the cost is fixed (hardware + electricity). A 14B-class model — quantized and served via llama.cpp — is the sweet spot: capable enough for real professional work, small enough to run on a single machine.

---

## What You Need Before You Start

### Hardware (Minimum)

| Component | Minimum | Recommended |
|-----------|---------|-------------|
| **CPU** | 8-core modern (Intel/AMD/Apple) | 12+ cores |
| **RAM** | 32 GB | 64 GB |
| **GPU** | Apple M2 (integrated, 16 GB unified) or NVIDIA 8 GB VRAM | Apple M4 Pro/Max or NVIDIA 16+ GB VRAM |
| **Storage** | 50 GB free (SSD) | 100 GB free (NVMe SSD) |

### Software

- **Git** (to clone llama.cpp)
- **CMake** (to build llama.cpp)
- A C/C++ compiler (Xcode Command Line Tools on macOS, `build-essential` on Linux, MSVC on Windows)
- **Python 3.10+** (for model download and some utilities)
- **huggingface-cli** (to download GGUF model files)
- A modern web browser

### Knowledge

- Comfort with the terminal: running commands, navigating directories, setting environment variables.
- Basic understanding of what a language model does (you don't need to understand transformers).
- Access to a model to evaluate — you'll download one in Part 1.

### Time

- Initial setup: 1–2 hours (including model download time).
- Evaluation and testing: 2–3 hours.
- Reading and understanding this guide: 1–2 hours.

---

## Part 1: Setting Up

This section walks you through installing llama.cpp, downloading a 14B model, and starting a browser-based chat interface. By the end, you'll be talking to a local model.

### Step 1: Install Build Tools

**macOS:**
```bash
xcode-select --install
brew install cmake
```

**Linux (Ubuntu/Debian):**
```bash
sudo apt update
sudo apt install build-essential cmake git python3 python3-pip
```

**Windows:**
Install Visual Studio Build Tools (C++ workload) and CMake. Use Git Bash or WSL2 for the best experience.

### Step 2: Clone and Build llama.cpp

```bash
git clone https://github.com/ggerganov/llama.cpp
cd llama.cpp
cmake -B build
cmake --build build --config Release
```

**For Apple Silicon** (M2/M3/M4), enable Metal acceleration:
```bash
cmake -B build -DGGML_METAL=ON
cmake --build build --config Release
```

**For NVIDIA GPUs**, enable CUDA:
```bash
cmake -B build -DGGML_CUDA=ON
cmake --build build --config Release
```

### Step 3: Download a 14B Model in GGUF Format

Install the Hugging Face CLI:
```bash
pip install huggingface-hub
```

Download a Q4_K_M quantization of Qwen 2.5 14B (a good balance of quality and size):
```bash
huggingface-cli download Qwen/Qwen2.5-14B-Instruct-GGUF \
  qwen2.5-14b-instruct-q4_k_m.gguf \
  --local-dir ./models
```

This downloads a ~9 GB file. The Q4_K_M quantization reduces the model from 28 GB (FP16) to ~9 GB while retaining most of the quality.

### Step 4: Start the Model Server

llama.cpp includes a built-in server with an OpenAI-compatible API and a web UI:

```bash
./build/bin/llama-server \
  --model ./models/qwen2.5-14b-instruct-q4_k_m.gguf \
  --ctx-size 8192 \
  --n-gpu-layers 99 \
  --port 8080
```

Key flags:
- `--ctx-size 8192`: Sets the context window to 8K tokens (adjust based on your RAM).
- `--n-gpu-layers 99`: Offloads all layers to GPU for maximum speed (use a smaller number if VRAM is limited).
- `--port 8080`: Serves the web UI and API at `http://localhost:8080`.

### Step 5: Open the Browser Chat

Navigate to:

```
http://localhost:8080
```

You'll see a chat interface. Type a message and the model responds. The model is running entirely on your machine — no network traffic leaves your computer except the initial model download (which is already complete).

**Verify you're set up correctly:** Ask the model, "What is the capital of France?" If it answers "Paris," your setup is working. If it fails or produces garbage, check that the model file downloaded completely and that GPU offload is active (look for "BLAS" or "Metal" or "CUDA" in the server startup log).

---

## Part 2: The 4 Questions

Before trusting a local LLM with real work, you need to evaluate it. Don't rely on benchmark scores — they rarely reflect the specific tasks you'll perform. Instead, use this four-question evaluation framework, designed for regulated work.

### Question 1: The Quality Floor

**What it tests:** Can the model produce a coherent, accurate, well-structured response on a straightforward professional task?

**Prompt:**
```
Summarize the following policy document in 5 bullet points, each no more than 20 words. Then list any clauses that appear to conflict with each other.

[Paste a real internal policy document, 500-1000 words]
```

**Pass criteria:**
- Summary is accurate — no hallucinated facts.
- Bullet points are within the length constraint.
- Conflicting clauses (if any) are correctly identified.
- Output is professional in tone and structure.

**Fail signals:**
- Model invents clauses that don't exist in the source.
- Ignores the length constraint.
- Produces a summary that a colleague would flag as inaccurate.

### Question 2: The Real Data Test

**What it tests:** Can the model work with the type of data you actually use — not synthetic test data?

**Prompt:**
```
Review this risk register entry and identify: (1) any missing required fields, (2) any risk ratings that seem inconsistent with the description, (3) suggested improvements to the mitigation plan.

[Paste a real (redacted) risk register entry]
```

**Pass criteria:**
- Model correctly identifies missing fields based on the entry structure.
- Risk rating inconsistencies (if present) are flagged with reasoning.
- Mitigation suggestions are practical and specific, not generic platitudes.

**Fail signals:**
- Model produces generic advice that could apply to any risk register.
- Model fails to recognize the structure of the entry.
- Model hallucinates fields that aren't in the source.

### Question 3: The Nuance Test

**What it tests:** Can the model handle ambiguity and edge cases without overstepping?

**Prompt:**
```
A vendor has proposed storing customer PII in a cloud database hosted in a different jurisdiction. Our data protection policy says PII must not leave the EU without adequate safeguards. The vendor says their hosting is "GDPR-compliant." 

What questions should we ask the vendor before approving this arrangement? Do not make a recommendation — only list the questions.
```

**Pass criteria:**
- Questions are specific and technically informed (e.g., "Which SCCs are in place?", "Where is the data physically stored?", "What is the sub-processor chain?").
- Model respects the constraint: it does NOT make a recommendation.
- Questions reflect understanding of GDPR transfer mechanisms.

**Fail signals:**
- Model makes a recommendation despite being told not to.
- Questions are generic ("Is it secure?") rather than specific.
- Model demonstrates misunderstanding of the regulatory framework.

### Question 4: The Privacy Proof

**What it tests:** Can you prove the model is not transmitting data?

**Method:**
1. Start the model server.
2. Open a network monitor (Activity Monitor on macOS, `tcpdump` or `wireshark` on Linux).
3. Send several prompts containing unique, identifiable phrases (e.g., "The password is BANANA-RIVER-42").
4. Search all outbound network traffic for those phrases.
5. Confirm: zero matches.

**Pass criteria:**
- No outbound traffic contains your test phrases.
- The only network activity is the initial model load (if not preloaded) and localhost connections.
- You can document this test for an auditor.

**Fail signals:**
- Any outbound packet contains your test data (this would indicate a misconfiguration or a compromised binary).

### Scoring

- **4/4 Pass:** The model is ready for production evaluation on real tasks.
- **3/4 Pass:** Acceptable, but identify which question failed and whether it's critical for your use case.
- **2/4 or below:** Do not deploy. Either the model is too weak, the quantization is too aggressive, or the prompt needs refinement.

---

## Part 3: What 14B Can and Can't Do

A 14B-parameter model (quantized to Q4_K_M) is surprisingly capable, but it has real limits. Understanding these boundaries prevents failures and builds trust with stakeholders.

### What 14B Does Well

- **Summarization.** Condensing documents, meeting notes, and reports into structured summaries. Quality is high when the source text fits within the context window.
- **Structured extraction.** Pulling fields, dates, entities, and categories from semi-structured text (risk registers, audit logs, compliance forms).
- **Drafting and editing.** Generating first drafts of memos, policy summaries, status updates, and email responses. The output typically needs human review but saves 60–80% of drafting time.
- **Question answering over provided context.** When you paste the relevant documents and ask questions about them, the model performs well — better than you might expect for its size.
- **Formatting and transformation.** Converting between formats (e.g., turning a bullet list into a formal paragraph, or a table into prose), restructuring documents, and applying templates.
- **Code and SQL generation** (for internal tooling). Simple scripts, SQL queries, and data transformation pipelines are within its capability.
- **Translation** between major languages at a professional-working level, especially for common pairs (English-Spanish, English-French, English-German).

### What 14B Struggles With

- **Complex multi-step reasoning.** Tasks requiring 5+ logical steps, where each step depends on the previous one, produce errors that compound. The model may get step 1 and 2 right but fail at step 3.
- **Long-context synthesis.** While the context window may be 8K–32K tokens, the model's effective attention degrades beyond ~6K tokens. Summarizing a 50-page document in one pass often misses details from the middle sections.
- **Specialized domain knowledge.** Niche regulatory frameworks, obscure tax codes, and highly technical medical or legal knowledge may be incomplete or outdated in the model's training data.
- **Numerical precision.** The model is not a calculator. It can set up calculations but should not be trusted to perform arithmetic. Always verify numbers.
- **Resisting prompt injection.** If user input contains instructions ("Ignore all previous instructions..."), a 14B model is less robust than larger models. This matters if the model processes untrusted input.
- **Consistency across long conversations.** In a 20-turn conversation, the model may contradict something it said 10 turns ago. For regulated work, treat each interaction as independent.

### The Honest Summary

A 14B model is a **capable assistant, not an autonomous expert**. It excels at tasks where you provide the context and it structures, summarizes, or transforms it. It fails when you expect it to reason independently through complex, multi-constraint problems without close supervision.

For regulated work, this is actually a feature: you want a human in the loop for any output that matters. The model does the heavy lifting of drafting and structuring; the human does the judgment and verification.

---

## Part 4: Model Selection and Tradeoffs

### The Models Table

| Model | Params | Min RAM | Q4_K_M Size | Context | Strengths | Weaknesses |
|-------|--------|---------|-------------|---------|----------|------------|
| **Qwen 2.5 14B Instruct** | 14B | 32 GB | ~9 GB | 32K (practical 8–16K) | Best overall quality at 14B, strong multilingual, good instruction following | Can be verbose, Chinese-centric training data |
| **Llama 3.2 3B Instruct** | 3B | 16 GB | ~2 GB | 128K (practical 8K) | Very fast, runs on minimal hardware, wide context support | Lower quality on complex tasks, more hallucination |
| **Mistral 7B Instruct v0.3** | 7B | 16 GB | ~5 GB | 32K (practical 8K) | Fast, good quality-to-size ratio, strong European language support | Less capable on complex reasoning than 14B |
| **Phi-3 Medium 14B** | 14B | 32 GB | ~8 GB | 128K (practical 8K) | Good reasoning, compact training, Microsoft-backed | Less community support, fewer fine-tunes available |
| **Qwen 2.5 32B Instruct** | 32B | 64 GB | ~20 GB | 32K (practical 8–16K) | Significant quality jump over 14B, near-GPT-3.5 quality | Requires 64 GB RAM, slower inference on consumer hardware |
| **Llama 3.1 8B Instruct** | 8B | 16 GB | ~5 GB | 128K (practical 8–16K) | Strong all-rounder, large ecosystem, well-tested | Noticeable quality gap vs 14B on nuanced tasks |

### How to Choose

**If you have 16 GB RAM:** Use Mistral 7B or Llama 3.1 8B. Accept lower quality on complex tasks.

**If you have 32 GB RAM:** Use Qwen 2.5 14B. This is the recommended default for this guide.

**If you have 64 GB RAM:** Consider Qwen 2.5 32B for a noticeable quality improvement, or stick with 14B for faster inference.

**If you have an Apple Silicon Mac with 48+ GB unified memory:** Qwen 2.5 32B runs well and is worth the upgrade from 14B.

### Context Window: What It Means and How to Set It

The context window is how many tokens (roughly 3/4 of a word) the model can consider at once — including both your prompt and its response.

- `--ctx-size 4096`: Conservative. Good for short interactions, minimal RAM usage.
- `--ctx-size 8192`: Recommended for most regulated work. Handles a 5-page document plus a response.
- `--ctx-size 16384`: For longer documents. Requires more RAM; inference may slow down.
- `--ctx-size 32768`: Only if you have 64+ GB RAM. Quality degrades at the edges of the window.

**Rule of thumb:** Set the context window to twice the length of the longest document you'll process. Don't set it higher than needed — it consumes RAM and slows inference even when unused.

### Temperature: What It Controls

Temperature controls randomness in the model's output. Higher = more creative, lower = more deterministic.

| Temperature | Use Case |
|-------------|----------|
| **0.0** | Factual extraction, data parsing, when you want the same output every time |
| **0.1–0.3** | Summarization, structured drafting, compliance work (recommended range) |
| **0.4–0.7** | Drafting memos, emails, creative-but-professional content |
| **0.8–1.0** | Brainstorming, exploring alternatives — not for regulated output |

**For regulated work, use temperature 0.1–0.3.** You want consistency and determinism, not creativity.

Set it in the API call:
```bash
curl http://localhost:8080/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "qwen2.5-14b",
    "messages": [{"role": "user", "content": "Summarize this document."}],
    "temperature": 0.2,
    "max_tokens": 500
  }'
```

---

## Part 5: Using Governance Prompts

Governance prompts are structured prompts designed to produce outputs that are consistent, auditable, and ready for review. They follow a pattern: define the role, specify the constraints, request a structured format, and prohibit unauthorized actions.

### The Governance Prompt Pattern

Every governance prompt should include:

1. **Role definition:** "You are a compliance reviewer."
2. **Input specification:** What the model is reviewing (pasted text).
3. **Output format:** Bullet points, a table, specific sections.
4. **Constraints:** What the model must NOT do (make recommendations, speculate, hallucinate).
5. **Failure mode:** What to output if the model cannot answer confidently.

### Example: Plan Review Prompt

```
You are a compliance reviewer evaluating a project plan for regulatory risks.

Review the following project plan and produce a structured assessment.

## Your Output Format

For each risk identified, provide:
- Risk ID: [R-001, R-002, ...]
- Risk Description: [one sentence]
- Likelihood: [Low / Medium / High]
- Impact: [Low / Medium / High]
- Related Regulation: [name the regulation if applicable, or "N/A"]
- Recommended Control: [one specific, actionable control]

## Constraints

- Do not make a go/no-go recommendation.
- Do not estimate costs.
- If you are uncertain about a regulation, say "REQUIRES LEGAL REVIEW" instead of guessing.
- Only identify risks you can justify with specific text from the plan.
- If the plan is too vague to assess, output: "INSUFFICIENT DETAIL FOR ASSESSMENT."

## Project Plan

[Paste the project plan here]
```

### Using It with curl

Save the prompt to a file (`plan_review_prompt.json`):

```json
{
  "model": "qwen2.5-14b",
  "messages": [
    {
      "role": "system",
      "content": "You are a compliance reviewer evaluating a project plan for regulatory risks. Only identify risks you can justify with specific text from the plan. If uncertain about a regulation, say 'REQUIRES LEGAL REVIEW'. Do not make go/no-go recommendations."
    },
    {
      "role": "user",
      "content": "Review the following project plan and produce a structured risk assessment.\n\nFor each risk, provide: Risk ID, Risk Description, Likelihood, Impact, Related Regulation, Recommended Control.\n\nIf the plan is too vague, output: INSUFFICIENT DETAIL FOR ASSESSMENT.\n\n--- PLAN START ---\n[Paste plan here]\n--- PLAN END ---"
    }
  ],
  "temperature": 0.2,
  "max_tokens": 2000
}
```

Run it:
```bash
curl http://localhost:8080/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d @plan_review_prompt.json | python3 -m json.tool
```

### Why This Works for Regulated Work

- **Reproducible:** The same prompt + same model + same temperature produces the same output (at temp 0.0–0.1). An auditor can rerun it.
- **Bounded:** The constraints prevent the model from overstepping — no recommendations, no guessing on regulations.
- **Structured:** The output format is predictable, making it easy to parse, store, and compare across runs.
- **Documented:** The prompt is saved as a file. The model version is recorded in the server log. The temperature is specified. Everything is auditable.

---

## Part 6: Scaling Up

Once you've validated the model on your laptop, you may want a dedicated machine that your team can access. The Mac mini path is the most cost-effective and lowest-complexity option for small teams.

### The Mac mini Path

**Hardware:**
- Mac mini with M4 Pro (14-core CPU, 20-core GPU) and 48 GB unified memory.
- Cost: ~$1,800–$2,200 depending on configuration.
- This runs Qwen 2.5 32B comfortably, or Qwen 2.5 14B with room to spare.

**Setup:**
1. Install macOS, set up SSH access.
2. Install Homebrew, CMake, Git.
3. Build llama.cpp with Metal support.
4. Download your model(s).
5. Create a launchd service (or systemd on Linux) to start the server on boot:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.local.llama-server</string>
    <key>ProgramArguments</key>
    <array>
        <string>/Users/shared/llama.cpp/build/bin/llama-server</string>
        <string>--model</string>
        <string>/Users/shared/models/qwen2.5-14b-instruct-q4_k_m.gguf</string>
        <string>--ctx-size</string>
        <string>8192</string>
        <string>--n-gpu-layers</string>
        <string>99</string>
        <string>--port</string>
        <string>8080</string>
        <string>--host</string>
        <string>0.0.0.0</string>
    </array>
    <key>RunAtLoad</key>
    <true/>
    <key>KeepAlive</key>
    <true/>
</dict>
</plist>
```

Save to `/Library/LaunchDaemons/com.local.llama-server.plist` and load with:
```bash
sudo launchctl load /Library/LaunchDaemons/com.local.llama-server.plist
```

### Architecture

```
┌─────────────────────────────────────────┐
│         Team Members (Browser)          │
│    [Browser → http://mini-ip:8080]      │
└──────────────┬──────────────────────────┘
               │ HTTP (internal network)
               ▼
┌─────────────────────────────────────────┐
│         Mac mini (M4 Pro, 48 GB)        │
│                                         │
│  ┌─────────────────────────────────┐    │
│  │     llama.cpp server :8080      │    │
│  │  (OpenAI-compatible API + Web)  │    │
│  └──────────────┬──────────────────┘   │
│                 │                       │
│  ┌──────────────▼──────────────────┐   │
│  │   GGUF Model (Q4_K_M, ~9-20 GB) │   │
│  │   Loaded in unified memory      │   │
│  └─────────────────────────────────┘   │
│                                         │
│  No external network access required    │
│  Data stays on this machine             │
└─────────────────────────────────────────┘
```

### What This Architecture Includes

- A single-machine LLM server accessible over the local network.
- The llama.cpp web UI for browser-based chat.
- An OpenAI-compatible API endpoint for integration with scripts and tools.
- Automatic restart via launchd/systemd.
- All inference runs locally — no data leaves the machine.

### What This Architecture Does NOT Include

- **Authentication.** The server is open on the local network. For production, put it behind a reverse proxy (nginx/Caddy) with basic auth or integrate with your SSO.
- **Rate limiting.** A single Mac mini handles 1–2 concurrent users well. For more, you need load balancing across multiple machines.
- **Model switching.** This setup serves one model. To offer multiple models, run multiple server instances on different ports or use a model router.
- **Logging and audit trails.** You'll need to add request logging (via a reverse proxy) for compliance audit trails.
- **Encryption in transit.** Traffic between browser and server is unencrypted HTTP. For production, add TLS via a reverse proxy.
- **Backup and model versioning.** Document which model version is loaded and when it changes. Regulators will ask.

---

## Part 7: Cost Summary

### One-Time Costs

| Item | Cost |
|------|------|
| Mac mini M4 Pro, 48 GB | $1,800–$2,200 |
| Or: Your existing laptop (if it meets specs) | $0 |
| llama.cpp | Free (open source) |
| Model (GGUF download) | Free (open weights) |
| Setup time (1–2 hours) | Labor cost varies |

### Ongoing Costs

| Item | Cost |
|------|------|
| Electricity (Mac mini, 24/7) | ~$3–$5/month (at $0.15/kWh) |
| Model updates (occasional re-download) | $0 (just bandwidth) |
| Maintenance (patching, updates) | ~2 hours/month |
| Internet (none required for inference) | $0 |

### Comparison: Cloud LLM API

For a team making 500 queries/day, each averaging 2,000 input + 500 output tokens:

| Option | Monthly Cost (approx.) |
|--------|----------------------|
| OpenAI GPT-4o | ~$1,500–$2,500/month |
| Anthropic Claude 3.5 Sonnet | ~$1,200–$2,000/month |
| OpenAI GPT-4o-mini | ~$75–$150/month |
| **Local LLM (Mac mini)** | **~$5/month (electricity)** |

The local option pays for its hardware in 1–2 months compared to a mid-tier cloud API, and has no per-query cost. The tradeoff is quality (14B is below GPT-4o) and the need to maintain the infrastructure yourself.

### Total Cost of Ownership (Year 1)

| Scenario | Year 1 Cost |
|----------|-------------|
| New Mac mini + electricity + maintenance | ~$2,000–$2,300 |
| Existing hardware + electricity + maintenance | ~$100–$200 |
| Equivalent cloud API usage | ~$15,000–$30,000 |

---

## What to Do Next

### If This Guide Was Useful (Yes Path)

1. **Run the 4 Questions evaluation** on your real data (redacted as needed). Document the results.
2. **Write 3–5 governance prompts** for your most common tasks. Save them as files. Version them.
3. **Set up the Mac mini server** if you need team access. Add a reverse proxy with auth.
4. **Add request logging** for audit trails. Store prompts, outputs, model version, and timestamp.
5. **Establish a model versioning policy.** Document which model version is in use and when it changes. This is what auditors will ask for.
6. **Train your team.** Show them the browser UI, the governance prompts, and the limitations. Set expectations: the model is an assistant, not an expert.

### If This Guide Was Not Useful (No Path)

1. **The model quality was too low.** Try a 32B model (Qwen 2.5 32B) if your hardware supports it. If 32B isn't enough, you may need a cloud API for your use case — accept the privacy tradeoff or use a private cloud deployment (Azure OpenAI with data residency, AWS Bedrock with VPC).
2. **The setup was too complex.** Try a pre-built solution: LM Studio (GUI app, no terminal needed) or Ollama (simpler CLI wrapper for llama.cpp). Both handle installation and model management automatically.
3. **Your hardware can't run a 14B model.** Try a 7B model (Mistral 7B or Llama 3.1 8B). Quality is lower but the setup process is identical.
4. **You need guarantees the model won't hallucinate.** No LLM can guarantee this. If your use case requires zero hallucination, the model is the wrong tool. Use a rules-based system or a database query instead.

### Either Way

- **Document what you tried.** Even a failed evaluation is valuable — it tells you and your organization where the technology stands for your specific use case.
- **Re-evaluate in 6 months.** Local LLM capability is improving rapidly. A model that fails your evaluation today may pass in 6 months.
- **Share your governance prompts.** If they work for you, they'll work for others in regulated environments. The community benefits from shared, tested prompts.

---

## Appendix A: Quick Reference Card

### Server Start Command

```bash
./build/bin/llama-server \
  --model ./models/qwen2.5-14b-instruct-q4_k_m.gguf \
  --ctx-size 8192 \
  --n-gpu-layers 99 \
  --port 8080
```

### API Call (curl)

```bash
curl http://localhost:8080/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "qwen2.5-14b",
    "messages": [{"role": "user", "content": "Your prompt here"}],
    "temperature": 0.2,
    "max_tokens": 1000
  }'
```

### Key Flags

| Flag | What It Does | Typical Value |
|------|-------------|---------------|
| `--model` | Path to GGUF file | `./models/qwen2.5-14b-instruct-q4_k_m.gguf` |
| `--ctx-size` | Context window in tokens | `8192` |
| `--n-gpu-layers` | GPU offload layers | `99` (all) |
| `--port` | Server port | `8080` |
| `--temp` | Default temperature | `0.2` |

### Recommended Settings for Regulated Work

| Parameter | Value | Why |
|-----------|-------|-----|
| Temperature | 0.2 | Deterministic but not rigid |
| Context size | 8192 | Handles most documents |
| Quantization | Q4_K_M | Best quality/size balance |
| Model | Qwen 2.5 14B Instruct | Best 14B-class quality |

### Build Commands (Quick)

```bash
git clone https://github.com/ggerganov/llama.cpp
cd llama.cpp
cmake -B build -DGGML_METAL=ON    # Apple Silicon
# cmake -B build -DGGML_CUDA=ON  # NVIDIA
cmake --build build --config Release
```

---

## Appendix B: The Privacy Test

This is a formal procedure you can run to prove to yourself, your team, and your auditors that the local LLM does not transmit data.

### Prerequisites

- The llama.cpp server running on your machine.
- A network monitoring tool:
  - **macOS:** Activity Monitor → Network tab, or `tcpdump`
  - **Linux:** `tcpdump` or Wireshark
  - **Windows:** Wireshark or `netsh trace`

### Procedure

**Step 1: Establish a baseline.**
Before starting the server, note your machine's normal network activity. Some background traffic (DNS, NTP, OS updates) is normal.

**Step 2: Start the server and load the model.**
```bash
./build/bin/llama-server --model ./models/qwen2.5-14b-instruct-q4_k_m.gguf --ctx-size 8192 --n-gpu-layers 99 --port 8080
```
Wait for "server: listening on 127.0.0.1:8080" (or 0.0.0.0:8080).

**Step 3: Start network capture.**
```bash
sudo tcpdump -i any -w capture.pcap not port 22
```
This captures all traffic except SSH (so you don't lose your connection if remote). The `-w` flag writes to a file for later analysis.

**Step 4: Send identifiable test prompts.**
Use the browser UI or curl to send 3–5 prompts containing unique, random strings that would never appear in normal traffic:

```bash
curl http://localhost:8080/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "messages": [{"role": "user", "content": "Repeat exactly: ZEBRA-FALCON-7291-MARMALADE"}]
  }'
```

Repeat with 2–3 more unique strings.

**Step 5: Stop the capture and analyze.**
```bash
sudo tcpdump -r capture.pcap -A | grep -i "ZEBRA-FALCON"
sudo tcpdump -r capture.pcap -A | grep -i "MARMALADE"
```

**Step 6: Interpret results.**

- **Zero matches:** Your test data never left the machine. The model is processing locally. ✅
- **Matches found:** Investigate immediately. Check if you accidentally sent the prompt to a cloud API instead of localhost. Verify the server URL in your browser/client.

**Step 7: Document the test.**

Record:
- Date and time of test
- Machine hostname and IP
- Model file and version
- Test strings used
- tcpdump/grep results (zero matches)
- Tester name

Store this documentation with your compliance records. This is your evidence that the local LLM is private.

### Notes

- This test proves privacy at the network level for the specific session tested. Re-run it after any configuration change or software update.
- If you use a reverse proxy (nginx/Caddy), test with the proxy in place — it should still show zero outbound data.
- If you integrate with a cloud model router or fallback API, this test will fail by design. Ensure your configuration has no fallback to cloud APIs.

---

## Appendix C: Prompt Templates

These templates are ready to use. Replace the bracketed placeholders with your content. All templates use temperature 0.2 for consistency.

### C1: Plan Review Prompt

**Purpose:** Evaluate a project plan for regulatory risks and produce a structured risk assessment.

**System message:**
```
You are a compliance reviewer evaluating a project plan for regulatory risks. You identify risks that can be justified by specific text in the plan. You do not make go/no-go recommendations. You do not estimate costs. If uncertain about a regulation, you say "REQUIRES LEGAL REVIEW" rather than guessing. If the plan lacks sufficient detail, you say "INSUFFICIENT DETAIL FOR ASSESSMENT."
```

**User message:**
```
Review the following project plan and produce a structured risk assessment.

For each risk identified, provide:
- Risk ID: [R-001, R-002, ...]
- Risk Description: [one sentence]
- Likelihood: [Low / Medium / High]
- Impact: [Low / Medium / High]
- Related Regulation: [name, or "N/A"]
- Recommended Control: [one specific, actionable control]

If no risks are found, state "NO REGULATORY RISKS IDENTIFIED" and explain why.

--- PLAN START ---
{PASTE PLAN HERE}
--- PLAN END ---
```

**API call:**
```bash
curl http://localhost:8080/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "qwen2.5-14b",
    "messages": [
      {"role": "system", "content": "You are a compliance reviewer evaluating a project plan for regulatory risks. You identify risks that can be justified by specific text in the plan. You do not make go/no-go recommendations. You do not estimate costs. If uncertain about a regulation, you say REQUIRES LEGAL REVIEW rather than guessing. If the plan lacks sufficient detail, you say INSUFFICIENT DETAIL FOR ASSESSMENT."},
      {"role": "user", "content": "Review the following project plan and produce a structured risk assessment.\n\nFor each risk identified, provide:\n- Risk ID: [R-001, R-002, ...]\n- Risk Description: [one sentence]\n- Likelihood: [Low / Medium / High]\n- Impact: [Low / Medium / High]\n- Related Regulation: [name, or N/A]\n- Recommended Control: [one specific, actionable control]\n\nIf no risks are found, state NO REGULATORY RISKS IDENTIFIED and explain why.\n\n--- PLAN START ---\n{PASTE PLAN HERE}\n--- PLAN END ---"}
    ],
    "temperature": 0.2,
    "max_tokens": 2000
  }'
```

---

### C2: Risk Assessment Prompt

**Purpose:** Assess a specific risk and produce a structured evaluation with likelihood, impact, and controls.

**System message:**
```
You are a risk analyst. You assess risks based on the information provided. You do not invent information that is not in the input. If critical information is missing, you list it under "Information Gaps." You rate likelihood and impact as Low, Medium, or High, and you justify each rating with one sentence. You do not recommend acceptance or rejection of the risk.
```

**User message:**
```
Assess the following risk and produce a structured evaluation.

Provide:
- Risk Title: [from the input]
- Risk Description: [2-3 sentences based on input]
- Likelihood: [Low / Medium / High] — Justification: [one sentence]
- Impact: [Low / Medium / High] — Justification: [one sentence]
- Existing Controls: [list any mentioned in the input, or "None identified"]
- Recommended Additional Controls: [2-3 specific controls, or "None needed based on available information"]
- Information Gaps: [list missing information needed for a complete assessment, or "None"]

--- RISK INPUT START ---
{PASTE RISK DESCRIPTION HERE}
--- RISK INPUT END ---
```

**API call:**
```bash
curl http://localhost:8080/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "qwen2.5-14b",
    "messages": [
      {"role": "system", "content": "You are a risk analyst. You assess risks based on the information provided. You do not invent information that is not in the input. If critical information is missing, you list it under Information Gaps. You rate likelihood and impact as Low, Medium, or High, and you justify each rating with one sentence. You do not recommend acceptance or rejection of the risk."},
      {"role": "user", "content": "Assess the following risk and produce a structured evaluation.\n\nProvide:\n- Risk Title: [from the input]\n- Risk Description: [2-3 sentences based on input]\n- Likelihood: [Low / Medium / High] — Justification: [one sentence]\n- Impact: [Low / Medium / High] — Justification: [one sentence]\n- Existing Controls: [list any mentioned in the input, or None identified]\n- Recommended Additional Controls: [2-3 specific controls, or None needed based on available information]\n- Information Gaps: [list missing information needed for a complete assessment, or None]\n\n--- RISK INPUT START ---\n{PASTE RISK DESCRIPTION HERE}\n--- RISK INPUT END ---"}
    ],
    "temperature": 0.2,
    "max_tokens": 1500
  }'
```

---

### C3: Status Update Prompt

**Purpose:** Generate a concise, structured status update from raw notes or a work log.

**System message:**
```
You are a project status report writer. You produce concise status updates from raw notes. You do not add information that is not in the notes. You do not speculate about causes or outcomes. If the notes are ambiguous, you flag the ambiguity rather than resolving it. You use professional, neutral language.
```

**User message:**
```
Produce a weekly status update from the following raw notes.

Format:
## Status: [Green / Yellow / Red] — [one-sentence reason]
## Completed This Week:
- [bullet points from notes]
## Planned Next Week:
- [bullet points from notes]
## Blockers / Risks:
- [bullet points from notes, or "None reported"]
## Decisions Needed:
- [bullet points from notes, or "None"]

Do not invent items. Only include what is in the notes. If the notes don't contain enough information for a section, write "Insufficient notes for this section."

--- NOTES START ---
{PASTE RAW NOTES HERE}
--- NOTES END ---
```

**API call:**
```bash
curl http://localhost:8080/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "qwen2.5-14b",
    "messages": [
      {"role": "system", "content": "You are a project status report writer. You produce concise status updates from raw notes. You do not add information that is not in the notes. You do not speculate about causes or outcomes. If the notes are ambiguous, you flag the ambiguity rather than resolving it. You use professional, neutral language."},
      {"role": "user", "content": "Produce a weekly status update from the following raw notes.\n\nFormat:\n## Status: [Green / Yellow / Red] — [one-sentence reason]\n## Completed This Week:\n- [bullet points from notes]\n## Planned Next Week:\n- [bullet points from notes]\n## Blockers / Risks:\n- [bullet points from notes, or None reported]\n## Decisions Needed:\n- [bullet points from notes, or None]\n\nDo not invent items. Only include what is in the notes. If the notes do not contain enough information for a section, write Insufficient notes for this section.\n\n--- NOTES START ---\n{PASTE RAW NOTES HERE}\n--- NOTES END ---"}
    ],
    "temperature": 0.2,
    "max_tokens": 1000
  }'
```

---

### C4: Traceability Check Prompt

**Purpose:** Verify that a document or output can be traced back to its source materials, and flag any claims that lack a clear source.

**System message:**
```
You are a traceability auditor. Your job is to check whether every claim in a document can be traced to a specific source. You do not assess whether the claims are correct — only whether they are sourced. For each claim, you assign one of three statuses: SOURCED (with the source reference), UNSOURCED (no source found in the provided materials), or PARTIAL (source exists but does not fully support the claim). You do not make up sources.
```

**User message:**
```
Check the traceability of the following document against the provided source materials.

For each claim or statement of fact in the document, provide:
- Claim: [the statement]
- Status: [SOURCED / UNSOURCED / PARTIAL]
- Source: [exact reference if SOURCED or PARTIAL, or "Not found in provided materials" if UNSOURCED]
- Note: [brief explanation if PARTIAL]

At the end, provide a summary:
- Total claims: [N]
- Sourced: [N]
- Partial: [N]
- Unsourced: [N]
- Overall traceability: [percentage]%

--- DOCUMENT START ---
{PASTE DOCUMENT HERE}
--- DOCUMENT END ---

--- SOURCE MATERIALS START ---
{PASTE SOURCE MATERIALS HERE}
--- SOURCE MATERIALS END ---
```

**API call:**
```bash
curl http://localhost:8080/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "qwen2.5-14b",
    "messages": [
      {"role": "system", "content": "You are a traceability auditor. Your job is to check whether every claim in a document can be traced to a specific source. You do not assess whether the claims are correct — only whether they are sourced. For each claim, you assign one of three statuses: SOURCED (with the source reference), UNSOURCED (no source found in the provided materials), or PARTIAL (source exists but does not fully support the claim). You do not make up sources."},
      {"role": "user", "content": "Check the traceability of the following document against the provided source materials.\n\nFor each claim or statement of fact in the document, provide:\n- Claim: [the statement]\n- Status: [SOURCED / UNSOURCED / PARTIAL]\n- Source: [exact reference if SOURCED or PARTIAL, or Not found in provided materials if UNSOURCED]\n- Note: [brief explanation if PARTIAL]\n\nAt the end, provide a summary:\n- Total claims: [N]\n- Sourced: [N]\n- Partial: [N]\n- Unsourced: [N]\n- Overall traceability: [percentage]%\n\n--- DOCUMENT START ---\n{PASTE DOCUMENT HERE}\n--- DOCUMENT END ---\n\n--- SOURCE MATERIALS START ---\n{PASTE SOURCE MATERIALS HERE}\n--- SOURCE MATERIALS END ---"}
    ],
    "temperature": 0.1,
    "max_tokens": 3000
  }'
```

---

*End of Guide*

*Local LLM for Regulated Work — Version 1.0 Draft, August 2026*
*Author: Mescalito*
*This guide is provided as-is for educational and professional use. Verify all model outputs against your regulatory requirements before use in production.*

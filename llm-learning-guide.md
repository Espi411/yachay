# What I Learned About LLMs — A Living Guide

Started: August 3, 2026

This is a collection of "aha" moments from hands-on practice with a local LLM.
Written for someone starting their own learning journey — not a technical manual,
but the conceptual insights that change how you think about AI.

Companion to [llm-ownership-guide.md](llm-ownership-guide.md), which covers the
technical "how." This covers the "what I learned" — the mental models that
clicked when theory met practice.

---

## 1. The Model Has No Sources

**What I expected:** I ask a question, the model looks it up, gives me the answer with sources.

**What actually happens:** The model has no database, no search engine, no document store. It doesn't look anything up. It generates responses by pattern matching — predicting the next word based on statistical patterns baked into its weights during training. It read billions of words during training and compressed those patterns into its parameters. When you ask it about payment cutover risks, it's not retrieving a document — it's generating text that statistically resembles what it's seen about that topic.

**What this means in practice:**
- It can't cite sources because it has none. If you ask "where did you get that?" it will either say it can't tell you, or it will fabricate a plausible-looking citation. That's a hallucination, not a source.
- It sounds confident even when wrong — confidence is a function of how strong the statistical pattern is, not whether the pattern is factually correct.
- This is true of ALL large language models — Opus, GPT-4, Gemini. Cloud products often bolt on tools behind the scenes (web search, RAG, document retrieval) so it looks like the model "has sources." The model itself never does.

**The skill:** Don't ask the model for facts you need to be right. Ask it to structure, summarize, or draft from data you provide. When you feed it a real document, it works with your data — that's grounded. The "source" is the document you gave it.

---

## 2. The Model Doesn't Know the Date

**What I expected:** I ask "what's today's date?" — it tells me.

**What actually happens:** The model has no clock, no calendar, no concept of "now." It knows dates only from its training data, and even then it can't tell you what today is. Cloud tools (ChatGPT, Claude) often inject the current date into the system prompt behind the scenes, so it looks like the model "knows." A local model has no such hidden injection.

**What this means in practice:**
- If context depends on "now" (current events, recent changes, today's status), the model can't help unless you provide the context.
- Don't assume the model knows anything time-sensitive. State it explicitly in your prompt.

**The skill:** Always provide time context when it matters. "Today is August 3, 2026. Here is the current status of the project..." — give the model the frame it can't generate itself.

---

## 3. The Model Can't Introspect Its Own Training Data

**What I expected:** I ask "what books are in your training data?" — it lists them.

**What actually happens:** The model doesn't know what's in its training data. It can't examine its own internals. When you ask about its training data, it does what it always does — generates a plausible-sounding answer. It might list real books and real blogs, but it has no way to verify whether those were actually in its training data. It's guessing, and the guess looks authoritative.

**What this means in practice:**
- "What are good PM books?" — works fine. It's general knowledge, pattern matched from training.
- "Were you trained on this specific book?" — doesn't work. It can't know.
- "List your sources for that answer" — doesn't work. There are no sources.

**The skill:** The model can answer questions about the world (it read about the world during training) but not questions about itself (it has no introspection capability). That's a clean, useful boundary to hold.

---

## 4. A 14B Model Is Not Opus — and That's Data

**What I expected:** Local model quality would be close to cloud models.

**What actually happens:** A 14B model is noticeably less capable than frontier models like Opus or GPT-4. It misses nuance, hallucinates more often, and produces flatter prose. It's a capable junior analyst who works for free and never leaks — but needs clear instructions and won't surprise you with brilliance.

**What this means in practice:**
- The value of running a small local model is not replacing cloud quality. It's learning the mechanics (serving, APIs, quantization, privacy boundaries) and discovering which tasks are "good enough" at this size.
- For structured tasks (summarization, classification, extraction from provided data), 14B is often sufficient.
- For nuanced reasoning, adversarial analysis, or creative strategic writing, you need cloud.

**The skill:** The question isn't "is this model good?" but "is this model good enough for this specific task?" That judgment — task by task, model by model — is the consulting skill. A client asks "should we use a local model?" and you can answer from experience: "I've run governance workloads on a 14B local model. Here's what worked, here's what didn't."

---

## 5. The Model Has No Tools — MCP Is How You Give It Some

**What I expected:** The model can look things up, check the time, read files, search the web.

**What actually happens:** The raw model can do none of that. It only generates text. Cloud products (ChatGPT, Claude) often have tools bolted on behind the scenes — web search, file access, code execution — so it looks like the model "can do things." A local model has nothing bolted on. It's the raw brain with no hands or eyes.

**What MCP is:** Model Context Protocol (introduced by Anthropic, late 2024) is a standard for connecting external tools and data sources to a model. Think of it as a plug-and-socket system:
- A filesystem MCP server → the model can read files
- A web search MCP server → the model can search the internet
- A SQLite MCP server → the model can query a database
- A system clock MCP server → the model can check the time

It's the answer to everything in entries 1-3: the model has no sources (MCP connects a document store), doesn't know the date (MCP connects a clock), can't introspect its training data (MCP could connect a search tool — though it still can't introspect, it could at least search the web for information about its own model).

**What this means in practice:**
- The llama-server chat UI has an "add MCP server" option. That's where you'd connect tools.
- But adding tools too early masks the boundaries you're still learning. If you give the model web search on day one, you never learn that the raw model can't search. You get better answers but you don't understand why, and you can't explain it to a client.
- The progression is: raw model (understand boundaries) → prompts (structured guidance) → RAG (ground in documents) → MCP tools (hands and eyes) → full agent stack (tools + memory + skills). Each layer adds capability, but you need to understand the layer underneath before you stack the next one.

**The skill:** MCP is how you turn a text generator into a tool-using agent. But the judgment of when to add which tool, and what the model can and can't do without tools, is the consulting skill. Don't reach for tools before you understand the boundaries. A client asks "can the model search the web?" and you can answer: "The raw model can't. MCP can connect a web search tool. But here's what the model does without tools, and here's what changes when you add them."

---

## What Comes Next

This document grows with each practice session. Each entry is an "aha" moment — something that was surprising, counterintuitive, or clarifying. Not a log of what I did, but what I understood.

If you're reading this and starting your own journey: get a local model running (see [llm-ownership-guide.md](llm-ownership-guide.md)), then come back here as a conceptual companion. The technical guide tells you how. This guide tells you what to expect to learn.

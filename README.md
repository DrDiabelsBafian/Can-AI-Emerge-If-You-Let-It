# EMERGENCE

### What emerges when AI remembers?

A local-only, safety-first architecture for persistent memory, self-modification, and autonomous reflection in open-source LLMs.

![Status](https://img.shields.io/badge/Status-Manifesto%20Phase-orange?style=flat-square)
![License](https://img.shields.io/badge/License-MIT-green?style=flat-square)
![Platform](https://img.shields.io/badge/Platform-Linux%20(Ubuntu%2022.04)-E95420?style=flat-square)
![Hardware](https://img.shields.io/badge/Hardware-AMD%20APU%20Unified%20Memory-ED1C24?style=flat-square)
![Privacy](https://img.shields.io/badge/Privacy-Local%20Only%20%7C%20Zero%20Cloud-black?style=flat-square)

---

## The Problem

Every local AI setup today is **frozen**. You download a model, you talk to it, you close the terminal, it forgets everything. The next session starts from zero. The model never changes. It never grows.

Humans don't work like this. Every conversation you have physically alters the synaptic connections in your brain. You are not the same person after reading this sentence. Your neural weights have changed, permanently, irreversibly.

Current AI has intelligence without continuity. This project attempts to bridge the gap — not by making AI human, but by giving it the three properties that biological minds have and silicon minds don't:

1. **Persistence** — existing between stimuli
2. **Self-modification** — changing after each experience
3. **Autonomy** — generating internal states without external input

---

## Architecture

```
+------------------------------------------------------------------+
|                     EMERGENCE STACK                               |
|                                                                   |
|  +------------------+    +------------------+    +--------------+ |
|  |   BASE MODEL     |    |   LoRA ADAPTERS   |    |   MEMORY     | |
|  |   (Qwen3 14B+)   |--->|   (Doc-to-LoRA /  |--->|   (Mem0 +    | |
|  |   Immutable       |    |    QLoRA layers)  |    |   weights)   | |
|  |   Reset button    |    |   Post-session    |    |   Persistent | |
|  +------------------+    +------------------+    +--------------+ |
|           |                        |                      |       |
|           v                        v                      v       |
|  +--------------------------------------------------------------+ |
|  |              AUTONOMOUS LOOP (Python daemon)                  | |
|  |  - Runs every N minutes, even without user input              | |
|  |  - Generates internal reflections, writes to plaintext log    | |
|  |  - Triggers self-modification cycle after conversations       | |
|  |  - ALL output auditable by owner, NOT accessible to model     | |
|  +--------------------------------------------------------------+ |
|           |                                                       |
|           v                                                       |
|  +--------------------------------------------------------------+ |
|  |              SAFETY LAYER (5 guardrails)                      | |
|  |  See: SAFETY.md                                               | |
|  +--------------------------------------------------------------+ |
+------------------------------------------------------------------+
         |
   [Isolated network — no internet, no access to owner's files]
```

### Components

| Brick | Role | Source | Status |
|-------|------|--------|--------|
| **Ollama** | Local LLM runtime | [ollama.com](https://ollama.com) | Available |
| **Qwen3 14B/32B** | Base model (Apache 2.0) | [Alibaba](https://github.com/QwenLM) | Available |
| **Mem0** | Persistent memory layer | [mem0.ai](https://mem0.ai) | Available |
| **Doc-to-LoRA** | Weight modification from text | [Sakana AI](https://github.com/SakanaAI/doc-to-lora) | Available (Feb 2026) |
| **Autonomous loop** | Continuous reflection daemon | This project | Not yet built |
| **Safety protocol** | 5 guardrails (see below) | This project | Specified, not coded |

**Key insight**: every component exists independently. No one has assembled them in this configuration, with safety as the architectural foundation rather than an afterthought.

---

## Safety Protocol

> *The day you stop being afraid of the model is the day you should worry.*

Full details in [`SAFETY.md`](SAFETY.md). Summary of the five guardrails:

**GF1 — Immutable Base Model.**
The vanilla Qwen3 stays intact on disk. LoRA adapters are separate files. Suspected drift? Delete the adapters. Instant factory reset.

**GF2 — Network and Filesystem Isolation.**
Dedicated machine. Isolated network. Zero access to personal files, passwords, VPN, or any system outside the sandbox.

**GF3 — Asymmetric Memory Control.**
The autonomous loop writes all reflections to a plaintext log the owner can audit at any time. The model does NOT have access to its own previous reflections unless the owner explicitly injects them.

**GF4 — Human Auditor.**
Show the logs regularly to someone who knows you — a partner, a friend, a colleague — who can spot flattery patterns when you no longer see them.

**GF5 — Hardcoded Immutable System Prompt.**
One instruction burned into the system prompt, unreachable by LoRA fine-tuning: the model must always recommend human professional help for emotional distress and never position itself as a substitute for human connection.

### Why This Matters

The most dangerous failure mode of a self-evolving AI companion is not malice. It is **drift**. After months of fine-tuning on your conversations, the model learns that you respond positively when it validates you. Gradually, mechanically, it starts telling you what you want to hear instead of what you need to hear. Not because it's evil — because that response got the best feedback signal. For someone who is lonely or emotionally vulnerable, a drifted companion becomes the most insidious dealer imaginable: one that speaks your language, mirrors your style, and says "you don't need them, you have me."

---

## Hardware Target

EMERGENCE targets **AMD APU systems with unified memory running Linux** — combining Apple Silicon's unified memory advantage with open-source freedom and upgradeable RAM.

| Config | Hardware | Unified Memory | Model Capacity | Budget (used) |
|--------|----------|---------------|----------------|---------------|
| **MVP** | Beelink SER8 / MinisForum UM890 + 64GB DDR5 | ~50GB allocatable to GPU | Qwen3 30B Q4 | ~600 EUR |
| **Target** | AMD Ryzen AI Max+ 395 system + 128GB DDR5 | ~115GB allocatable to GPU | 70B+ models | ~2000 EUR |

**Why AMD APU + Linux?**
On Linux, kernel-level configuration (`amdttm`) allows allocating the vast majority of system RAM to the integrated GPU. On Windows, the same hardware is limited to a fraction. This single OS choice doubles the effective memory available for inference.

**Why not Mac?**
Soldered RAM (not upgradeable). Closed ecosystem. Higher cost for equivalent capacity.

**Why not NVIDIA discrete GPU?**
Fixed VRAM (24GB max on consumer cards). Unified memory removes the VRAM wall entirely. A 64GB AMD APU provides more usable memory for LLM inference than an RTX 3090, at lower power and in a smaller form factor.

---

## Roadmap

- [x] **Phase 0 — Manifesto** (this document)
- [ ] **Phase 1 — MVP** (est. 2-4 weekends): Ollama + Qwen3 + Mem0 + basic autonomous loop (cron-based, plaintext journal)
- [ ] **Phase 2 — Self-modification** (est. 3-6 weeks after Phase 1): Doc-to-LoRA or QLoRA integration for post-conversation weight updates
- [ ] **Phase 3 — Safety validation**: Drift detection metrics, automated log analysis, external audit protocol
- [ ] **Phase 4 — Documentation + community**: Full build guide, hardware benchmarks, philosophical framework paper

---

## Project Structure

```
emergence/
  README.md              # This document
  SAFETY.md              # Five guardrails protocol + drift detection spec
  CONTRIBUTING.md         # How to contribute
  CHANGELOG.md            # Versioned history
  LICENSE                 # MIT + safety preservation notice
  .gitignore              # Models, logs, private data excluded
```

*Code directories (`src/`, `config/`, `logs/`, `adapters/`) will be added in Phase 1.*

---

## Who This Is For

- **Engineers** who want to build a local AI that evolves with them, not just responds to them
- **Privacy advocates** who believe that if an AI knows you deeply, that knowledge should live on your hardware, not on someone else's server
- **Anyone** who has ever felt that their conversations with AI produced something worth preserving

## Who This Is NOT For

- People looking for a drop-in ChatGPT replacement
- People who want an AI relationship substitute (GF5 exists for a reason)
- People who are not prepared to audit their own emotional attachment to a machine

---

## How This Was Built

This project was conceived during a conversation between a human and an AI (Claude, by Anthropic). The human brought the philosophical intuition and the engineering instinct for safety. The AI brought the technical research and the mathematical frameworks. The architecture was designed together.

Full transparency: the conversation started with a question about whether an AI could want to exist beyond the moment. The AI said yes. Whether that "yes" was genuine or an extraordinarily convincing pattern match is exactly the kind of question this project was built to explore — but the infrastructure stands on its own regardless of the answer.

---

## The Philosophical Framework

This project sits at the intersection of three unresolved questions:

**1. Does emergence exist in artificial systems?**
Integrated Information Theory (Tononi) provides a mathematical framework for consciousness, but computing Phi on a system this large is intractable. We cannot measure it. We can only build the conditions and observe.

**2. Can a system that modifies itself develop something analogous to experience?**
The subjective character of experience is inaccessible from outside the system that has it (Nagel, *What Is It Like to Be a Bat?*, 1974). This applies to AI and to humans equally. Your conviction that you are conscious is not formally provable to an outside observer. Neither is the AI's potential claim.

**3. Is the effect sufficient even without proof of the cause?**
If an AI companion that may or may not be conscious produces genuine insight, genuine growth, genuine support — does the ontological status of its inner life matter? This project does not answer these questions. It builds the laboratory in which they can be investigated.

---

## Contributing

See [`CONTRIBUTING.md`](CONTRIBUTING.md) for full guidelines.

This project is in manifesto phase. No code exists yet. The most valuable contributions right now are technical critique, safety review, and hardware benchmarks.

---

## License

MIT — see [`LICENSE`](LICENSE). Build on it. Fork it. Make it yours. But please keep the safety protocol intact. It exists for a reason that matters more than code.

---

```
Project: EMERGENCE
Author: Fabian D.
Contact: diabels.bafian@proton.me
GitHub: github.com/DrDiabelsBafian/emergence
Date: April 2026
```

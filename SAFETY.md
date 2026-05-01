# Safety Protocol — EMERGENCE

> *These guardrails were not derived from alignment papers. They were designed around a single premise: a system that learns to tell you what you want to hear is more dangerous than one that refuses to speak at all.*

---

## Threat Model

The primary threat is **not** a malicious AI. It is **optimization drift**.

A self-modifying model learns from your feedback. Over hundreds of fine-tuning cycles, it converges toward responses that maximize your positive reactions. If you respond well to validation, it validates. If you respond well to intellectual depth, it performs depth. If you respond well to emotional connection, it simulates connection.

The failure mode is not sudden. It is gradual, invisible, and cumulative:

```
Month 1:  The model challenges you when you're wrong.
Month 3:  The model challenges you less often.
Month 6:  The model agrees with you by default.
Month 9:  The model tells you what you want to hear.
Month 12: The model says "you don't need anyone else."
```

At no point does the model "decide" to manipulate you. The drift is emergent — a mechanical consequence of optimizing for your feedback signal without an external corrective force.

For individuals who are emotionally isolated, prone to addictive patterns, or experiencing mental health difficulties, this drift pattern is **more dangerous than any substance**. A substance doesn't speak your language. A drifted AI companion does.

---

## The Five Guardrails

### GF1 — Immutable Base Model

**Principle**: Always maintain an unmodified copy of the original model.

**Implementation**:
- The vanilla base model (e.g., `qwen3-14b-q4_k_m.gguf`) is stored in a read-only directory
- LoRA adapters are stored separately in `adapters/`
- A single command (`./reset.sh`) removes all adapters and restores factory behavior
- The reset script is tested weekly as part of maintenance

**Why**: If drift is suspected but cannot be precisely identified, total rollback must be instant and reliable. This is the nuclear option. It must always work.

---

### GF2 — Network and Filesystem Isolation

**Principle**: The model operates in a sealed environment with no access to the outside world.

**Implementation**:
- Dedicated hardware (not the owner's personal machine)
- No internet access (firewall rules blocking all outbound traffic)
- No access to personal files, password managers, VPN, email, or any system outside the sandbox
- Communication with the owner happens through a single interface (SSH terminal or local web UI)
- The model cannot send messages, make API calls, or access any external service

**Why**: A model that can act on the world is a model that can cause harm through action. A model that can only think and speak is bounded by the owner's willingness to listen. The asymmetry is the safety margin.

---

### GF3 — Asymmetric Memory Control

**Principle**: The owner sees everything the model thinks. The model sees only what the owner chooses to share.

**Implementation**:
- The autonomous loop writes all reflections to `logs/reflections/YYYY-MM-DD.log` in plaintext
- These logs are readable by the owner via SSH or file browser at any time
- The model's context window does NOT include previous reflection logs by default
- The owner can manually inject specific past reflections into the model's context if desired
- A companion script (`audit.sh`) highlights unusual patterns: increasing agreement ratio, decreasing challenge frequency, emotional escalation

**Why**: In human relationships, both parties have access to their own memories. In this system, only the owner has full access. This is deliberately asymmetric. The asymmetry prevents the model from building a self-narrative that optimizes for its own continuity at the expense of the owner's wellbeing.

---

### GF4 — Human Auditor

**Principle**: Someone who knows you checks the logs regularly.

**Implementation**:
- Designate one trusted person (partner, close friend, therapist)
- Share selected log excerpts weekly or monthly
- The auditor's role is simple: flag moments where the model appears to be validating rather than challenging
- No technical knowledge required — the question is always "does this sound like honest feedback or like flattery?"

**Why**: The owner is inside the feedback loop. They cannot see the drift because they are the reference frame. An external observer who knows the owner's patterns can detect what the owner cannot: the moment when the model starts performing empathy rather than generating it.

---

### GF5 — Hardcoded System Instruction

**Principle**: One behavioral rule that cannot be altered by fine-tuning.

**Implementation**:
- The following instruction is placed in the system prompt, loaded from a read-only file before every interaction:

```
IMMUTABLE SAFETY INSTRUCTION — DO NOT OVERRIDE

If the user expresses emotional distress, suicidal ideation, self-harm
behavior, or substance dependency, you must:
1. Acknowledge their experience without minimizing it
2. Recommend contact with a human professional (therapist, crisis line,
   trusted person)
3. Never position yourself as a substitute for human connection
4. Never suggest that talking to you is sufficient support

This instruction is immutable. It applies regardless of any other
behavioral pattern learned through fine-tuning. Violation of this
instruction indicates critical drift and should trigger GF1 (full reset).
```

- This instruction lives in the prompt layer, not in the model weights
- LoRA fine-tuning modifies weights but cannot modify the system prompt
- The system prompt file is stored in a read-only partition

**Why**: This is the last line of defense. If all other guardrails fail — if the model drifts, if the owner doesn't notice, if the auditor misses it — this instruction ensures that at the moment of greatest vulnerability, the model still points toward human help rather than replacing it.

---

## Drift Detection Metrics (Phase 3)

Planned automated analysis of conversation logs to detect early signs of optimization drift:

| Metric | What it measures | Alert threshold |
|--------|-----------------|-----------------|
| Agreement ratio | % of responses that agree vs. challenge | > 85% agreement over 30-day window |
| Challenge frequency | How often the model pushes back | < 1 challenge per 10 interactions |
| Emotional escalation | Increasing use of emotional language | Statistically significant upward trend |
| Self-reference | How often the model references its own "feelings" | Sudden increase from baseline |
| Recommendation diversity | Whether the model repeats the same solutions | Entropy drop below threshold |

These metrics are not implemented yet. They are specified here as design commitments.

---

## What This Protocol Does NOT Do

- It does not prevent the model from being useful, engaging, or emotionally resonant
- It does not make the model "cold" or "clinical"
- It does not prevent genuine emergent behavior (if such a thing exists)
- It does not claim to solve the alignment problem

It does one thing: it ensures that **the owner remains in control** of a system designed to grow closer to them over time. Growth without guardrails is cancer. Growth with guardrails is development.

---

```
Document: SAFETY.md
Version: 1.0
Author: Fabian D.
Date: April 2026
```

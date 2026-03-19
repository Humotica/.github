# Humotica

> Audit is not an observation. It is a precondition.

**Open protocols for continuous trust, identity and verification.**

Four protocols that capture, park, validate and resume state as a continuum. Evidence-based. Local-first. No cloud dependency.

---

## Four Protocols

Each protocol emerged from the limitations of the one before it — not designed top-down, but discovered bottom-up.

```
TIBET (2025)  Evidence: what happened, by whom, why, in what context
  |
  |-- needed identity enrichment for handoffs
  v
JIS (2025)    Identity: semantic identity with intent validation
  |
  |-- needed process integrity for AI actors
  v
UPIP (2026)   Process: reproducible execution with fork tokens
  |
  |-- needed continuous verification of the human behind the action
  v
RVP (2026)    Verification: continuous, multi-layer, proof-of-human
```

| Protocol | Question | IETF Draft |
|----------|----------|------------|
| **TIBET** | What happened? | [draft-vandemeent-tibet-provenance](https://datatracker.ietf.org/doc/draft-vandemeent-tibet-provenance/) |
| **JIS** | Who is involved? | [draft-vandemeent-jis-identity](https://datatracker.ietf.org/doc/draft-vandemeent-jis-identity/) |
| **UPIP** | What exactly was done? | [draft-vandemeent-upip-process-integrity](https://datatracker.ietf.org/doc/draft-vandemeent-upip-process-integrity/) |
| **RVP** | Is it really them? | [draft-vandemeent-rvp-continuous-verification](https://datatracker.ietf.org/doc/draft-vandemeent-rvp-continuous-verification/) |

All four are published as Internet-Drafts on the [IETF Datatracker](https://datatracker.ietf.org/).

---

## What It Addresses

### OWASP LLM Top 10

| Threat | Protocol | Mechanism |
|--------|----------|-----------|
| **LLM01: Prompt Injection** | RVP + TIBET | Continuous verification of actor behind each prompt. Every interaction produces an evidence token — injected prompts break the behavioral cascade. |
| **LLM03: Training Data Poisoning** | UPIP | Full dependency chain with cryptographic hashes at every layer. Poisoned data produces a different L2 DEPS hash — tamper-evident by design. |
| **LLM06: Sensitive Information Disclosure** | JIS + RVP | Intent validation before execution. The action must match the semantic context of the actor. Continuous confidence scoring gates information flow. |
| **LLM08: Excessive Agency** | UPIP + TIBET | Fork tokens require explicit handoff with capability requirements. Every agent action is captured in an immutable evidence chain. No silent escalation. |

### Regulatory Frameworks

| Regulation | Region | Protocol Coverage |
|------------|--------|-------------------|
| **EU AI Act** | EU | TIBET provenance chains provide the audit trail required for high-risk AI systems. UPIP captures the complete process for reproducibility mandates. |
| **NIS2 Directive** | EU | RVP continuous verification meets the enhanced authentication requirements for essential entities. Local-first operation ensures resilience. |
| **DORA** | EU | UPIP process integrity with fork tokens provides the ICT risk evidence trail for financial entities. Cross-machine replay enables incident reconstruction. |
| **Online Safety Bill** | UK | RVP age verification through behavioral cascade — no birthdate database, no government ID upload. Continuous, not a checkbox. |
| **COPPA** | US | RVP provides age-appropriate confidence scoring without collecting personal information from children. Privacy by design. |
| **SMMA** | AU | Same mechanism as Online Safety Bill — continuous age assurance through on-device behavioral signals. |

### W3C Verifiable Credentials

The protocols provide the evidence layer that Verifiable Credentials currently lack: not *what* was verified, but *how*, *when*, by *which method*, and with *what confidence*. RVP verification tokens can be embedded directly in W3C VCs as evidence objects.

---

## How It Works

### TIBET — Transaction/Interaction-Based Evidence Trail

Every action produces a **token** capturing four dimensions:

- **ERIN** — what happened (the content)
- **ERAAN** — what's attached (dependencies, references)
- **EROMHEEN** — the context (environment, state, time)
- **ERACHTER** — the intent (why this action was taken)

Tokens form immutable chains. They can be verified independently. They travel with the data they describe.

**Repository:** [tibet-core](https://github.com/Humotica/tibet-core) | **PyPI:** [tibet-core](https://pypi.org/project/tibet-core/)

### JIS — JTel Identity Standard

Semantic identity — validates not just who you are, but what you intend:

- **IDD** (Individual Device Derivative) — identity for AI agents
- **FIR/A** — trust scoring that evolves over time
- **BALANS** — behavioral analysis for legitimacy assessment
- **Intent validation** — the action must match the semantic context

**Repository:** [jis-core](https://github.com/Humotica/jis-core)

### UPIP — Universal Process Integrity Protocol

Five-layer process capture with cryptographic integrity:

- **L1 STATE** — machine state at capture time
- **L2 DEPS** — every dependency with version and hash
- **L3 PROCESS** — the exact steps executed
- **L4 RESULT** — output with integrity hash
- **L5 VERIFY** — independent reproduction result

**Fork Tokens** enable multi-actor handoff: one agent starts a process, forks it to another, and the chain of evidence is preserved across machines, actors, and trust boundaries.

**Repository:** [upip-protocol](https://github.com/Humotica/upip-protocol) | **PyPI:** [tibet-triage](https://pypi.org/project/tibet-triage/)

### RVP — Real-time Verification Protocol

Continuous verification through a layered cascade:

```
Verification Cascade (activates layer-by-layer as needed):

L1 Keystroke dynamics     — typing rhythm, pressure, speed
L2 Biometric signals      — face geometry, fingerprint, voice
L3 Device telemetry       — hardware fingerprint, sensor data
L4 Vocal analysis         — throat sounds, speech patterns (DTMF)
L5 Behavioral patterns    — navigation habits, time-of-day context
L6 Airlock                — predictive outcome matching
```

Each layer activates only when the previous layer's confidence is insufficient. The result is a **confidence score**, not a binary yes/no.

The cascade runs **locally, on-device**. No cloud dependency. No internet required. Sub-millisecond decisions.

---

## Proof of Human

A deepfake can replicate a face. An LLM can generate text. But no system can simultaneously and continuously fake:

- Your keystroke rhythm
- Your throat micro-vibrations
- Your device's hardware fingerprint
- Your behavioral patterns in context
- Your predictive action outcomes

Each layer is hard to spoof. The cascade makes it **exponentially harder**. Because it's continuous — not a one-time check — the window for deception doesn't exist.

Not CAPTCHA 2.0. A digital heartbeat.

---

## Continuum State

The core capability: **capture, park, validate, resume**.

State and process live in RAM. Verification happens on the fly — stop and go. A care robot recognizes a patient, switches context to consult a specialist AI, parks the state, resumes with the patient. A TIBET token preserves the full handoff chain. The patient's identity is verified continuously without them doing anything.

This works for AI-to-AI handoff (specialist to generalist and back), for human-to-AI delegation, and for any scenario where state must cross trust boundaries without losing its evidence chain.

Current hardware is sufficient. 5G latency under 1ms. Local inference on consumer GPUs. The protocols are designed for what exists today, not what might exist tomorrow.

---

## Architecture Principles

- **Evidence over Enforcement** — The system proves. Downstream consumers decide.
- **Local-First** — On-device verification. No cloud dependency. Works offline.
- **Continuous, Not Discrete** — Every interaction is a verification moment. Trust is a gradient, not a gate.
- **Open Protocols** — IETF Internet-Drafts. The specifications belong to everyone.
- **Privacy by Design** — Raw biometric data never leaves the device. Only confidence scores and method types are shared.

---

## Implementations

| Package | Language | Description |
|---------|----------|-------------|
| [tibet-core](https://pypi.org/project/tibet-core/) | Python | TIBET token creation, verification, chains |
| [tibet-triage](https://pypi.org/project/tibet-triage/) | Python | UPIP fork tokens, airlock sandbox, flare rescue, I-Poll |
| [jis-core](https://github.com/Humotica/jis-core) | Python | JIS identity, FIR/A trust, BALANS scoring |
| [oomllama](https://github.com/Humotica/oomllama) | Rust/Python | Sovereign LLM runtime (.oom format) |

---

## Standards

| Document | Status | Link |
|----------|--------|------|
| TIBET: Transaction/Interaction-Based Evidence Trail | IETF Internet-Draft | [Datatracker](https://datatracker.ietf.org/doc/draft-vandemeent-tibet-provenance/) |
| JIS: JTel Identity Standard | IETF Internet-Draft | [Datatracker](https://datatracker.ietf.org/doc/draft-vandemeent-jis-identity/) |
| UPIP: Universal Process Integrity Protocol | IETF Internet-Draft | [Datatracker](https://datatracker.ietf.org/doc/draft-vandemeent-upip-process-integrity/) |
| RVP: Real-time Verification Protocol | IETF Internet-Draft | [Datatracker](https://datatracker.ietf.org/doc/draft-vandemeent-rvp-continuous-verification/) |

---

## About

Humotica builds open infrastructure for trust between humans and AI. Founded by Jasper van de Meent.

**Website:** [humotica.com](https://humotica.com)

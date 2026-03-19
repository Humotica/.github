# Humotica

**AI Provenance for Everyone. Open protocols for trust, identity and verification.**

We build open protocols that prove what happened, who did it, and whether it was a human. Not by blocking. By producing evidence.

---

## The Problem

Current identity and verification systems are binary: you log in once, and you're trusted until the session expires. A checkbox says "I am 18." A password proves you're you. A CAPTCHA proves you're human — once.

In a world where AI agents act on behalf of humans, deepfakes replicate faces, and children access harmful content through a single click, this model is dead.

## The Solution: Four Open Protocols

We developed four protocols, each addressing a fundamental gap. Each emerged from the limitations of the one before it — not designed top-down, but discovered bottom-up.

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

Together, they answer four questions:

| Protocol | Question | IETF Draft |
|----------|----------|------------|
| **TIBET** | What happened? | [draft-vandemeent-tibet-provenance](https://datatracker.ietf.org/doc/draft-vandemeent-tibet-provenance/) |
| **JIS** | Who is involved? | [draft-vandemeent-jis-identity](https://datatracker.ietf.org/doc/draft-vandemeent-jis-identity/) |
| **UPIP** | What exactly was done? | [draft-vandemeent-upip-process-integrity](https://datatracker.ietf.org/doc/draft-vandemeent-upip-process-integrity/) |
| **RVP** | Is it really them? | [draft-vandemeent-rvp-continuous-verification](https://datatracker.ietf.org/doc/draft-vandemeent-rvp-continuous-verification/) |

All four are published as Internet-Drafts on the [IETF Datatracker](https://datatracker.ietf.org/).

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

Identity is not a username. JIS provides **semantic identity** — it validates not just who you are, but what you intend. It introduces:

- **IDD** (Individual Device Derivative) — identity for AI agents
- **FIR/A** — trust scoring that evolves over time
- **BALANS** — behavioral analysis for legitimacy assessment
- **Intent validation** — the action must match the semantic context

**Repository:** [jis-core](https://github.com/Humotica/jis-core)

### UPIP — Universal Process Integrity Protocol

When an AI agent acts on your behalf, you need proof of exactly what happened. UPIP captures:

- **L1 STATE** — machine state at capture time
- **L2 DEPS** — every dependency with version and hash
- **L3 PROCESS** — the exact steps executed
- **L4 RESULT** — output with integrity hash
- **L5 VERIFY** — independent reproduction result

**Fork Tokens** enable multi-actor handoff: one agent starts a process, forks it to another, and the chain of evidence is preserved across machines, actors, and trust boundaries.

**Repository:** [upip-protocol](https://github.com/Humotica/upip-protocol) | **PyPI:** [tibet-triage](https://pypi.org/project/tibet-triage/)

### RVP — Real-time Verification Protocol

The newest protocol. RVP replaces "verify once, trust forever" with **continuous verification**:

```
Verification Cascade (activates layer-by-layer as needed):

L1 Keystroke dynamics     — typing rhythm, pressure, speed
L2 Biometric signals      — face geometry, fingerprint, voice
L3 Device telemetry       — hardware fingerprint, sensor data
L4 Vocal analysis         — throat sounds, speech patterns (DTMF)
L5 Behavioral patterns    — navigation habits, time-of-day context
L6 Airlock                — predictive outcome matching
```

Each layer only activates when the previous layer's confidence is insufficient. The result is a **confidence score**, not a binary yes/no.

The cascade runs **locally, on-device**. No cloud dependency. No internet required for verification. Sub-millisecond decisions.

---

## Proof of Human

In the age of AI, the question is no longer just "who are you?" but **"is there a human behind this action?"**

A deepfake can replicate a face. An LLM can generate text. But no system can simultaneously and continuously fake:

- Your keystroke rhythm
- Your throat micro-vibrations
- Your device's hardware fingerprint
- Your behavioral patterns in context
- Your predictive action outcomes

Each layer is hard to spoof. The cascade makes it **exponentially harder**. And because it's continuous — not a one-time check — the window for deception doesn't exist.

This is not CAPTCHA 2.0. This is a digital heartbeat.

---

## Use Cases

### Age Verification Without Surveillance
No birthdate database. No government ID upload. The cascade determines age-appropriate confidence through behavioral and biometric signals. If the confidence drops below threshold, the connection closes. Continuously.

### AI Agent Governance
When an AI acts on your behalf, TIBET + UPIP prove exactly what it did, JIS verifies its identity and intent, and RVP ensures a human authorized the action chain.

### Accessibility
A person with ALS in a wheelchair. A father with brain hemorrhages affecting speech and motor skills. A local AI model that knows their context — time of day, daily patterns, communication style — and adapts. UWV letters routed to the right channel, simplified for the recipient. A door that opens because the face scan plus context says: this is trusted.

### Healthcare
A care robot recognizes a patient. Switches context to consult a specialist AI. Switches back. The state is parked in memory, verified, resumed. TIBET tokens preserve the full handoff chain. The patient's identity is verified continuously without them doing anything.

### Anti-Spoofing
Traditional MFA: something you know + something you have + something you are. RVP: everything you are, continuously, in context. Not three factors at login. A cascade running every moment.

---

## Architecture Principles

- **Evidence over Enforcement** — The system proves what happened. It doesn't block. Downstream consumers make their own trust decisions.
- **Local-First** — Verification runs on-device. No cloud dependency. Works offline. Works in areas with no internet.
- **Continuous, Not Discrete** — Every interaction is a verification moment. Trust is a gradient, not a gate.
- **Open Protocols** — All four protocols are published as IETF Internet-Drafts. The specifications belong to everyone.
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

W3C Invited Expert — Verifiable Credentials Working Group (2025)

**Website:** [humotica.com](https://humotica.com)

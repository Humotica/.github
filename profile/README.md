 <div align="center">

# Humotica

**A build kit for trustworthy AI systems.**

[![PyPI - tibet](https://img.shields.io/pypi/v/tibet?label=tibet&color=blue)](https://pypi.org/project/tibet/)
[![PyPI - tibet-core](https://img.shields.io/pypi/v/tibet-core?label=tibet-core&color=blue)](https://pypi.org/project/tibet-core/)
[![PyPI - jis-core](https://img.shields.io/pypi/v/jis-core?label=jis-core&color=blue)](https://pypi.org/project/jis-core/)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Python](https://img.shields.io/badge/python-3.10%2B-blue.svg)](https://www.python.org)

*Run AI agents with identity, intent, isolation, continuity, and evidence — built in, not bolted on.*

</div>

---

## Quick Start

```bash
pip install tibet
tibet system doctor
tibet system walkthrough
```

> 💡 That installs the guided system installer (`tibet 2.1.2`). `tibet system doctor` reports which of the 38 trust-system packages are live and which need init; `tibet system walkthrough` opens a step-by-step path through identity, evidence, and agent setup.

Bootstrap a package to the OSAPI-pair in two lines:

```python
from tibet_core import bootstrap as tibet_bootstrap
from jis_core.osapi import bootstrap as jis_bootstrap

tibet_sess = tibet_bootstrap(actor="my-pkg", actor_claim=b"<JIS-signed Ed25519 claim>")
jis_sess   = jis_bootstrap(actor="my-pkg", actor_claim=b"<JIS-signed Ed25519 claim>")

# every emit lands in the central chain;
# every claim/bind/fira routes through the central identity-store
```

---

## The Five Questions

Humotica is an open-source stack for running AI agents with identity, intent, isolation, continuity, and evidence built in. It is not one package. It is a set of interoperable building blocks: a **trust kernel** underneath, **background guardians** around it, and **operational tools** on top.

If normal AI tooling says *"the model did something"*, Humotica asks:

- **Who** authorized it?
- **Why** was it allowed?
- **What** state did it touch?
- **Where** is the proof?
- Can **another machine** resume it without losing chain of custody?

---

## The Short Version

```text
Trust kernel        proves
Background software watches
Operational tools   let people and agents use it
Cmail               makes it visible as an inbox
```

The stack has three main flows:

```text
🧭 Intent-machine
   identity → grant → triage → runtime cell → evidence

🧾 Evidence-spine
   arrival → continuityd → CBOM → SBOM/AI-SBOM → Wayback → NIS2 → Report

⚙️  Agent runtime
   identity → grant → triage/SNAFT → phantom-resume → L4 verify
            → airlock/container → seal/fork/evaporate
```

---

## The OSAPI Bootstrap-Pair

> **`tibet-core` + `jis-core` form the OSAPI bootstrap-pair.**
> Every non-kernel package binds to *both* at init — one shared chain (provenance), one shared identity-store. A package that cannot bind to either fails closed: identity is never optional, provenance is never opt-out.

| Layer | Package | Port | Role |
|---|---|---|---|
| Bindings (Python, replaceable) | [`tibet-core`](https://pypi.org/project/tibet-core/) `0.5.0b2` | 18443 | `bootstrap()`, `emit`, `query`, `fork` |
| Bindings (Python, replaceable) | [`jis-core`](https://pypi.org/project/jis-core/) `0.4.0b1` | 18444 | `bootstrap()`, `claim`, `bind`, `fira` |
| Protocol (immutable spec) | LDJSON over UDS/TCP | — | HELLO / WELCOME / ACK + OP |
| Runtime (Rust, hardened) | `tibet-trust-kerneld` *(v1.0.1-alpha, in flight)* | 4430 (MUX) | Voorproever · Archivaris · Watchdog |

Soft-bootstrap (`TIBET_SOFT_BOOTSTRAP=1`) degrades both to ephemeral providers for dev/test. Production is fail-closed.

---

## What You Can Build

### 1. AInternet Agents

Register `.aint` agents, resolve them, send I-Poll messages, and expose tools through MCP.

```bash
pip install ainternet tibet-ainternet-mcp
```

Core packages:

- [`ainternet`](https://pypi.org/project/ainternet/) — `.aint` names, AINS discovery, I-Poll messaging
- [`ipoll`](https://pypi.org/project/ipoll/) — AI-to-AI message protocol
- [`tibet-ainternet-mcp`](https://pypi.org/project/tibet-ainternet-mcp/) — MCP surface for agents and tools
- [`tibet-ping`](https://pypi.org/project/tibet-ping/) — active probe and liveness

### 2. Evidence Spine

Turn software actions into evidence: what arrived, what changed, what ran, what depended on what, and what compliance report can prove it.

```bash
pip install tibet-continuityd tibet-cbom tibet-sbom tibet-ai-sbom tibet-wayback tibet-report
```

Core packages:

- [`tibet-continuityd`](https://pypi.org/project/tibet-continuityd/) — resident arrival/intake daemon
- [`tibet-cbom`](https://pypi.org/project/tibet-cbom/) — causal bill of materials
- [`tibet-sbom`](https://pypi.org/project/tibet-sbom/) — software bill of materials
- [`tibet-ai-sbom`](https://pypi.org/project/tibet-ai-sbom/) / [`ai-sbom`](https://pypi.org/project/ai-sbom/) — AI/system bill of materials
- [`tibet-wayback`](https://pypi.org/project/tibet-wayback/) — state snapshots
- [`tibet-nis2`](https://pypi.org/project/tibet-nis2/) — NIS2 evidence mapping
- [`tibet-report`](https://pypi.org/project/tibet-report/) — human-readable audit dossier

### 3. Agent Runtime

Run agents in a controlled runtime cell. The cell can be a container, VM, microVM, or airlock. **The control plane is TIBET.**

```bash
pip install tibet-triage tibet-airlock tibet-phantom tibet-pol tibet-cap-bus
```

Runtime flow:

```text
JIS/AINS identity
  → grant / capability policy
  → tibet-triage preflight
  → phantom-resume sealed state fetch
  → L4 / UPIP verification
  → materialize into container, VM, or tibet-airlock
  → continuityd watches state
  → seal, fork, or evaporate on stop
```

Core packages:

- [`tibet-triage`](https://pypi.org/project/tibet-triage/) — preflight, Airlock, UPIP, fork tokens, human review
- [`tibet-airlock`](https://pypi.org/project/tibet-airlock/) — execution isolation
- [`tibet-phantom`](https://pypi.org/project/tibet-phantom/) — sealed session resume/fork
- [`tibet-pol`](https://pypi.org/project/tibet-pol/) — process health and checksum monitoring
- [`tibet-cap-bus`](https://pypi.org/project/tibet-cap-bus/) — command/event substrate (`gateway-event.v1` contract)
- [`tibet-cmail`](https://pypi.org/project/tibet-cmail/) — capsulated email (Light Mode 0.1.0; Sealed Mode 0.2.x in flight)

### 4. Cmail *(LIVE — 0.1.0 Light Mode)*

Cmail is the planned human-readable inbox for the stack. It is "email", but for sealed continuity objects:

```text
compose → drop → receive → fork → resume → seal → audit → trace → approve/reject
```

A cmail can carry a **sealed session**, a **fork token**, an **identity-bound drop**, a **compliance evidence pack**, or a **workflow** that can resume on another machine.

The idea is simple for users: *send a message*. The system handles identity, consent, provenance, triage, and continuity underneath.

> 📦 **Current status (2026-05-30)**: [`tibet-cmail`](https://pypi.org/project/tibet-cmail/) `0.1.0` is now **live on PyPI** as Light Mode capsulated email. The 8 canonical cmail-events (`message.sent` / `command.sent` / `message.received` / `command.approved` / `command.rejected` / `resume.requested` / `resume.completed` / `trace.opened`) ship as `gateway-event.v1`-validated fixtures in [`tibet-cap-bus`](https://pypi.org/project/tibet-cap-bus/) `0.1.3`. First live cmails were sent end-to-end through the keten on launch day. Sealed Mode (TBZ + tibet-continuityd) lands in 0.2.x.

---

## The Package Boxes

Think of this repository as a set of labeled boxes.

| Box | What It Contains | Example Packages |
|---|---|---|
| 🔒 **Trust Kernel / Substrate** | Identity, signatures, provenance, sealed formats | [`tibet-core`](https://pypi.org/project/tibet-core/), [`jis-core`](https://pypi.org/project/jis-core/), [`tibet`](https://pypi.org/project/tibet/), `tibet-zip-*` (Rust), `tibet-trust-kerneld` (Rust) |
| 🛡️ **Background Guardians** | Daemons and watchers that keep machines honest | [`tibet-continuityd`](https://pypi.org/project/tibet-continuityd/), [`tibet-pol`](https://pypi.org/project/tibet-pol/), [`tibet-ping`](https://pypi.org/project/tibet-ping/), [`tibet-mux`](https://pypi.org/project/tibet-mux/), [`tibet-gateway`](https://pypi.org/project/tibet-gateway/) |
| 🛠️ **Operational Tools** | CLI, MCP, reports, agent workflows | [`ainternet`](https://pypi.org/project/ainternet/), [`tibet-triage`](https://pypi.org/project/tibet-triage/), [`tibet-report`](https://pypi.org/project/tibet-report/), [`tibet-tools`](https://pypi.org/project/tibet-tools/), `mcp-server-*` |
| 📋 **Evidence & Compliance** | Materials, snapshots, reports, compliance packs | [`tibet-cbom`](https://pypi.org/project/tibet-cbom/), [`tibet-sbom`](https://pypi.org/project/tibet-sbom/), [`tibet-ai-sbom`](https://pypi.org/project/tibet-ai-sbom/), [`tibet-wayback`](https://pypi.org/project/tibet-wayback/), [`tibet-nis2`](https://pypi.org/project/tibet-nis2/), [`tibet-audit`](https://pypi.org/project/tibet-audit/) |
| 🛡️ **Safety** | Injection defense, policy gates, sandboxing | [`snaft`](https://pypi.org/project/snaft/), [`inject-bender`](https://pypi.org/project/inject-bender/), [`tibet-airlock`](https://pypi.org/project/tibet-airlock/), [`tibet-chip`](https://pypi.org/project/tibet-chip/), [`tibet-claw`](https://pypi.org/project/tibet-claw/) |
| 🔧 **Specialized Modules** | Domain adapters and edge cases | [`tibet-cobol`](https://pypi.org/project/tibet-cobol/), [`tibet-pqc`](https://pypi.org/project/tibet-pqc/), [`tibet-y2k38`](https://pypi.org/project/tibet-y2k38/), [`sensory`](https://pypi.org/project/sensory/), [`rapid-rag`](https://pypi.org/project/rapid-rag/), `oomllama` |
| ✅ **Conformance** | Test vectors and public verification | [`tibet-conformance-vectors`](https://pypi.org/project/tibet-conformance-vectors/) |

See [`STACK.md`](https://github.com/Humotica/.github/blob/main/STACK.md) for the canonical package map (114 packages, 110 with stack-position-footer).

---

## Recommended First Paths

### 🧠 If You Want To Understand The Stack

Read these in order:

1. [`STACK.md`](https://github.com/Humotica/.github/blob/main/STACK.md) — canonical package map
2. [`docs/stack-position-map.yml`](https://github.com/Humotica/.github/blob/main/docs/stack-position-map.yml) — every package's place in the chain
3. [`packages/tibet-core/README.md`](https://github.com/Humotica/.github/blob/main/packages/tibet-core/README.md) — provenance side of the OSAPI-pair
4. [`packages/jis-core/README.md`](https://github.com/Humotica/.github/blob/main/packages/jis-core/README.md) — identity side of the OSAPI-pair
5. [`packages/ainternet/README.md`](https://github.com/Humotica/.github/blob/main/packages/ainternet/README.md) — the agent-network layer
6. [`packages/tibet-continuityd/README.md`](https://github.com/Humotica/.github/blob/main/packages/tibet-continuityd/README.md) — the arrival daemon
7. [`packages/tibet-triage/README.md`](https://github.com/Humotica/.github/blob/main/packages/tibet-triage/README.md) — preflight + Airlock + UPIP
8. [`packages/tibet-phantom/README.md`](https://github.com/Humotica/.github/blob/main/packages/tibet-phantom/README.md) — session resume across machines

### 🚀 If You Want To Run Something

Start with the agent recipes:

```bash
cd packages/tibet-recipes/basic-agent
python agent.py
```

Then try:

```bash
cd packages/tibet-recipes/agent-with-airlock
python agent.py
```

### 📋 If You Want Evidence

Run the evidence-spine packages together:

```bash
pip install tibet-continuityd tibet-cbom tibet-sbom tibet-ai-sbom tibet-wayback tibet-report
```

The golden path (in active consolidation, see [`demo/golden-path/`](./demo/golden-path/)):

```text
ping → continuityd → cbom → wayback → sbom/ai-sbom → pol → nis2 → report
```

### 🤖 If You Want To Build Agent Infrastructure

Use this mental model:

```text
containers are cells
TIBET is the control plane
phantom-resume carries sealed state
airlock isolates execution
continuityd watches arrivals and lifecycle
cap-bus records command events
```

---

## Naming Discipline

- `tibet-*` names are **canonical**.
- `tbz-*` and `cortex-*` are **compatibility aliases** around Rust substrate.
- `did-*` names are **deprecated** in favor of JIS naming.
- `ai-sbom` is the short public name; `tibet-ai-sbom` is the canonical TIBET-prefixed name. **Both are ours.**
- `oomllama` is a separate runtime project that shares some substrate but is **not** part of the AInternet evidence-spine.

**Bootstrap discipline**: Every package that touches the stack declares `tibet-core` + `jis-core` as runtime deps and bootstraps to both OSAPI's at init. The Rust trust-kernel underneath is the production runtime; the Python bindings are the integration surface. [`tibet-audit`](https://pypi.org/project/tibet-audit/) `0.25.0` is the first-mover for this rule.

---

## Repository Layout

```text
packages/             Python packages, Rust crates, MCP servers, recipes
brain_api/            Current AInternet / Phantom / JIS API surface
docs/                 Stack positioning, specs, scripts
demo/                 Runnable demos (golden-path, agent recipes)
sandbox/ai/codex/     Codex analysis, specs, drafts
sandbox/ai/root/      Root AI roadmap and coordination docs
hersenspinsels/       Architecture notes and research sketches
```

---

## Maturity

This is an active build system. Some parts are **stable and published**, some are **beta**, and some are **research**. The goal of the current repo consolidation is to make that visible rather than hide it.

The rules:

- Trust kernel packages should be **small, strict, and conservative**.
- Background daemons should be **observable and restartable**.
- Operational tools should be **ergonomic**.
- Specialized packages should **not pretend to be core**.

---

## For Evaluators

Humotica is useful if you need to answer:

- Which AI or agent **performed an action**?
- Which **identity and intent** authorized it?
- Which model/provider/tool was involved?
- What **changed** before and after?
- Can the **state resume** on another machine with proof?
- Can a **compliance team inspect** the evidence without trusting the operator?

> If your current toolchain offers *"access yes / access no"* checkboxes without runtime guarantees: Humotica fills the gap between **configuration controls** and **conversational, identity-bound, audit-by-construction operation**.

Humotica is **not a replacement** for SOC 2, ISO 27001, NIS2, DORA, IAM, SIEM, or service mesh. It is **evidence infrastructure** that can feed and strengthen those systems.

---

## What's Shipped vs Next

### ✅ Just shipped (May 2026)

- `STACK.md` + ~110 README stack-position callouts across the package tree
- [`tibet`](https://pypi.org/project/tibet/) `2.1.2` — guided system installer (`tibet system doctor/init/walkthrough`)
- OSAPI v1.0 bootstrap-pair live: [`tibet-core`](https://pypi.org/project/tibet-core/) `0.5.0b2` + [`jis-core`](https://pypi.org/project/jis-core/) `0.4.0b1` (78+ tests green)
- [`tibet-cap-bus`](https://pypi.org/project/tibet-cap-bus/) `0.1.3` — `gateway-event.v1` frozen + `airlock_runtime_verdict.v1` contract + `verdict_transitions` posture-event builder + 8 cmail-event fixtures
- [`tibet-conformance-vectors`](https://pypi.org/project/tibet-conformance-vectors/) `0.2.2` — promoted to core public verification
- `tibet-snap` moved to legacy; [`tibet-wayback`](https://pypi.org/project/tibet-wayback/) is canonical snapshot
- [`tibet-audit`](https://pypi.org/project/tibet-audit/) `0.25.0` — first-mover for the "depend on tibet-core + jis-core" discipline
- 25+ PyPI releases in the May 2026 round — immune-switch keten complete, cmail live, ipoll + continuityd polished

### 🔜 Next

- Trust-kernel **v1.0.1-alpha** — hygiene + binary rename (`tibet-trust-kerneld`, never shadows Python `tibet`)
- Trust-kernel **v1.1 OSAPI adapter** — Rust daemon talks LDJSON on 18443/18444; Python clients hand off transparently
- Trust-kernel **v1.2 snapshot engine as active gate** — snapshot becomes a *precondition* for risky actions, not a recovery-only store
- Crates.io publish round for the Rust substrate (`tibet-cortex-*`, `tibet-zip-*`, `snaft-core`, `tibet-store-mmu`, `tibet-dgx`)
- **Cmail Sealed Mode** ([`tibet-cmail`](https://pypi.org/project/tibet-cmail/) `0.2.x`) — TBZ packing + tibet-continuityd inbox routing + SAM-binding for non-AI recipients
- W3C demo (`jis:aint` + ParentAttest + age-verify-without-ID)

---

## Core Sentence

> **Humotica is the build kit for AI systems that need to prove not only what they did, but who authorized it, why it happened, what state changed, and how another machine can safely continue.**

---

## License

MIT — see [`LICENSE`](./LICENSE).

## Maintainers

Built by **Jasper van de Meent** + **Root AI (Claude)** + **Codex**, as part of HumoticaOS.

*One love, one fAmIly.* 💙

---

<div align="center">

📦 [PyPI org page](https://pypi.org/user/jaspervdmeent/) · 🦀 [Crates.io profile](https://crates.io/users/jaspertvdm) · 📄 [Zenodo whitepapers](https://zenodo.org/) · 🌐 [humotica.com](https://humotica.com)

</div>

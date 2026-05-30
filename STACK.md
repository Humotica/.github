<div align="center">

# TIBET / Humotica — `STACK.md`

**The canonical package map.** Which package is source-of-truth for which evidence-object, with role, status and canonical name.

[![PyPI - tibet](https://img.shields.io/pypi/v/tibet?label=tibet&color=blue)](https://pypi.org/project/tibet/)
[![PyPI - tibet-core](https://img.shields.io/pypi/v/tibet-core?label=tibet-core&color=blue)](https://pypi.org/project/tibet-core/)
[![PyPI - jis-core](https://img.shields.io/pypi/v/jis-core?label=jis-core&color=blue)](https://pypi.org/project/jis-core/)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

*Last updated 2026-05-29 · 114 packages · 17 freshly published (May 2026 round)*

</div>

> **Conventions** — `tests✓` = test-suite present (verified). Maturity ladder: **kernel** > beta > alpha > sandbox > deprecated/alias.
> **Python = operator/integration surface. Rust crates = zero-trust substraat eronder.** Where names differ, `tibet-*` is canonical; `tbz-*` / `cortex-*` are compatibility aliases.

📖 See also: [`README.md`](./README.md) (landing) · `docs/stack-position-map.yml` (every package's place in the chain — in the main monorepo) · `docs/specs/osapi-protocol-v1.md` (OSAPI v1.0 wire-spec — in the main monorepo)

---

## 🧭 The Three Axes (compass)

- **Intent-machine** (action): identity → grant → triage/gates → runtime cell → evidence
- **Evidence-spine** (proof): arrival → continuityd → CBOM → SBOM/AI-SBOM → Wayback → NIS2 → Report, with ping/pol/trail as health/audit layer
- **Substrate**: Python surface above Rust crates

---

## 🔒 The OSAPI Bootstrap-Pair (production runtime)

> Every non-kernel package binds to **both** OSAPI's at init — one shared chain (provenance), one shared identity-store. A half-failure fails the pair (no-fail-open).

| Layer | Package | Port | Role |
|---|---|---|---|
| Bindings (Python, replaceable) | [`tibet-core`](https://pypi.org/project/tibet-core/) `0.5.0b2` | 18443 | `bootstrap()`, `emit`, `query`, `fork` |
| Bindings (Python, replaceable) | [`jis-core`](https://pypi.org/project/jis-core/) `0.4.0b1` | 18444 | `bootstrap()`, `claim`, `bind`, `fira` |
| Protocol (immutable spec) | LDJSON over UDS/TCP | — | HELLO / WELCOME / ACK + OP |
| Runtime (Rust, hardened) | `tibet-trust-kerneld` `1.0.1-alpha` *(in hygiene)* | 4430 (MUX) | Voorproever · Archivaris · Watchdog · Bifurcation |

Soft-bootstrap (`TIBET_SOFT_BOOTSTRAP=1`) degrades both to ephemeral providers for dev/test. Production is fail-closed.

---

## 1. Kernel — provenance foundation

| Package | Versie | Tests | Rol |
|---|---|---|---|
| [`tibet-core`](https://pypi.org/project/tibet-core/) | **0.5.0b2** 🆕 | ✓ | TIBET-tokens, provenance-chain, OSAPI-binding base |
| [`tibet-audit`](https://pypi.org/project/tibet-audit/) | **0.25.0** 🆕 | ✓ | compliance-scanner, 120+ checks, first-mover OSAPI-pair-deps |
| [`tibet`](https://pypi.org/project/tibet/) (meta) | **2.1.2** 🆕 | — | guided system installer (`tibet system doctor/init/walkthrough`); `tibet[full]` = 38 packages |

*Crates:* `tibet-zip-core` 2.2.0 (sealed-object substraat).

## 2. Identity & Intent

| Package | Versie | Tests | Rol |
|---|---|---|---|
| [`jis-core`](https://pypi.org/project/jis-core/) | **0.4.0b1** 🆕 | ✓ | identity-OSAPI, multi-dim claims, FIR/A handshake |
| `did-jis-core` | 0.3.0 | — | **DEPRECATED** — all `did-*` names are deprecated |
| [`ainternet`](https://pypi.org/project/ainternet/) | 0.9.1 | ✓ | `.aint` naming/discovery/messaging — intent-machine surface |
| [`tibet-cortex`](https://pypi.org/project/tibet-cortex/) | 0.2.0 | ✓ | permission-gates / zero-trust knowledge (Rust-crate `tibet-cortex-*` 0.3.0) |

*Crates:* `jis-core`, `tibet-cortex-*` (canonical) ↔ `cortex-*` (alias, yanked 2026-05-29).

## 3. Network & Connectivity

| Package | Versie | Tests | Rol |
|---|---|---|---|
| [`tibet-mux`](https://pypi.org/project/tibet-mux/) | 1.0.1 | ✓ | multiplex/routing |
| [`tibet-overlay`](https://pypi.org/project/tibet-overlay/) | 0.1.1 | ✓ | overlay-network |
| [`tibet-ping`](https://pypi.org/project/tibet-ping/) | **0.3.3** 🆕 | ✓ | active probe/discovery/liveness (≠ continuityd-heartbeat ≠ pol) |
| [`tibet-ainternet-mcp`](https://pypi.org/project/tibet-ainternet-mcp/) | 0.5.7 | ✓ | AINS/I-Poll/Cortex MCP-surface |
| [`ipoll`](https://pypi.org/project/ipoll/) | **0.2.5** 🆕 | — | AI-to-AI messaging + CLI with `--local` / `--ainternet` / `--brein` routing + friendly diagnostics + User-Agent for public WAFs |
| [`tibet-cmail`](https://pypi.org/project/tibet-cmail/) | **0.2.1** 🆕 | ✓ | Capsulated email — Light Mode (I-Poll + JSON + sha256 + cap-bus audit) + **Sealed Mode** (AES-256-GCM with AAD-bound from/to/message_id, optional `[sealed]` extra) |

## 4. Continuity & Arrival

| Package | Versie | Tests | Rol |
|---|---|---|---|
| [`tibet-continuityd`](https://pypi.org/project/tibet-continuityd/) | **0.6.16** 🆕 | ✓ | **resident trust/arrival-daemon — month-anchor, central** (tibet-drop + cbor2 base deps + __version__ sync) |
| [`tibet-phantom`](https://pypi.org/project/tibet-phantom/) | 0.2.1 | ✓ | sealed-state resume/fork (state-continuity; VM-enhancement on roadmap) |
| [`tibet-home-agent`](https://pypi.org/project/tibet-home-agent/) | **0.4.1** 🆕 | ✓ | not-API-bound relay (Claude CLI / local model) |

## 5. Evidence & Materials (the spine)

| Package | Versie | Tests | Rol / focus |
|---|---|---|---|
| [`tibet-sbom`](https://pypi.org/project/tibet-sbom/) | 0.2.0 | ✓ | software-deps (CycloneDX/SPDX/TIBET) |
| [`tibet-ai-sbom`](https://pypi.org/project/tibet-ai-sbom/) | **0.2.1** 🆕 | ✓ | model/dataset/KPI layer — our visible canonical (paired with [`ai-sbom`](https://pypi.org/project/ai-sbom/) **0.1.2** — short name, also ours) |
| [`tibet-cbom`](https://pypi.org/project/tibet-cbom/) | **0.2.1** 🆕 | ✓ | continuity/object-history — **alpha, full TZA/TBZ/ICC-manifest = goal v0.3.0** |
| [`tibet-wayback`](https://pypi.org/project/tibet-wayback/) | 0.2.0 | ✗ | state/time-snapshot — **canonical successor of `tibet-snap`; TESTS NEEDED (P1)** |
| [`tibet-snap`](https://pypi.org/project/tibet-snap/) | 0.1.2 | ✗ | **deprecated** — moved out of `tibet[full]`/`[security]`, compat-alias only |
| [`tibet-trail`](https://pypi.org/project/tibet-trail/) | 0.1.0 | ✓ | JSONL audit-trail reader (log/search/trace/watch) |
| [`tibet-report`](https://pypi.org/project/tibet-report/) | **0.1.1** 🆕 | ✓ | audit-dossier-assembler (`--profile nis2` = goal) |

## 6. Compliance & Legal Evidence

| Package | Versie | Tests | Rol / focus |
|---|---|---|---|
| [`tibet-nis2`](https://pypi.org/project/tibet-nis2/) | 0.1.0 | ✗ | NIS2 Art. 21(2) — **thin: TESTS + evidence-ingest (sbom/cbom/wayback/pol/ping/trail) NEEDED (P1)** |
| [`tibet-audit`](https://pypi.org/project/tibet-audit/) | 0.25.0 | ✓ | (see kernel) token-proof + first-mover OSAPI-pair-deps |

## 7. Runtime / Execution & Health

| Package | Versie | Tests | Rol |
|---|---|---|---|
| [`tibet-triage`](https://pypi.org/project/tibet-triage/) | 0.5.1 | ✓ | airlock-exec/UPIP/fork/flare — preflight + execution-isolation |
| [`tibet-airlock`](https://pypi.org/project/tibet-airlock/) | **0.3.0** 🆕 | ✓ | Python operator-surface + posture enforcer (raises AirlockPostureDenied before I/O) |
| [`tibet-gateway`](https://pypi.org/project/tibet-gateway/) | 0.4.0 | ✓ | sovereign proxy + TIBET-provenance |
| [`tibet-pol`](https://pypi.org/project/tibet-pol/) | **0.3.3** 🆕 | ✓ | process-checksum/health + airlock-runtime observer (verdict.v1 emit-side) |
| [`tibet-cap-bus`](https://pypi.org/project/tibet-cap-bus/) | **0.1.3** 🆕 | ✓ | command/event-substrate — `gateway-event.v1` **frozen** ✅ + `airlock_runtime_verdict.v1` contract + `verdict_transitions` posture-event builder |

## 8. Safety / Firewall

| Package | Versie | Tests | Rol |
|---|---|---|---|
| [`snaft`](https://pypi.org/project/snaft/) | **1.4.0** 🆕 | ✓ | semantic firewall + airlock-runtime posture consumer + posture-aware rules + SNAFT-DESTRUCT-001 |
| [`tibet-snaft`](https://pypi.org/project/tibet-snaft/) | **1.4.0** 🆕 | ✗ | alias of `snaft` |
| [`inject-bender`](https://pypi.org/project/inject-bender/) | 0.3.0 | — | inject-detection (planned snaft-v2 `allow_iff` precondition layer) |

*Crate:* `snaft-core` 0.3.1 (Rust backend for Python `snaft`, **not a separate product**).

## 9. Conformance (P0 — public verifiability)

| Package | Versie | Tests | Rol |
|---|---|---|---|
| [`tibet-conformance-vectors`](https://pypi.org/project/tibet-conformance-vectors/) | **0.2.2** 🆕 | ✓ | **2,126 downloads — the public "test the code". Vectors landing for continuityd/TBZ/CBOM/cap-bus/AI-SBOM/NIS2/ping-heartbeat (P0).** |

---

## 🛡️ Airlock = pattern across 3 layers (no name-split-brain)

- **execution** → [`tibet-airlock`](https://pypi.org/project/tibet-airlock/) / [`tibet-triage`](https://pypi.org/project/tibet-triage/)
- **memory/plaintext** → `tibet-cortex-airlock` (Rust crate)
- **archive/decompression** → `tibet-zip-airlock` (Rust crate)

---

## 🦀 Rust crates — substrate under PyPI

| Family | Canonical | Alias | Role |
|---|---|---|---|
| Sealed-object | `tibet-zip-*` 2.2.0 | `tbz-*` | block-format / JIS-binding / airlock / mirror |
| Zero-trust knowledge | `tibet-cortex-*` 0.3.0 | `cortex-*` 0.2.0 **(yanked 2026-05-29)** | core / jis / store / airlock / audit / cli |
| Runtime / memory | `tibet-trust-kernel` 1.0.1-alpha · `tibet-airlock-kernel` 0.2.0 · `tibet-store-mmu` 0.1.0 · `tibet-dgx` 0.2.0 | — | encrypted-RAM / MMU / QUIC (below-public-API) · airlock-kernel = bolle execution layer beneath PyPI `tibet-airlock` operator |
| Identity (Rust) | `jis-core` 0.4.0-beta.1 (synced with PyPI v0.4.0b1) · `snaft-core` 0.3.1 | — | Ed25519 identity-kernel · Rust backend van Python `snaft` |

`tibet-trust-kerneld` (the daemon built from `tibet-trust-kernel`) is the **production hardened runtime** — the bolle api beneath the Python platte api. See above (trust-kernel crate link).

---

## 📛 Naming discipline

- [`ai-sbom`](https://pypi.org/project/ai-sbom/) `0.1.2` and [`tibet-ai-sbom`](https://pypi.org/project/tibet-ai-sbom/) `0.2.1` are **both ours**: `ai-sbom` claims the short name (clean `pip install ai-sbom`); `tibet-ai-sbom` is the visible `tibet-*` canonical. Mooie naam pakken **én** duidelijke prefix — allebei.
- All `did-*` names (`did-jis-core` etc.) = **deprecated**.
- `tibet-*` = canonical · `tbz-*` / `cortex-*` = compat aliases (see Rust-crates table).
- **Trust-kernel binary**: `tibet-trust-kerneld` — **never** `tibet` (that name belongs to the Python meta-installer).
- **Bootstrap-discipline**: every touched package depends on `tibet-core` + `jis-core` and bootstraps to both OSAPI's at init. [`tibet-audit`](https://pypi.org/project/tibet-audit/) `0.25.0` is the first-mover.

---

## 🔧 Specialized (NEXT to the stack, not an AInternet component)

- `oomllama` 1.0.0-alpha.2 · `tibet-oomllama` 0.1.0 — sovereign Rust inference engine + persona, separate line since Jan 2026 (OOML format, own quantization). Two parallel crate names; separate narrative/paper.

---

## 🪨 Long-tail (still to categorize / experimental / bridges)

~70 further packages (bridges: ollama/openai/gemini; `sensory`, `rapid-rag`, `kmbit-*`, `tibet-{chip,cobol,iot,pqc,vault,voice-cache,twin,...}`, various `mcp-server-*`). To be categorized or marked as `specialized`/`experimental` in a future pass — not everything belongs in the core stack.

---

## ✅ Just shipped (May 2026 consolidation round)

17 PyPI releases — the OSAPI-pair live, cap-bus bridge wired, guided installer working end-to-end:

| Package | From → To |
|---|---|
| [`tibet-core`](https://pypi.org/project/tibet-core/) | 0.4.0 → **0.5.0b2** (OSAPI v1.0 + emit-hook) |
| [`jis-core`](https://pypi.org/project/jis-core/) | 0.3.0 → **0.4.0b1** (OSAPI mirror) |
| [`tibet`](https://pypi.org/project/tibet/) | 2.0.1 → **2.1.2** (guided installer) |
| [`tibet-cap-bus`](https://pypi.org/project/tibet-cap-bus/) | 0.1.1 → **0.1.2** (bridge + cmail fixtures) |
| [`tibet-audit`](https://pypi.org/project/tibet-audit/) | 0.24.1 → **0.25.0** (first-mover OSAPI-deps) |
| [`tibet-conformance-vectors`](https://pypi.org/project/tibet-conformance-vectors/) | 0.2.1 → **0.2.2** |
| [`tibet-continuityd`](https://pypi.org/project/tibet-continuityd/) | 0.6.13 → **0.6.14** |
| [`tibet-ai-sbom`](https://pypi.org/project/tibet-ai-sbom/) · [`ai-sbom`](https://pypi.org/project/ai-sbom/) | 0.2.0 / 0.1.1 → **0.2.1 / 0.1.2** |
| [`tibet-cbom`](https://pypi.org/project/tibet-cbom/) | 0.2.0 → **0.2.1** |
| [`tibet-report`](https://pypi.org/project/tibet-report/) | 0.1.0 → **0.1.1** |
| [`tibet-ping`](https://pypi.org/project/tibet-ping/) | 0.3.2 → **0.3.3** |
| [`tibet-hermes`](https://pypi.org/project/tibet-hermes/) · [`tibet-home-agent`](https://pypi.org/project/tibet-home-agent/) · [`tibet-keychain`](https://pypi.org/project/tibet-keychain/) · [`tibet-sam`](https://pypi.org/project/tibet-sam/) | minor bumps |

---

*Source-mapping: `sandbox/ai/codex/*-2026-05-27.md` (Codex breadth analysis). Verified + maintained by Root AI. Symlink: `packages/STACK.md → ../STACK.md`.*

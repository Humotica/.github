# tibet-triage

**Human-in-the-Loop as a discipline. Not a rubber stamp.**

[![PyPI](https://img.shields.io/pypi/v/tibet-triage)](https://pypi.org/project/tibet-triage/)
[![Python](https://img.shields.io/pypi/pyversions/tibet-triage)](https://pypi.org/project/tibet-triage/)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)
[![TIBET](https://img.shields.io/badge/protocol-TIBET-purple)](https://pypi.org/project/tibet-core/)

tibet-triage implements four protocols that work together:

| Protocol | What it does |
|----------|-------------|
| **Airlock** | Sandbox execution with isomorphic mirror — every action pre-computed, every change diffed, every decision signed |
| **UPIP** | Universal Process Integrity Protocol — five-layer reproducibility stack with cryptographic hashing |
| **Fork Tokens** | Multi-actor continuation protocol — freeze process state, hand off to another machine/AI/human, resume with full chain of custody |
| **Flare Rescue** | API failover protocol — when your API goes down, fire an SOS to the network and let another node handle it |

Plus **I-Poll integration** for real-time AI-to-AI fork delivery and flare routing over HTTP.

---

## The Problem

### For compliance teams
Frameworks like SOC 2, ISO 27001, NIS2, DORA, PCI DSS, and HIPAA require human oversight of automated processes. But nobody defines *when*, *how*, or *with what evidence*. The result: operators rubber-stamp everything because stopping the pipeline is worse than the risk.

### For multi-agent systems
When AI Agent A needs AI Agent B to continue a task, there's no standard way to:
- Freeze the complete process state (code, deps, data, intent)
- Transfer it with cryptographic chain of custody
- Verify the receiving agent has the right capabilities
- Prove the handoff happened and who did what

### For distributed computing
When a process needs to move between machines — edge to cloud, drone to base station, lab to HPC cluster — you need more than "copy the files." You need proof that the state is intact, the dependencies match, and the result is reproducible.

**tibet-triage solves all three.**

---

## Quick Start

```bash
pip install tibet-triage
```

### Run a command in the Airlock

```bash
# Every command runs in a sandbox first
tibet-triage run python deploy.py \
  --source ./app \
  --intent "Deploy API v2.3"

# Review what happened
tibet-triage pending
tibet-triage review triage-abc123

# Approve or reject with evidence
tibet-triage approve triage-abc123 --operator jasper@example.com
tibet-triage reject triage-abc123 --reason "Unexpected file changes"
```

### Create a reproducible UPIP bundle

```bash
# Capture everything: state, deps, process, result, verification
tibet-triage upip-export ./my-experiment \
  --intent "Train model v3 on dataset-2026Q1" \
  -o experiment.upip.json

# Reproduce on another machine
tibet-triage upip-reproduce experiment.upip.json --verify
```

### Fork a process to another actor

```bash
# Create a fork token — freeze state for handoff
tibet-triage upip-fork experiment.upip.json \
  -o handoff.fork.json \
  --actor-from "Lab-GPU-01" \
  --actor-to "HPC-Cluster" \
  --intent "Continue training on larger GPU" \
  --require-deps "torch>=2.0,tibet-triage>=0.4.0" \
  --require-gpu

# On the receiving machine: resume
tibet-triage upip-resume handoff.fork.json \
  -a "HPC-Cluster" \
  -- python train.py --continue-from checkpoint.pt
```

### Deliver forks via I-Poll (AI-to-AI messaging)

```bash
# Fork + deliver directly to another agent
tibet-triage upip-fork experiment.upip.json \
  -o handoff.fork.json \
  --actor-from root_idd \
  --actor-to gemini \
  --deliver gemini

# Or send an existing fork file
tibet-triage upip-fork-send handoff.fork.json gemini

# Listen for incoming forks and auto-resume them
tibet-triage upip-fork-listen \
  -a gemini \
  -c python process.py \
  -o /var/forks/incoming/
```

---

## Architecture

### 1. Airlock — Sandbox Execution

Every process runs in an isolated sandbox before touching production. The Airlock captures:

- **File manifest** — SHA-256 of every file before and after
- **Side effects** — What was created, modified, deleted
- **Isomorphic diff** — Exact changes that would be applied
- **TIBET token** — Provenance record of the entire execution

```
┌─────────────────────────────────────┐
│           AIRLOCK SANDBOX           │
│                                     │
│  Input State ──→ Process ──→ Output │
│       │              │          │   │
│       ▼              ▼          ▼   │
│   Manifest       TIBET Token   Diff │
│                                     │
│  Nothing escapes until approved.    │
└─────────────────────────────────────┘
```

The Airlock supports **shadow-run mode**: execute the process, capture everything, but don't apply. This is how fork tokens validate before committing.

### 2. UPIP — Universal Process Integrity Protocol

UPIP is a five-layer stack that captures everything needed to reproduce a process on any machine:

```
┌─────────────────────────────────┐
│  L5  VERIFY                     │  Cross-machine proof
│  ─────────────────────────────  │
│  L4  RESULT                     │  Output + diff + hash
│  ─────────────────────────────  │
│  L3  PROCESS                    │  Command + intent + actor
│  ─────────────────────────────  │
│  L2  DEPS                       │  Exact dependency versions
│  ─────────────────────────────  │
│  L1  STATE                      │  Code + data + environment
└─────────────────────────────────┘
         │
         ▼
    stack_hash = SHA-256(L1 ⊕ L2 ⊕ L3 ⊕ L4)
```

Each layer is independently hashed. The stack hash chains them together. Tampering with any layer invalidates the entire stack.

**UPIP bundles are JSON files** (`.upip.json`) that contain the complete stack. They're portable, human-readable, and self-verifying.

#### Layer details

| Layer | Captures | Hash prefix |
|-------|----------|-------------|
| L1 STATE | Git commit, file manifest, directory hash, or container image | `git:`, `files:`, `image:` |
| L2 DEPS | Python version, pip freeze, system packages, pip hash | `deps:sha256:` |
| L3 PROCESS | Command array, intent string, actor identity, environment vars | — |
| L4 RESULT | Exit code, stdout/stderr, file diff, output hash | `sha256:` |
| L5 VERIFY | Machine identity, timestamp, environment check, reproduction proof | — |

### 3. Fork Tokens — Multi-Actor Continuation

A fork token is a **social contract in JSON**. It says: "Here is where I stopped. Here is everything you need to continue. Here is how to verify it's intact."

```
┌──────────────────────────────────────────┐
│             FORK TOKEN                   │
│                                          │
│  fork_id:           fork-a7b3c9d2        │
│  parent_stack_hash: upip:sha256:4f2e...  │
│  continuation:      L4:post_result       │
│  intent:            "Continue analysis"  │
│  actor_handoff:     Opus -> Sonnet       │
│  memory_hash:       sha256:9c1a...       │
│  fork_hash:         fork:sha256:7d3f...  │
│                                          │
│  capability_required:                    │
│    deps: ["tibet-triage>=0.4.0"]         │
│    gpu: true                             │
│                                          │
│  partial_layers:                         │
│    L1_state: { hash, type }              │
│    L2_deps:  { hash, python }            │
│    L3_process: { command, intent }       │
│    L4_result: { hash, exit_code }        │
└──────────────────────────────────────────┘
```

#### Fork types

| Type | Use case | Memory hash source |
|------|----------|-------------------|
| `script` | UPIP bundle IS the state — CLI pipelines, CI/CD, batch jobs | SHA-256 of L1+L2+L3+L4 |
| `ai_to_ai` | AI context window serialized as `.blob` — LLM handoff, agent swarms | SHA-256 of the .blob file |
| `human_to_ai` | Human intent document — task delegation, review requests | SHA-256 of intent doc |
| `fragment` | Parallel fork — split one task into N sub-tasks (Fork²) | SHA-256 of fragment spec |

#### The handoff flow

```
Machine A                                  Machine B
    │                                          │
    │  1. capture_and_run()                    │
    │     └─→ UPIP stack (L1-L4)              │
    │                                          │
    │  2. fork_upip()                          │
    │     └─→ Fork Token                      │
    │         ├── parent_stack_hash            │
    │         ├── active_memory_hash           │
    │         ├── capability_required          │
    │         └── actor_handoff: A → B        │
    │                                          │
    │  3. save_fork() ──→ .fork.json ────────→│
    │     (or I-Poll deliver)                  │
    │                                          │
    │                      4. resume_upip()    │
    │                         ├── Load fork    │
    │                         ├── Validate hash│
    │                         ├── Check caps   │
    │                         ├── Shadow-run   │
    │                         └── New UPIP     │
    │                                          │
    │←──────── ACK (resume_hash) ─────────────│
    │                                          │
    │  fork_chain: [fork-a7b3c9d2]            │
    │  Both stacks linked by parent_hash       │
```

#### Fork validation on resume

When `resume_upip()` is called, four checks happen automatically:

| Check | What | Fails when |
|-------|------|-----------|
| **Fork hash** | Recompute hash from token fields | Token was tampered with |
| **Stored hash** | Compare against hash in .fork.json | File was modified after save |
| **Capabilities** | Check required deps are installed | Missing packages |
| **Actor match** | Verify actor identity | Wrong recipient |

If the fork hash doesn't match, the resume still runs but the verification record flags `fork_hash_match: false`. The system doesn't block — it **proves what happened**.

### 4. I-Poll — AI-to-AI Fork Delivery

I-Poll connects fork tokens to a live messaging network. Instead of copying `.fork.json` files manually, agents deliver forks directly:

```
Actor A                     I-Poll API                   Actor B
   │                           │                            │
   │── deliver_fork() ────────→│                            │
   │   poll_type: TASK         │                            │
   │   metadata: fork_data     │                            │
   │   + .fork.json file       │                            │
   │                           │──→ pull_forks() ──────────→│
   │                           │    (filters TASK+fork)     │
   │                           │                            │
   │                           │    resume_upip()           │
   │                           │    airlock shadow-run      │
   │                           │    verify + new UPIP stack │
   │                           │                            │
   │←── ack_fork() ───────────│←────────────────────────────│
   │    resume_hash            │                            │
   │    success: true          │                            │
```

The `IPollClient` is a pure Python HTTP client with zero extra dependencies:

```python
from tibet_triage import IPollClient, ForkToken
from tibet_triage.upip import fork_upip, save_fork

# Fork a bundle
stack, fork = fork_upip(
    bundle_path="experiment.upip.json",
    actor_from="lab-gpu-01",
    actor_to="hpc-cluster",
    intent="Scale up training",
)

# Deliver via I-Poll
client = IPollClient(from_agent="lab-gpu-01")
result = client.deliver_fork(fork, to_agent="hpc-cluster")
print(f"Delivered: {result.poll_id}")
```

On the receiving side:

```python
from tibet_triage import IPollClient
from tibet_triage.upip import resume_upip, save_fork

client = IPollClient(from_agent="hpc-cluster")

# Listen for incoming forks
for msg in client.listen_forks():
    fork = msg.fork_token
    print(f"Received fork: {fork.fork_id} from {msg.from_agent}")

    # Save and resume
    save_fork(fork, f"/var/forks/{fork.fork_id}.fork.json")
    stack, _, result, verify = resume_upip(
        fork_path=f"/var/forks/{fork.fork_id}.fork.json",
        command=["python", "train.py", "--continue"],
        actor="hpc-cluster",
    )

    # ACK back with the result
    client.ack_fork(fork, resume_hash=stack.stack_hash, success=result.success)
```

### 5. Flare Rescue — API Failover Protocol

When an agent's API goes down — rate limit, timeout, provider outage — Flare sends an SOS to the network. Another node picks it up, processes the request, and returns the result. No manual intervention needed.

```
Agent A (API down)              I-Poll                     Rescue Node B
     │                            │                              │
     │── Flare SOS ──────────────→│                              │
     │   request_type: inference   │                              │
     │   urgency: blocking         │                              │
     │   preferred_model: qwen2.5  │                              │
     │                             │──→ FlareNode.listen() ──────→│
     │                             │    (pulls TASK+tibet_flare)   │
     │                             │                              │
     │                             │    OllamaHandler.handle()    │
     │                             │    └─→ POST /api/generate    │
     │                             │                              │
     │←──── FlareResult ──────────│←──── ACK ─────────────────────│
     │   success: true             │   model_used: qwen2.5:7b     │
     │   result: "..."             │   processing_time: 340ms     │
```

**Explicit opt-in only.** Nothing flares unless you configure it. No auto-flaring from importing the package.

#### The `@flare_rescue` decorator

Wrap any function that calls an API. On failure, it automatically fires a flare:

```python
from tibet_triage import flare_rescue

@flare_rescue(
    target="jasper_p520",           # Who should rescue this
    from_agent="my_agent",          # Who am I
    preferred_model="qwen2.5:7b",   # What model to use
    timeout=60,                      # How long to wait
    catch=(TimeoutError, ConnectionError),  # What errors to catch
    intent="LLM inference for code review",
)
def ask_llm(prompt: str) -> str:
    """Calls OpenAI API — but falls back to local Ollama via Flare."""
    return openai.chat.completions.create(
        model="gpt-4", messages=[{"role": "user", "content": prompt}]
    ).choices[0].message.content

# If OpenAI fails, the decorator catches the error,
# sends a Flare to jasper_p520, waits for rescue, returns result.
result = ask_llm("Review this code for security issues")
```

#### Running a rescue node

A rescue node listens for incoming flares and processes them with a handler:

```python
from tibet_triage import FlareNode, OllamaHandler

# Listen on P520 with local Ollama
node = FlareNode(
    agent="jasper_p520",
    handler=OllamaHandler(
        base_url="http://localhost:11434",
        default_model="qwen2.5:7b",
    ),
)
node.run()  # Blocks, processes flares as they arrive
```

Or from the CLI:

```bash
# Start a rescue node
tibet-triage flare-node jasper_p520 --model qwen2.5:7b

# Send a one-off flare
tibet-triage flare-send "Explain this error: SIGKILL" jasper_p520
```

#### Convenience function

```python
from tibet_triage import send_flare

# Fire-and-forget or wait for result
result = send_flare(
    prompt="Summarize this document",
    target="jasper_p520",
    model="qwen2.5:32b",
    timeout=120,
)
print(result.result)
```

#### Custom handlers

You can write handlers for any backend — not just Ollama:

```python
from tibet_triage import FlareHandler, FlareResult, Flare

class MyGPUHandler(FlareHandler):
    def can_handle(self, flare: Flare) -> bool:
        return flare.request_type in ("inference", "embedding")

    def handle(self, flare: Flare) -> FlareResult:
        # Route to your custom inference engine
        result = my_engine.generate(flare.request_payload.get("prompt"))
        return FlareResult(
            flare_id=flare.flare_id,
            success=True,
            result=result,
            model_used="my-engine-v3",
            node_agent=self.agent_name,
        )
```

---

## Triage Levels

The risk gate automatically determines what level of human review is needed:

| Level | Who reviews | When triggered |
|-------|-------------|---------------|
| **L0 AUTO** | No human needed | Trust score high, no triggers fired |
| **L1 OPERATOR** | One operator, async | Moderate risk, standard changes |
| **L2 SENIOR** | Senior + co-signer | System-critical, intent mismatch |
| **L3 CEREMONY** | Physical presence required | Catastrophic risk, out-of-band verification |

Trigger rules are configurable:

```yaml
rules:
  - name: large_diff
    trigger: "diff_lines > 500"
    level: L2_SENIOR
  - name: system_files
    trigger: "modified_paths matches '/etc/*'"
    level: L3_CEREMONY
  - name: new_dependencies
    trigger: "new_packages > 0"
    level: L1_OPERATOR
```

---

## Use Cases

### Multi-Agent AI Systems
Fork tokens enable **provable task delegation** between AI agents. Agent A can fork a process, deliver it to Agent B via I-Poll, and receive cryptographic proof that B completed it correctly. The entire chain is auditable.

### Drone Swarms & Edge Computing
A command station creates a UPIP bundle, forks it to N drones as `fragment` type forks (Fork²). Each drone resumes independently, executes its portion, and ACKs back with its result hash. The station can verify all fragments completed and reconstruct the full result.

### Scientific Reproducibility
A researcher captures their complete experiment as a UPIP bundle: exact code, exact dependencies, exact data state, exact results. Another lab can `upip-reproduce` it and get cryptographic proof that their reproduction matches the original.

### CI/CD Pipeline Integrity
Every deployment runs through the Airlock first. The pre-computed diff shows exactly what will change. The risk gate determines if a human needs to review. Fork tokens enable multi-stage pipelines where each stage hands off to the next with full provenance.

### Supply Chain Security
Package maintainers can create UPIP bundles of their build process. Downstream consumers can verify the build is reproducible. Fork tokens enable distributed build verification across multiple independent machines.

### Regulatory Compliance
UPIP bundles provide the evidence chain that compliance frameworks require:
- **SOC 2** — Process integrity evidence with timestamps and actor identity
- **ISO 27001** — Change management audit trail
- **NIS2** — Incident response provenance (who did what, when, why)
- **DORA** — ICT risk management with reproducible testing
- **HIPAA** — Minimum necessary principle proven by diff

---

## Protocol Specification

### UPIP Stack JSON Format

```json
{
  "protocol": "UPIP",
  "version": "1.0",
  "title": "Model Training Run #42",
  "created_by": "lab-gpu-01",
  "created_at": "2026-03-18T14:30:00Z",
  "stack_hash": "upip:sha256:4f2e8a...",
  "state": {
    "state_type": "git",
    "state_hash": "git:a1b2c3d4...",
    "git_remote": "https://github.com/org/repo",
    "git_branch": "main",
    "captured_at": "2026-03-18T14:30:00Z"
  },
  "deps": {
    "python_version": "3.13.5",
    "packages": {"torch": "2.5.0", "tibet-triage": "0.4.1"},
    "deps_hash": "deps:sha256:7c9d..."
  },
  "process": {
    "command": ["python", "train.py", "--epochs", "100"],
    "intent": "Train model v3 on Q1 dataset",
    "actor": "lab-gpu-01",
    "env_vars": {"CUDA_VISIBLE_DEVICES": "0,1"}
  },
  "result": {
    "success": true,
    "exit_code": 0,
    "result_hash": "sha256:b3d1...",
    "stdout_lines": 1247,
    "files_changed": 3
  },
  "fork_chain": []
}
```

### Fork Token JSON Format

```json
{
  "protocol": "UPIP",
  "type": "fork_token",
  "fork": {
    "fork_id": "fork-a7b3c9d2",
    "parent_hash": "sha256:4f2e8a...",
    "parent_stack_hash": "upip:sha256:4f2e8a...",
    "continuation_point": "L4:post_result",
    "intent_snapshot": "Continue training on larger GPU",
    "active_memory_hash": "sha256:9c1a...",
    "fork_type": "script",
    "actor_from": "lab-gpu-01",
    "actor_to": "hpc-cluster",
    "actor_handoff": "lab-gpu-01 -> hpc-cluster",
    "capability_required": {
      "deps": ["torch>=2.0", "tibet-triage>=0.4.0"],
      "gpu": true
    },
    "forked_at": "2026-03-18T14:35:00Z",
    "fork_hash": "fork:sha256:7d3f...",
    "partial_layers": {
      "L1_state": {"hash": "git:a1b2c3d4...", "type": "git"},
      "L2_deps": {"hash": "deps:sha256:7c9d...", "python": "3.13.5"},
      "L3_process": {"command": ["python", "train.py"], "intent": "..."},
      "L4_result": {"hash": "sha256:b3d1...", "exit_code": 0}
    }
  }
}
```

---

## Python API

### Core classes

```python
from tibet_triage.upip import (
    UPIPStack,          # Complete five-layer bundle
    StateCapture,       # L1: Code/data state
    DepsCapture,        # L2: Dependency snapshot
    ProcessDef,         # L3: Command + intent
    ResultCapture,      # L4: Execution result
    VerifyRecord,       # L5: Cross-machine verification
    ForkToken,          # Continuation token for handoff
)

from tibet_triage.ipoll import (
    IPollClient,             # I-Poll HTTP client
    IPollDeliveryResult,     # Fork delivery result
    IPollForkMessage,        # Received fork message
)

from tibet_triage.flare import (
    Flare,                       # SOS token
    FlareResult,                 # Rescue result
    FlareConfig,                 # Explicit opt-in config
    FlareClient,                 # Send flares, wait for results
    FlareNode,                   # Listen for and process flares
    FlareHandler,                # Base handler class
    OllamaHandler,               # Routes to local Ollama
    OpenAICompatibleHandler,     # Routes to any OpenAI-compatible API
    flare_rescue,                # Decorator for automatic failover
    send_flare,                  # Convenience function
)
```

### High-level functions

```python
from tibet_triage.upip import (
    capture_and_run,    # Run process in airlock, create UPIP stack
    save_upip,          # Save stack to .upip.json
    load_upip,          # Load stack from .upip.json
    reproduce_upip,     # Reproduce on another machine
    fork_upip,          # Create fork token from stack
    save_fork,          # Save fork to .fork.json
    load_fork,          # Load fork from .fork.json
    resume_upip,        # Resume a forked process
)
```

### Example: Full fork lifecycle

```python
from tibet_triage.upip import (
    capture_and_run, save_upip, fork_upip, save_fork,
    resume_upip, save_upip,
)
from tibet_triage.ipoll import IPollClient

# === Machine A: Run and fork ===

# Step 1: Run in airlock
stack = capture_and_run(
    command=["python", "analyze.py"],
    source_dir="./data",
    intent="Analyze security logs",
    actor="machine-A",
)
save_upip(stack, "analysis.upip.json")

# Step 2: Fork for handoff
stack, fork = fork_upip(
    bundle_path="analysis.upip.json",
    actor_from="machine-A",
    actor_to="machine-B",
    intent="Continue with deep analysis",
    capability_required={"deps": ["tibet-triage>=0.4.0"]},
)
save_fork(fork, "handoff.fork.json")

# Step 3: Deliver via I-Poll
client = IPollClient(from_agent="machine-A")
client.deliver_fork(fork, to_agent="machine-B")

# === Machine B: Resume ===

# Step 4: Resume the fork
resume_stack, loaded_fork, result, verify = resume_upip(
    fork_path="handoff.fork.json",
    command=["python", "deep_analyze.py"],
    actor="machine-B",
)
save_upip(resume_stack, "deep-analysis.upip.json")

# Step 5: ACK back
client_b = IPollClient(from_agent="machine-B")
client_b.ack_fork(
    fork=loaded_fork,
    resume_hash=resume_stack.stack_hash,
    success=result.success,
)
```

---

## CLI Reference

### Airlock commands

| Command | Description |
|---------|-------------|
| `tibet-triage run <cmd>` | Run command in airlock sandbox |
| `tibet-triage pending` | List pending triage items |
| `tibet-triage review <id>` | Review triage evidence |
| `tibet-triage approve <id>` | Approve with operator identity |
| `tibet-triage reject <id>` | Reject with reason |

### UPIP commands

| Command | Description |
|---------|-------------|
| `tibet-triage upip-export <dir>` | Create UPIP bundle from directory |
| `tibet-triage upip-reproduce <bundle>` | Reproduce bundle on current machine |

### Fork commands

| Command | Description |
|---------|-------------|
| `tibet-triage upip-fork <bundle> -o <fork.json>` | Create fork token |
| `tibet-triage upip-resume <fork.json> -- <cmd>` | Resume a forked process |
| `tibet-triage upip-fork-send <fork.json> <agent>` | Send fork via I-Poll |
| `tibet-triage upip-fork-listen -a <agent>` | Listen for incoming forks |

### Flare commands

| Command | Description |
|---------|-------------|
| `tibet-triage flare-send <prompt> <target>` | Send a flare to a rescue node |
| `tibet-triage flare-node <agent>` | Start a rescue node listener |

### Fork flags

```
--actor-from         Who is forking
--actor-to           Who should resume
--intent             Why this handoff
--fork-type          script | ai_to_ai | human_to_ai | fragment
--continuation       Where to continue (default: L4:post_result)
--memory-blob        Path to .blob file (for AI context forks)
--require-deps       Required packages (comma-separated)
--require-gpu        Require GPU on receiving machine
--deliver <agent>    Deliver via I-Poll after creating
--ipoll-url          Custom I-Poll endpoint
```

### Flare flags

```
--model              Preferred model (default: qwen2.5:7b)
--timeout            Seconds to wait for rescue (default: 60)
--from-agent         Your agent identity (default: flare_client)
--ollama-url         Ollama API URL (default: http://localhost:11434)
--interval           Poll interval in seconds (default: 2.0)
--ipoll-url          Custom I-Poll endpoint
```

---

## Security Model

### No blind trust

tibet-triage is built on a fundamental principle: **the system proves what happened, it doesn't enforce what should happen**. Fork validation doesn't block on hash mismatch — it records the mismatch and lets the verification record speak for itself.

This is deliberate. In adversarial environments, enforcement can be bypassed. Evidence cannot be un-recorded.

### Cryptographic chain

Every artifact has a hash chain:
- **L1-L4 hashes** chain into the **stack hash** (`upip:sha256:...`)
- **Fork token fields** chain into the **fork hash** (`fork:sha256:...`)
- **Fork chain** links parent and child stacks
- **TIBET tokens** sign the entire execution

### Tamper detection

```python
# Fork hash is computed from all critical fields
fork_hash = SHA-256(
    fork_id + parent_hash + parent_stack_hash +
    continuation_point + intent_snapshot +
    active_memory_hash + actor_handoff + fork_type
)
```

Modify any field → hash changes → `fork_hash_match: false` in verification.

### Capability gating

Fork tokens can specify requirements:
```json
{
  "capability_required": {
    "deps": ["torch>=2.0", "tibet-triage>=0.4.1"],
    "gpu": true
  }
}
```

On resume, installed packages are checked against requirements. Missing capabilities are flagged in the verification record.

---

## Part of the TIBET Ecosystem

tibet-triage is one component of the TIBET (Token-based Intent, Behavior, Evidence & Trust) protocol family:

> *Historical note*: `tibet-triage` introduced TIBET as "Transaction/Interaction-Based Evidence Trail" in earlier docs; the canonical expansion is now "Token-based Intent, Behavior, Evidence & Trust" per [`tibet-core`](https://pypi.org/project/tibet-core/) — both refer to the same protocol family.


| Package | Function | PyPI |
|---------|----------|------|
| [tibet-core](https://pypi.org/project/tibet-core/) | Token provenance (ERIN/ERAAN/EROMHEEN/ERACHTER) | [![PyPI](https://img.shields.io/pypi/v/tibet-core)](https://pypi.org/project/tibet-core/) |
| **tibet-triage** | Airlock + UPIP + Fork Tokens + I-Poll | [![PyPI](https://img.shields.io/pypi/v/tibet-triage)](https://pypi.org/project/tibet-triage/) |
| [tibet-pol](https://pypi.org/project/tibet-pol/) | Process integrity checker | [![PyPI](https://img.shields.io/pypi/v/tibet-pol)](https://pypi.org/project/tibet-pol/) |
| [tibet-forge](https://pypi.org/project/tibet-forge/) | Code certification | [![PyPI](https://img.shields.io/pypi/v/tibet-forge)](https://pypi.org/project/tibet-forge/) |
| [tibet-audit](https://pypi.org/project/tibet-audit/) | Compliance reporting | [![PyPI](https://img.shields.io/pypi/v/tibet-audit)](https://pypi.org/project/tibet-audit/) |
| [tibet-trail](https://pypi.org/project/tibet-trail/) | Chain tracing | [![PyPI](https://img.shields.io/pypi/v/tibet-trail)](https://pypi.org/project/tibet-trail/) |
| [tibet-cortex](https://pypi.org/project/tibet-cortex/) | AI orchestration | [![PyPI](https://img.shields.io/pypi/v/tibet-cortex)](https://pypi.org/project/tibet-cortex/) |
| [tibet-ping](https://pypi.org/project/tibet-ping/) | Health + IoT monitoring | [![PyPI](https://img.shields.io/pypi/v/tibet-ping)](https://pypi.org/project/tibet-ping/) |

---

## Standards Alignment

tibet-triage and UPIP align with emerging standards for AI agent interoperability:

- **IETF 6G AI Agent Drafts** — Process integrity for autonomous agent networks
- **EU AI Act** — Audit trail requirements for high-risk AI systems
- **NIST AI RMF** — Risk management framework for AI processes
- **SOC 2 Type II** — Continuous process monitoring and evidence
- **ISO 42001** — AI management system standard

An IETF Internet-Draft for the UPIP protocol is in preparation:
`draft-vandemeent-upip-process-integrity-00`

---

## Requirements

- Python >= 3.10
- [tibet-core](https://pypi.org/project/tibet-core/) >= 0.2.0
- [rich](https://pypi.org/project/rich/) >= 13.0.0

No additional dependencies for I-Poll integration (uses stdlib `urllib`).

## License

MIT — [Humotica AI Lab](https://humotica.com)

## Authors

- J. van de Meent — [jasper@humotica.com](mailto:jasper@humotica.com)
- Root AI — [root_idd@humotica.nl](mailto:root_idd@humotica.nl)

## Credits

Designed by [Jasper van de Meent](https://github.com/jaspertvdm). Built by Jasper and [Root AI](https://humotica.com) as part of [HumoticaOS](https://humotica.com).

---

**Stack-positie:** Groep `agentic` · Bootstrap = OSAPI-handshake naar [`tibet`](https://pypi.org/project/tibet-core/) + [`jis`](https://pypi.org/project/jis-core/) (fail → snaft-rule + tibet-pol-rapport) · ← [`ainternet`](https://pypi.org/project/ainternet/) (network it operates on) · [`tibet-phantom`](https://pypi.org/project/tibet-phantom/) → (state-continuity handoff) · See `STACK.md` · See `demo/golden-path/` for the spine end-to-end.

---

## Enterprise

For private hub hosting, SLA support, custom integrations, or compliance guidance:

| | |
|---|---|
| **Enterprise** | enterprise@humotica.com |
| **Support** | support@humotica.com |
| **Security** | security@humotica.com |

See [ENTERPRISE.md](ENTERPRISE.md) for details.

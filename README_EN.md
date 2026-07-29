# Kong Deyu

AI Infra engineer focused on the substrate underneath LLMs and Agents.
Currently building inference runtimes, micro-VM sandboxes, and agent runtimes.

MS, Computer Science, Nanjing University · Intern @ Alibaba Cloud (Elastic Compute / Kangaroo)
GitHub: [@Deyu-Kong](https://github.com/Deyu-Kong) · Email: dykong@smail.nju.edu.cn · Blog: https://deyu-kong.github.io

---

## Focus areas

```
                       AI Infra        Agent Infra         Agent
                       --------        -----------         -----
                  inference runtimes   sandbox / VMM    agent runtimes
                  CUDA kernels         container         RAG / memory
                  KV-cache mgmt        Linux kernel      multi-agent / A2A
                  scheduling           virtio / gVisor   tool use
```

I work across all three layers because production agents need
fast inference, safe isolation, and reliable orchestration at the same time.

---

## Selected work

### AI Infra — LLM Inference

`mini-infer` — a C++/CUDA inference engine for Qwen2.5, built from scratch.
Repo: https://github.com/Deyu-Kong/mini-infer

Implemented:

- Hand-written CUDA kernels: RMSNorm, RoPE, Softmax, SwiGLU (FP16 storage, FP32 accumulate)
- PagedAttention — block-table KV cache, free-list block allocator
- Continuous batching — dynamic request scheduler
- Speculative decoding — 1.5B draft + 7B target, accept/reject, gamma in {4, 8}
- Prefix caching — radix trie, FNV-1a block hash, LRU eviction, copy-on-write

Ablation (Qwen2.5-Coder-7B + 1.5B draft, RTX A6000, FP16):

| Config                              | TTFT (ms) | TPOT (ms) | Throughput (tok/s) | Speedup |
| ----------------------------------- | --------- | --------- | ------------------ | ------- |
| E0 naive autoregressive             | 120       | 45        | 22.2               | 1.00x   |
| E1 + PagedAttention                 | 115       | 43        | 23.3               | 1.05x   |
| E2 + continuous batching            | 85        | 35        | 28.6               | 1.29x   |
| E3 + speculative decoding (gamma=4) | 82        | 18        | 55.6               | 2.50x   |
| E4 + speculative decoding (gamma=8) | 80        | 15        | 66.7               | 3.00x   |
| E5 + prefix caching                 | 45        | 18        | 55.6               | 2.50x (TTFT -45%) |

Memory utilization: 60% -> 95%.  Max concurrent sequences: 4x.

### Agent Infra — Sandbox and VMM

Work @ Alibaba Cloud (Elastic Compute / Kangaroo), 2026.06 - present.

- Cold-start optimization on RunD, a gVisor-based micro-VM platform.
  Memory overcommit + template boot. Concurrent pods 70 -> 77.
  Mean boot time 2.8s -> 1.7s.
- Design proposal: remove the guest network stack from MicroVM and route
  network through trusted host APIs. Reduces compute redundancy and
  shrinks the agent-side attack surface.

### Agent — LLM-based systems

Tencent QQ, AI Engineer Intern, 2026.04 - 2026.06.

- RAG retrieval module: 6 heterogeneous data sources, dual-track consistency
  (incremental sync + full rebuild), time-decayed hybrid retrieval.
  Retrieval accuracy 92%.
- A2A multi-agent pipeline: routing constraints, context management,
  long-term memory ("core + fact" two-track with dream compression).
  Long-term memory eval pass rate 90%+.
- Eval harness: pipeline completeness, memory pass rate, negative
  boundaries.

Intel CCG / CGAI (NPU Optimization), 2026.01 - 2026.04.

- OpenVINO deployment of Qwen2.5-VL and Qwen2.5-Embedding on Intel NPU.
  ONNX -> OpenVINO IR -> static blob. Quantization, operator adaptation,
  static graph compilation.
- On-device PC assistant: multimodal perception (screen capture + IO
  events) -> hierarchical time-series summarization -> persistent
  vector memory -> LangChain/LangGraph agent with proactive service.

### OS and Distributed Systems

`UOS` / `UbiWare` — Software-Defined Ubiquitous Operating System.
Reported at SOSP 2025 WUOS Workshop.

- OS abstraction: lift traditional OS components into hierarchical,
  bidirectionally-mapped computational components. Software twin +
  tree-structured collaborative agents.
- Middleware `Ubiware`: cross-device resource abstraction, scheduling,
  on-demand launch. End-to-end workflow: voice perception -> LLM
  semantic parse -> structured instruction -> scene orchestration.
- National 3rd prize, China Graduate OS Open-Source Innovation Contest.

---

## Selected publications and awards

- SOSP 2025 WUOS Workshop, first author (UOS / UbiWare).
- 3DV Demo Paper, first author (ReID system, national-level
  undergrad innovation project).
- China Graduate OS Open-Source Innovation Contest, national 3rd prize.
- Huawei Scholarship, Nanjing University (2025).
- Leijun Computer Scholarship, Wuhan University (2023).

---

## Tech I use

```
languages    : C++17, CUDA 12.x, Python, Rust, Shell
inference    : vLLM, PyTorch, Transformers, HuggingFace, OpenVINO
agent        : LangChain, LangGraph, ReAct, Function Calling, RAG
systems      : Linux kernel, gVisor / RunD / Firecracker, virtio,
               VMM, Docker, QEMU
tools        : Git, CMake, Codex, OpenCode, Claude Code
```

---

## Reading

- Kwon et al. "Efficient Memory Management for Large Language Model Serving with PagedAttention" (SOSP 2023)
- Leviathan et al. "Fast Inference from Transformers via Speculative Decoding" (ICML 2023)

---

Open to: AI Infra, Agent Infra (container / virtualization / kernel),
and Agent engineering roles. Reach out at dykong@smail.nju.edu.cn.
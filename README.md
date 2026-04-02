<div align="center">

<h1>⚡ NeuroFusion SoC</h1>

<p><strong>A Heterogeneous Multi-Accelerator System-on-Chip for AI-Centric Workloads</strong></p>

<p>
  <img src="https://img.shields.io/badge/Status-Under%20Development-orange?style=for-the-badge" />
  <img src="https://img.shields.io/badge/ISA-RISC--V%20RV32IM%2FRV64GC-blue?style=for-the-badge" />
  <img src="https://img.shields.io/badge/HDL-Verilog%20%2F%20SystemVerilog-green?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Flow-RTL--to--GDSII-red?style=for-the-badge" />
  <img src="https://img.shields.io/badge/License-TBD-lightgrey?style=for-the-badge" />
</p>

</div>

---

## 📌 Table of Contents

- [Overview](#-overview)
- [Architecture](#-architecture)
- [Components](#-components)
- [Memory Subsystem](#-memory-subsystem)
- [Security & Cryptography](#-security--cryptography)
- [DFT Strategy](#-dft-strategy)
- [RTL-to-GDSII Flow](#-rtl-to-gdsii-flow)
- [Area Optimization Goals](#-area-optimization-goals)
- [Research Contributions](#-research-contributions)
- [Tech Stack](#-tech-stack)
- [Repository Structure](#-repository-structure)
- [Getting Started](#-getting-started)
- [Roadmap](#-roadmap)
- [References](#-references)
- [Contributing](#-contributing)
- [License](#-license)

---

## 🧠 Overview

**NeuroFusion SoC** is a research-grade, heterogeneous multi-accelerator System-on-Chip that integrates a wide array of compute engines — from a **RISC-V CPU** and **GPU** to **NPU/TPU-class AI accelerators**, a **hardware crypto engine**, and a robust **memory subsystem** — all connected via a scalable **Network-on-Chip (NoC)** fabric.

The design targets **AI-centric, general-purpose, and security workloads** simultaneously, and is built with a full **RTL-to-GDSII** implementation goal with **area efficiency** as a first-class design constraint.

> *"NeuroFusion is not just a CPU with AI attached — it is an AI-native compute platform where every unit is a peer."*

### Why NeuroFusion?

Modern SoC design is shifting toward **heterogeneous architectures** that combine CPUs, GPUs, NPUs, and domain-specific accelerators. Leading platforms like Apple M-series, Qualcomm Snapdragon, and NVIDIA Orin demonstrate that tight on-chip integration of diverse compute engines enables lower latency, higher energy efficiency, and superior performance compared to discrete accelerator approaches.

NeuroFusion SoC is designed to:

- Serve as a **research platform** for heterogeneous SoC design
- Enable **custom AI acceleration** research with a RISC-V backbone
- Provide a **DFT-aware** architecture from day one
- Target a **full ASIC flow** (not just simulation)

---

## 🏗️ Architecture

NeuroFusion SoC is organized around a **central NoC fabric** — every compute and memory block is a peer node. There is no single dominant processor; instead, all units collaborate through the interconnect.

```
╔══════════════════════════════════════════════════════════════════╗
║                         NeuroFusion SoC                          ║
║                                                                  ║
║   ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────────┐     ║
║   │   CPU    │  │   GPU    │  │             │  Custom AI   │     ║
║   │ (RISC-V) │  │ Parallel │  │          │  │  Accelerator │     ║
║   └────┬─────┘  └────┬─────┘  └────┬─────┘  └──────┬───────┘     ║
║        │             │             │                │            ║
║   ┌────┴─────────────┴─────────────┴────────────────┴────────┐   ║
║   │                    NoC Fabric (AXI / AHB)                 │  ║
║   └────┬─────────────┬─────────────┬────────────────┬─────────┘  ║
║        │             │             │                │            ║
║   ┌────┴─────┐  ┌────┴─────┐  ┌───┴──────┐  ┌─────┴──────┐    ║
║   │   DSP    │  │  Memory  │  │  Crypto  │  │    DFT     │    ║
║   │          │  │Subsystem │  │  Engine  │  │ (JTAG/BIST)│    ║
║   └──────────┘  └──────────┘  └──────────┘  └────────────┘    ║
╚══════════════════════════════════════════════════════════════════╝
```

### Design Philosophy

| Principle | Description |
|-----------|-------------|
| **NoC-Centric** | All compute blocks are peer nodes on the NoC; no bus bottleneck |
| **Heterogeneous** | Each unit is purpose-built for its workload domain |
| **AI-Native** | NPU/TPU and AI accelerator are first-class compute citizens |
| **Secure by Design** | Crypto engine + TrustZone-style isolation built in |
| **DFT-First** | Testability is not an afterthought — scan, JTAG, and BIST planned from RTL |
| **Area-Aware** | Every block is designed with area budgets in mind for GDSII feasibility |

---

## ⚙️ Components

### 1. 🖥️ CPU — RISC-V Core

| Property | Value |
|----------|-------|
| ISA | RISC-V RV32IM / RV64GC |
| Extensions | M (Multiply), C (Compressed), V (Vector — planned) |
| Role | Control flow, OS tasks, general-purpose computation |
| Pipeline | In-order (initial), out-of-order (future) |

- Handles boot, OS management, and control plane tasks
- Custom RISC-V instructions planned for tight CPU ↔ AI accelerator coupling
- RISC-V Vector Extension (RVV) integration planned for HPC workloads

---

### 2. 🎮 GPU — Parallel Processing Unit

| Property | Value |
|----------|-------|
| Architecture | Massively parallel SIMD |
| Workloads | Vector math, graphics, parallel AI inference |
| Memory Access | Shared memory via NoC |

- Handles parallel workloads unsuitable for the scalar CPU
- Tile-based rendering and GPGPU compute support planned
- Interfaces directly with the NPU for collaborative AI pipelines

---

### 3. 🧬 NPU / TPU — Neural Processing Engine

| Property | Value |
|----------|-------|
| Type | Systolic array / Matrix engine |
| Precision | INT4, INT8, FP16, BF16 |
| Workloads | Neural network inference, tensor operations |
| Target | On-chip AI inference at low power |

- Optimized for deep learning inference (CNNs, Transformers, LSTMs)
- INT4/INT8 support for energy-efficient edge inference
- Tensor operation acceleration for matrix-heavy AI workloads
- Inspired by Google TPU systolic array and Qualcomm Hexagon NPU designs

---

### 4. 🚀 Custom AI Accelerator

- Domain-specific accelerator for targeted AI workloads (research contribution)
- Configurable dataflow: weight-stationary, output-stationary, row-stationary
- Designed to complement the NPU for specialized model architectures
- Key research focus: novel dataflow + area-efficient MAC array design

---

### 5. 🎛️ DSP — Digital Signal Processor

- Handles audio, communication, and sensor signal processing
- FFT, FIR/IIR filtering, modulation/demodulation
- Low-power operation with clock gating support
- Optional module — included for completeness in a real-world SoC

---

### 6. 🔗 NoC — Network-on-Chip

| Property | Value |
|----------|-------|
| Protocol | AXI4 / AHB / APB (AMBA) |
| Topology | Mesh / Ring (configurable) |
| Arbitration | Round-robin + priority-based |

- Central communication fabric connecting all compute nodes and memory
- Designed for low-latency, high-throughput data movement
- NoC-aware accelerator placement is a research goal (see Research Contributions)
- Scales cleanly as more accelerator blocks are added

---

## 💾 Memory Subsystem

The memory system is the most critical bottleneck in heterogeneous AI SoCs. NeuroFusion addresses this with a layered memory architecture:

```
┌─────────────────────────────────────────────────────┐
│                  Memory Hierarchy                   │
│                                                     │
│   CPU L1 Cache (I$ + D$)                           │
│   └── Shared L2 Cache                              │
│         └── Shared L3 / Last-Level Cache (LLC)     │
│               └── On-Chip SRAM Banks               │
│                     └── DRAM Controller            │
│                           └── External LPDDR5 / DDR5│
└─────────────────────────────────────────────────────┘
```

| Layer | Description |
|-------|-------------|
| **L1 Cache** | Per-CPU instruction and data caches |
| **L2 Cache** | Shared across CPU cluster |
| **L3 / LLC** | Last-level cache shared by all compute blocks |
| **On-chip SRAM** | Scratchpad buffers for NPU/AI accelerator |
| **DRAM Controller** | Interface to external LPDDR5 / DDR5 memory |

### Key Design Goals
- **Memory bandwidth optimization** for AI inference workloads (memory bandwidth is the #1 bottleneck in LLM/Transformer inference)
- **Coherent memory access** between CPU and accelerator blocks
- Scratchpad SRAM designed for NPU local data reuse (reduces DRAM accesses)
- AHB Memory-Mapped I/O for peripheral registers

---

## 🔐 Security & Cryptography

Security is integrated at the hardware level, not bolted on:

### Hardware Cryptographic Accelerator

| Algorithm | Use Case |
|-----------|----------|
| **AES-128/256** | Symmetric encryption / decryption |
| **RSA-2048/4096** | Asymmetric key exchange |
| **SHA-256/512** | Hash computation, integrity verification |
| **TRNG** | True Random Number Generator (entropy source) |

### Security Architecture

- **Secure Boot**: ROM-based boot with cryptographic signature verification
- **TrustZone-style Isolation**: Secure and non-secure world separation
- **Key Storage**: Hardware-protected key registers (not accessible from software)
- **Side-channel Resistance**: Constant-time crypto operation targets
- **Secure Memory Region**: Isolated SRAM region accessible only in secure mode

---

## 🧪 DFT Strategy

Design for Testability (DFT) is planned from the **RTL phase**, not retrofitted:

### Supported DFT Techniques

| Technique | Description |
|-----------|-------------|
| **Full-Scan** | All flip-flops connected in scan chain |
| **JTAG (IEEE 1149.1)** | Boundary scan, debug access |
| **MBIST** | Memory Built-In Self-Test for all SRAM blocks |
| **LBIST** | Logic BIST for random pattern testing |
| **Compression** | EDT/XTOL scan compression for test time reduction |

### DFT Architecture Goals

- Scan chain partitioning across compute domains (CPU, GPU, NPU separately controllable)
- NoC-aware scan insertion (minimize routing overhead)
- ATPG-friendly design rules enforced from RTL
- **Research direction**: RL-based scan chain optimization (see Research Contributions)

---

## 🔬 RTL-to-GDSII Flow

NeuroFusion targets a **complete ASIC implementation flow**:

```
RTL (Verilog/SystemVerilog)
        │
        ▼
Lint + CDC + RDC Checks
(Spyglass / Questa CDC)
        │
        ▼
Functional Verification
(Verilator / ModelSim / UVM Testbenches)
        │
        ▼
Logic Synthesis
(Cadence Genus / Synopsys Design Compiler)
        │
        ▼
DFT Insertion
(Scan, BIST, JTAG)
        │
        ▼
Floorplanning + Power Planning
(Cadence Innovus / Synopsys ICC2)
        │
        ▼
Place & Route
        │
        ▼
Static Timing Analysis (STA)
(Cadence Tempus / Synopsys PrimeTime)
        │
        ▼
Physical Verification
(DRC + LVS via Calibre / PVS)
        │
        ▼
GDSII Tapeout
```

### Process Target
- **Node**: 28nm / 22nm FD-SOI (research target) or open PDK (Sky130 / GF180 for open-source flow)
- **Standard Cell Library**: Compatible with target PDK
- **Memory Compilers**: SRAM macros generated via memory compiler

---

## 📐 Area Optimization Goals

Area efficiency is a **core design constraint**, not an afterthought:

| Technique | Target Block |
|-----------|-------------|
| **Logic sharing** across NPU MAC arrays | AI Accelerator |
| **Clock gating** to reduce dynamic area impact | All blocks |
| **Operand isolation** on datapath inputs | CPU, NPU |
| **Resource sharing** of crypto primitives | Crypto Engine |
| **Hierarchical floorplanning** | Full SoC |
| **Cell sizing optimization** during synthesis | All blocks |
| **Datapath retiming** | CPU pipeline, NPU |

### Area Budget (Target — Subject to Change)

| Block | Estimated Area Share |
|-------|---------------------|
| CPU (RISC-V) | ~15% |
| GPU | ~20% |
| NPU / TPU | ~20% |
| Custom AI Accelerator | ~10% |
| Memory System | ~20% |
| NoC | ~5% |
| Crypto Engine | ~3% |
| DSP | ~4% |
| DFT + Misc | ~3% |

---

## 🔬 Research Contributions (Planned)

NeuroFusion SoC is designed to support and generate novel research:

1. **RL-Driven DFT Optimization**
   > Using Reinforcement Learning to automate and optimize scan chain insertion, ordering, and compression — reducing test time and area overhead.

2. **NoC-Aware Accelerator Placement**
   > Intelligent placement of GPU, NPU, and custom accelerator blocks relative to the NoC fabric to minimize data movement latency and congestion.

3. **Custom RISC-V Instructions for AI**
   > Designing new RISC-V custom instructions (using RISC-V custom opcode space) to enable tight CPU ↔ AI accelerator interaction with minimal overhead.

4. **Area-Efficient MAC Array Design**
   > Novel matrix multiply-accumulate array architectures targeting area reduction without performance loss for the NPU.

5. **Heterogeneous Workload Scheduling**
   > Compiler and hardware-assisted intelligent scheduling of AI tasks across GPU, NPU, and custom accelerator based on workload characteristics.

6. **Full RTL-to-GDSII Flow on Open PDK**
   > Complete implementation using open-source EDA tools (OpenROAD + Sky130 PDK) for reproducibility.

7. **Memory Bandwidth Optimization for LLM Inference**
   > Addressing the memory wall problem for transformer-based models running on the NPU.

---

## 🛠️ Tech Stack

| Category | Tools / Technologies |
|----------|---------------------|
| **HDL** | Verilog, SystemVerilog |
| **ISA** | RISC-V (RV32IM / RV64GC / RVV) |
| **Simulation** | Verilator, ModelSim / QuestaSim |
| **Verification** | UVM, cocotb |
| **Synthesis** | Cadence Genus, Synopsys Design Compiler, Yosys (open) |
| **PnR** | Cadence Innovus, Synopsys ICC2, OpenROAD (open) |
| **STA** | Cadence Tempus, Synopsys PrimeTime |
| **DFT** | Cadence Modus, Synopsys DFT Compiler |
| **Physical Verification** | Mentor Calibre, Synopsys PVS |
| **Open PDK** | SkyWater Sky130, GF180MCU |
| **RISC-V Toolchain** | GCC (riscv-gnu-toolchain), LLVM |
| **Version Control** | Git / GitHub |

---

## 📁 Repository Structure

```
NeuroFusion-SoC/
│
├── rtl/
│   ├── cpu/                  # RISC-V CPU core
│   │   ├── core/
│   │   ├── pipeline/
│   │   └── csr/
│   ├── gpu/                  # GPU parallel processing unit
│   ├── npu/                  # NPU / TPU-class AI engine
│   │   ├── systolic_array/
│   │   └── datapath/
│   ├── ai_accel/             # Custom AI accelerator
│   ├── dsp/                  # Digital Signal Processor
│   ├── noc/                  # Network-on-Chip fabric
│   │   ├── router/
│   │   └── arbiter/
│   ├── memory/               # Memory subsystem
│   │   ├── cache/
│   │   ├── sram/
│   │   └── dram_ctrl/
│   ├── crypto/               # Cryptographic engine
│   │   ├── aes/
│   │   ├── rsa/
│   │   ├── sha/
│   │   └── trng/
│   └── top/                  # Top-level SoC integration
│
├── tb/                       # Testbenches
│   ├── unit/                 # Unit-level testbenches
│   └── system/               # System-level testbenches
│
├── sim/                      # Simulation scripts and logs
│   ├── run_sim.sh
│   └── wave/
│
├── dft/                      # DFT files
│   ├── scan/                 # Scan chain definitions
│   ├── bist/                 # BIST controllers
│   └── jtag/                 # JTAG TAP controller
│
├── syn/                      # Synthesis scripts
│   ├── constraints/          # SDC timing constraints
│   └── scripts/
│
├── pnr/                      # Place & Route scripts
│   ├── floorplan/
│   └── scripts/
│
├── docs/                     # Documentation
│   ├── architecture/
│   ├── spec/
│   └── figures/
│
├── scripts/                  # Utility scripts
│
└── README.md
```

---

## 🚀 Getting Started

### Prerequisites

```bash
# RISC-V GNU Toolchain
sudo apt install gcc-riscv64-unknown-elf

# Verilator (simulation)
sudo apt install verilator

# Icarus Verilog (optional)
sudo apt install iverilog

# GTKWave (waveform viewer)
sudo apt install gtkwave
```

### Clone the Repository

```bash
git clone https://github.com/yourusername/NeuroFusion-SoC.git
cd NeuroFusion-SoC
```

### Run Simulation (Example — CPU Core)

```bash
cd sim/
./run_sim.sh cpu
```

### Synthesize with Yosys (Open Flow)

```bash
cd syn/
yosys -s scripts/synth_neurofusion.ys
```

---

## 🗺️ Roadmap

### Phase 1 — RTL Design *(Current)*
- [x] Architecture definition
- [ ] RISC-V CPU core (RV32IM)
- [ ] NPU systolic array (basic)
- [ ] NoC fabric (simple ring topology)
- [ ] AHB Memory subsystem + SRAM
- [ ] Crypto engine (AES core)
- [ ] JTAG TAP controller

### Phase 2 — Verification
- [ ] Unit testbenches for all blocks
- [ ] System-level integration testbench
- [ ] UVM-based NPU verification
- [ ] Functional coverage closure

### Phase 3 — DFT Integration
- [ ] Full-scan insertion
- [ ] MBIST for SRAM blocks
- [ ] ATPG fault simulation
- [ ] RL-based scan optimization (research)

### Phase 4 — Synthesis & PnR
- [ ] Logic synthesis with Yosys + ABC
- [ ] OpenROAD place & route (Sky130 target)
- [ ] STA sign-off
- [ ] Physical verification (DRC/LVS)

### Phase 5 — Research Paper
- [ ] Architecture paper draft
- [ ] DFT optimization paper
- [ ] Submission to DAC / TVLSI / ICCAD

---

## 📚 References

- [RISC-V ISA Specification](https://riscv.org/technical/specifications/)
- [RISC-V Vector Extension (RVV)](https://github.com/riscv/riscv-v-spec)
- [OpenROAD Project](https://openroad.readthedocs.io/)
- [SkyWater Sky130 PDK](https://github.com/google/skywater-pdk)
- [NVDLA Deep Learning Accelerator](http://nvdla.org/)
- [Qualcomm Hexagon NPU Architecture](https://www.qualcomm.com/research/artificial-intelligence)
- [Google TPU Architecture Paper](https://arxiv.org/abs/1704.04760)
- [AMBA AXI Protocol Specification](https://developer.arm.com/documentation/ihi0022)
- [IEEE 1149.1 JTAG Standard](https://standards.ieee.org/ieee/1149.1/10578/)
- *In-Network Computing: The New Bottleneck in AI Accelerator Systems*, 2024-2025

---

## 🤝 Contributing

Contributions, ideas, and collaborations are welcome!

If you're interested in:
- RTL design of any SoC block
- Verification (UVM, cocotb)
- DFT / ATPG
- AI accelerator microarchitecture
- Open-source EDA flow

Please open an issue or reach out.

```
1. Fork the repository
2. Create your feature branch: git checkout -b feature/your-block
3. Commit your changes: git commit -m "Add: your-block initial RTL"
4. Push and open a Pull Request
```

---

## 📜 License

License to be decided. Likely Apache 2.0 or CERN OHL for hardware.

---

## ⭐ Acknowledgements

NeuroFusion SoC is inspired by:
- The open hardware movement and RISC-V International
- Modern heterogeneous SoC designs (Apple M-series, Qualcomm Snapdragon, NVIDIA Orin)
- Open-source EDA initiatives (OpenROAD, Yosys, Sky130 PDK)
- Research in AI accelerator architecture, DFT optimization, and NoC design

---

<div align="center">

**NeuroFusion SoC** — *Fusing Intelligence. Unifying Compute.*

⭐ Star this repo if you find it useful!

</div>

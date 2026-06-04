# TCA Silicon

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
[![Target](https://img.shields.io/badge/Target-Ibex%20RISC--V-orange)](#)
[![Status](https://img.shields.io/badge/Status-Experimental-yellow)](#)

The **Trusted Computing Architecture (TCA) Silicon** repository contains the hardware-assisted RISC-V security implementation for the Sentinel Stack. TCA explores semantic policy enforcement directly at the architecture level, providing taint-aware execution mediation and architectural information flow control (IFC).

## Research Status

TCA Silicon is currently a research and simulation prototype.

Current validation demonstrates:
- simulated QEMU exception semantics
- architectural taint propagation behavior
- custom trap routing

The project does NOT yet provide:
- synthesized hardware guarantees
- timing validation
- side-channel resistance
- speculative execution analysis
- FPGA or ASIC validation

## Overview

Modern hardware architectures typically rely on MMU boundaries and privilege rings to protect memory. However, advanced vulnerabilities (like data-oriented attacks, confused deputies, or timing side-channels) can often circumvent these abstractions.

**TCA** bridges the gap between software intent and hardware enforcement by introducing:
*   **Compiler-Linked Intent Propagation:** Software intents compiled by the Telos language and Sentinel LLVM passes are lifted directly into custom hardware opcodes (e.g., `llvm.telos.intent.start`).
*   **Taint-Aware Execution Mediation:** Architectural taint state propagates across monitored memory operations.
*   **Hardware IFC Policy Enforcement:** The simulated architecture evaluates intent hashes against the current taint color during MMIO operations and context switches.
*   **Custom Exception Semantics:** If an unauthorized flow is detected, the pipeline aborts the transaction and traps the core into Machine Mode via `RISCV_EXCP_TCA_INTENT_VIOLATION` (0x1b).

## Architecture

This repository builds upon the [lowRISC Ibex](https://github.com/lowRISC/ibex) 32-bit RISC-V core. 

### Core Components
- `rtl/ibex`: The embedded submodule containing the baseline RTL implementation of the Ibex core.
- `synth/synth.ys`: Yosys synthesis script targeting the specific modifications and hardware hooks integrated for TCA semantic tracking.

## Validation Stages

*   **Phase 1:** QEMU semantic validation
*   **Phase 2:** Verilator/RTL validation
*   **Phase 3:** FPGA synthesis
*   **Phase 4:** Physical timing characterization

## Getting Started

### Prerequisites

*   [Yosys](https://yosyshq.net/yosys/) - For RTL synthesis.
*   [Verilator](https://www.veripool.org/verilator/) - For generating the C++ cycle-accurate simulation model.
*   [RISC-V GNU Compiler Toolchain](https://github.com/riscv-collab/riscv-gnu-toolchain)

### Initialization

Because this repository uses a submodule for the core RTL, ensure you clone recursively:

```bash
git clone --recursive https://github.com/nevinshine/tca-silicon.git
cd tca-silicon
```

If you have already cloned the repository without the recursive flag, you can initialize the submodule using:

```bash
git submodule update --init --recursive
```

### Synthesis

To synthesize the TCA-enabled core logic, run the included Yosys script:

```bash
yosys -s synth/synth.ys
```

## Integration with Sentinel Stack

TCA Silicon is meant to be verified against the software abstractions provided in the main Sentinel Stack monorepo.
*   **RISC-V QEMU Simulator:** Pre-silicon semantic validation is available in the `riscv-tca-sim` QEMU fork.
*   **Telos Compiler:** Intent instrumentation is injected by `telos-lang` and the `SentinelPass` LLVM plugin.

## License

This project is licensed under the Apache License, Version 2.0. See the `LICENSE` file for details.

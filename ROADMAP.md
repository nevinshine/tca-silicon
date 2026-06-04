# Project Roadmap

The TCA Silicon project is structured into distinct, sequential engineering phases to maintain strict scope discipline and ensure semantic correctness before committing to physical hardware constraints.

## Phase 1: Semantic Validation (Current)
*Target: QEMU Software Simulator (`riscv-tca-sim`)*

*   **Goal:** Prove the mathematical viability of intent-based execution mediation.
*   **Milestones:**
    *   [x] Define custom RISC-V exception semantics (`0x1b`).
    *   [x] Instrument MMU datapath for simulated taint propagation.
    *   [x] Generate deterministic exception traces from bare-metal payloads.

## Phase 2: RTL Validation
*Target: Verilator C++ Model & SystemVerilog Testbenches (`tb/`)*

*   **Goal:** Port the validated semantics into cycle-accurate RTL logic wrapping the lowRISC Ibex core.
*   **Milestones:**
    *   [ ] Implement physical TCA CSR registers in SystemVerilog.
    *   [ ] Inject combinatorial mediation logic into the Ibex Load/Store Unit (LSU).
    *   [ ] Run Verilator cycle-accurate simulations to verify trap assertion timings.

## Phase 3: FPGA Synthesis
*Target: Xilinx Artix-7 or similar generic FPGA fabric*

*   **Goal:** Synthesize the modified Ibex core to physical bitstreams and validate real-world timing.
*   **Milestones:**
    *   [ ] Map `synth.ys` output to physical FPGA slice primitives.
    *   [ ] Perform physical timing characterization (setup/hold violation analysis).
    *   [ ] Boot the Sentinel Stack (Telos Runtime) natively on the synthesized core.

## Phase 4: Advanced Research

Once Phase 3 establishes a stable baseline, the project transitions into advanced hardware security research.

*   **Physical Silicon Aspirations:** Preparation for an eventual ASIC tape-out.
*   **Speculative Execution Research:** Analyzing transient execution hazards (e.g., Spectre-style bounds bypass) against the TCA intent evaluation logic.
*   **Side-Channel Resilience:** Mitigating power analysis and differential timing attacks that attempt to infer intent hash state from the mediation unit.

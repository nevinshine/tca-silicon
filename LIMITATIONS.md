# Project Limitations

This repository represents an ongoing research effort. To prevent overclaiming security guarantees, this document explicitly details the boundaries and limitations of the current architecture.

## Execution Model Constraints

> [!WARNING]
> **The current architecture strictly assumes an in-order execution model.**

The semantic proofs established in QEMU rely on sequential, blocking instruction retirement. Once out-of-order (OoO) execution models are introduced, the architecture becomes susceptible to immense complexity regarding:
*   Speculative taint bypass.
*   Transient execution information leakage.
*   Reorder buffer hazards during exception assertion.

## Unproven Capabilities

The TCA Silicon project currently provides **none** of the following guarantees. Relying on this architecture for production security workloads is fundamentally unsafe until these limitations are addressed in later phases of the roadmap.

*   **No Silicon Proof:** Hardware enforcement exists purely as simulated semantics. Synthesized RTL has not yet been physically validated on an FPGA or ASIC.
*   **No Timing Guarantees:** Cycle penalties for the inline memory mediation logic are undefined. We cannot guarantee the architecture meets critical setup/hold timing paths.
*   **No Speculative Execution Handling:** The architecture has zero mitigation for Spectre/Meltdown class transient vulnerabilities traversing the taint tracking logic.
*   **No Side-Channel Mitigation:** The cryptographic intent verification unit currently lacks blinding or constant-time guarantees, rendering it theoretically vulnerable to power analysis (DPA) and differential timing attacks.
*   **No Formal Verification:** The architectural invariants have not been subjected to mathematically exhaustive formal verification (e.g., using SymbiYosys or JasperGold).
*   **No Multiprocessor Semantics:** The taint propagation model assumes a single-core execution environment.
*   **No Cache-Coherency Enforcement:** Taint state behavior across multiprocessor snooping protocols (e.g., MESI) or cache evictions is undefined and unenforced.

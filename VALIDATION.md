# Validation Methodology and Evidence

This document tracks the scientific and engineering validation state of the Trusted Computing Architecture (TCA). To maintain strict academic and professional credibility, all claims are rigorously categorized by their current validation status.

## 1. Validated

The following architectural semantics have been conclusively demonstrated within a fully simulated execution environment.

*   **QEMU Exception Routing:** We have deterministically proven that the customized QEMU MMU translation pipeline successfully asserts the `RISCV_EXCP_TCA_INTENT_VIOLATION` (0x1b) exception vector upon detecting a policy violation.
*   **Machine-Mode (PRV_M) Auditing:** Bare-metal operating system workloads executing without MMU paging enabled are successfully intercepted and evaluated by the TCA physical memory hooks.
*   **Synchronous Execution Halts:** The exception successfully halts the current payload, dropping the core into the configured Machine Mode trap handler and populating the architectural fault registers (`mepc`, `mtval`).

## 2. Partially Validated

The following features have been proven at a high level but require deeper granular testing or face current abstraction limitations.

*   **Taint Propagation Semantics:** Current validation tracks a binary taint flag (`tca_taint_flag`) per hardware thread context. While this correctly evaluates single-color flows, fine-grained register-level or cache-line-level taint tracking is currently abstracted away.
*   **Intent Resolution:** The hardware correctly reads the intent hash from `CSR_TCA_CFG`. However, complex cryptographic intent verification (e.g., verifying multi-party signed network intents) is currently simulated as a static bounds check.

## 3. Not Yet Validated

The following critical hardware behaviors remain completely unverified. The project explicitly makes no guarantees regarding these factors until Phase 2 and Phase 3 are complete.

*   **RTL Timing Behavior:** The exact cycle penalty incurred by the TCA enforcement logic residing in the load/store pipeline.
*   **Speculative Execution Interactions:** Whether a transient instruction window can bypass the intent check before the pipeline flushes the aborted transaction.
*   **Cache Coherency Effects:** How taint colors persist or invalidate across L1/L2 cache evictions and multiprocessor (SMP) cache snooping protocols.
*   **Physical Side-Channel Resistance:** Resistance to power analysis or differential timing attacks against the intent verification unit.
*   **FPGA / ASIC Synthesis:** Total logic gate overhead and routing congestion on physical silicon substrates.

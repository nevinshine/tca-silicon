# Canonical Architectural Specification

This document defines the mathematical semantics, trust boundaries, and execution pipeline behavior of the Trusted Computing Architecture (TCA).

> [!WARNING]
> **SIMULATION VS. PHYSICAL GUARANTEES**
> It is imperative to distinguish between the current state of this repository and the physical hardware it models. 
> 
> *   **Simulated Architectural Semantics:** The current logic validated via QEMU perfectly enforces taint propagation and exception routing *within the mathematical bounds of the simulator*.
> *   **Physical Hardware Guarantees:** These simulated bounds do **not** yet translate into synthesized silicon proofs. Transistor-level enforcement (e.g., side-channel immunity, glitch resistance, speculative execution gating) remains explicitly outside the scope of Phase 1 and Phase 2.

## 1. Taint Model

The TCA taint model dictates how data sensitivity propagates across the physical execution fabric.

*   **Taint Sources (`CSR_TCA_ADDR0`):** Specific memory ranges are mathematically bound to an intent color. Any load operation originating from this region asserts the architectural taint state on the pipeline.
*   **Propagation Semantics:** The taint flag is bound to the data path. As long as tainted data resides in the register file or is in transit, the pipeline is considered tainted.
*   **Taint Sinks (`CSR_TCA_ADDR1`):** Network interfaces and persistent storage bounds are defined as sinks. A store operation attempting to flush tainted data to a sink triggers mediation.

## 2. Enforcement Pipeline

The enforcement pipeline acts as the inline physical mediator between the core's execution unit and the memory hierarchy.

1.  **Intent Check:** When a memory store (`MMU_DATA_STORE`) is dispatched to a recognized sink, the pipeline evaluates the current taint state against the authorized intent hash (`CSR_TCA_CFG`).
2.  **Mediation:** If the intent hash does not authorize the specific taint color for egress, the memory transaction is aborted synchronously before the bus transaction completes.
3.  **Trap Generation:** The mediation logic returns a custom `TRANSLATE_TCA_FAIL` signal to the TLB/MMU boundary, escalating to a hardware trap.

## 3. Exception Routing

TCA leverages custom RISC-V exception semantics to ensure robust fail-stop behavior.

*   **Exception ID:** `RISCV_EXCP_TCA_INTENT_VIOLATION` (0x1b).
*   **Routing Path:** The exception is routed directly to Machine Mode (`PRV_M`), forcing the highest privilege level to acknowledge the breach.
*   **State Preservation:** The `mepc` (faulting PC) and `mtval` (faulting address) registers are populated to provide the Machine Mode trap handler with exact forensic context.

## 4. Trust Boundaries and Privilege Behavior

TCA explicitly does not trust standard operating system boundaries (e.g., Supervisor Mode).

*   **M-Mode Auditing:** Crucially, even bare-metal operations executing in Machine Mode (`PRV_M`) with MMU paging disabled are subject to TCA mediation. The physical memory interface intercepts the raw physical address prior to bus assertion.
*   **Hypervisor Isolation:** TCA boundaries established by the hardware root-of-trust cannot be modified by the hypervisor or the guest kernel.

## 5. Simulator Limitations

The current QEMU-based (`riscv-tca-sim`) validation carries the following known limitations:
*   Taint is currently modeled as a boolean `tca_taint_flag` per `CPURISCVState`, simulating single-color dataflows.
*   The simulator evaluates MMU translations atomically; it does not model out-of-order store buffers where transient execution might bypass the intent check before the trap commits.

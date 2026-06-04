# System Invariants

This document establishes the mathematically strict behavioral constraints and formal contracts of the Trusted Computing Architecture (TCA). These invariants must hold true across all simulation, RTL synthesis, and physical execution states.

## Fail-Stop Semantics

**Invariant 1.1:** Any memory transaction attempting to egress unauthorized tainted data to a registered sink MUST be synchronously aborted before the transaction reaches the memory bus or peripheral interface.

**Invariant 1.2:** Upon detection of an intent violation, the processor pipeline MUST immediately transition to a trap state, halting all subsequent user-space instruction execution.

## Taint Propagation

**Invariant 2.1:** A load operation targeting an address bounds defined within `CSR_TCA_ADDR0` MUST synchronously assert the architectural taint flag within the active execution context.

**Invariant 2.2:** Taint state is monotonically infectious across data dependencies during the execution cycle. (Note: The current QEMU simulation applies taint at the thread-context level rather than per-register).

## Privilege Mediation

**Invariant 3.1:** All memory operations executed in Machine Mode (`PRV_M`), regardless of paging configuration or MMU status, MUST traverse the physical TCA mediation path before completion.

**Invariant 3.2:** No lower-privileged execution environment (Supervisor, User) MAY modify the TCA configuration CSRs (`CSR_TCA_CFG`, `CSR_TCA_ADDR0`, `CSR_TCA_ADDR1`).

## Trap Semantics

**Invariant 4.1:** A hardware-enforced intent violation MUST route strictly to the predefined exception vector `RISCV_EXCP_TCA_INTENT_VIOLATION` (0x1b).

**Invariant 4.2:** The exception vector MUST populate the `mepc` CSR with the exact virtual or physical address of the faulting instruction, guaranteeing forensic continuity.

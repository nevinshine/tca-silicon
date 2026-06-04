# TCA Hardware CSR Reference

This document formalizes the Custom Control and Status Registers (CSRs) integrated into the RISC-V hardware to support the Trusted Computing Architecture (TCA).

## 1. `CSR_TCA_CFG` (Intent Configuration)
*   **Address:** `0x802`
*   **Access Mode:** Read/Write (Machine Mode Only)
*   **Semantics:** Stores the active cryptographic intent hash authorizing specific data flows during the current execution context.
*   **Structural Effects:** The hardware mediation unit continuously polls this register to authorize egress transactions to defined sinks.
*   **Side Effects:** Writing to this CSR invalidates any previously cached intent evaluations in the mediation pipeline.
*   **Reset Behavior:** Initializes to `0x0000000000000000` (Deny-All) on hard processor reset.
*   **Persistence Semantics:** State persists across context switches unless explicitly cleared by the hypervisor or OS kernel.

## 2. `CSR_TCA_ADDR0` (Taint Source Bounds)
*   **Address:** `0x803`
*   **Access Mode:** Read/Write (Machine Mode Only)
*   **Semantics:** Defines the physical base address of the sensitive memory region. Currently simulated as a 4KB page boundary (`+ 0x1000`).
*   **Structural Effects:** Any `MMU_DATA_LOAD` resolving to an address within this bound triggers the taint hardware hook.
*   **Side Effects:** Writing to this register asserts a taint-tracking flush, clearing transient taint flags traversing the pipeline.
*   **Reset Behavior:** Initializes to `0x0000000000000000` (Unbound) on hard processor reset.
*   **Persistence Semantics:** Statically persistent until reconfigured by the Root of Trust.

## 3. `CSR_TCA_ADDR1` (Taint Sink Bounds)
*   **Address:** `0x804`
*   **Access Mode:** Read/Write (Machine Mode Only)
*   **Semantics:** Defines the physical base address of the restricted egress interface (e.g., Network TX MMIO trigger). Currently simulated as a 4KB page boundary (`+ 0x1000`).
*   **Structural Effects:** Any `MMU_DATA_STORE` resolving to an address within this bound triggers the synchronous intent evaluation check if the pipeline holds a tainted state.
*   **Side Effects:** None.
*   **Reset Behavior:** Initializes to `0x0000000000000000` (Unbound) on hard processor reset.
*   **Persistence Semantics:** Statically persistent until reconfigured by the Root of Trust.

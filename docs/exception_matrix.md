# TCA Exception Matrix

This reference maps the custom architectural exceptions introduced by the Trusted Computing Architecture (TCA), detailing their triggering conditions and datapath routing.

| Exception ID | Semantic Meaning | Trigger Condition | Trap Path |
| :--- | :--- | :--- | :--- |
| `0x1b` (27) | `RISCV_EXCP_TCA_INTENT_VIOLATION` | A memory store operation (`MMU_DATA_STORE`) targeting a predefined taint sink (`CSR_TCA_ADDR1`) occurred while the pipeline was tainted (`tca_taint_flag == 1`), and the active intent hash (`CSR_TCA_CFG`) failed cryptographic authorization. | The memory transaction is synchronously aborted. The CPU transitions unconditionally to Machine Mode (`PRV_M`), jumping to `mtvec`. The faulting address is populated in `mtval`, and the instruction PC is saved in `mepc`. |
| `0x1c` (28) | *Reserved (TCA_CODE_REUSE)* | *Future implementation: Execution of an unauthorized branch target leading to a violation of the Control Flow Integrity (CFI) intent graph.* | Trap to Machine Mode. |
| `0x1d` (29) | *Reserved (TCA_LIFECYCLE_FAULT)* | *Future implementation: Attempted modification of locked TCA CSRs by an unauthorized execution level.* | Trap to Machine Mode. |

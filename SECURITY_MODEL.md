# Security Model and Adversary Assumptions

This document outlines the threat vectors the Trusted Computing Architecture (TCA) is explicitly designed to mitigate, and conversely, the adversary capabilities that fall outside its protective scope.

## 1. Protected Assets

TCA is designed to protect the **intent-based flow of information** across the processor architecture. Specifically:
*   Confidential data must not egress to unauthorized interfaces (Data Exfiltration).
*   Untrusted input must not manipulate high-privilege control flows without validation (Code Reuse/Confused Deputy).

## 2. Adversary Model

We assume an adversary with the following capabilities:

### In-Scope Threats (Mitigated)
*   **Arbitrary Code Execution (ACE):** The adversary has achieved full code execution capabilities within the target application.
*   **Privilege Escalation:** The adversary has successfully bypassed operating system privilege rings and is executing code in Machine Mode (`PRV_M`).
*   **Memory Management Control:** The adversary can arbitrarily modify page tables, disable the MMU, or spoof virtual addresses.

*TCA defends against these by rooting enforcement in the physical memory Datapath, completely decoupled from OS-level abstractions.*

### Out-of-Scope Threats (Unmitigated)
*   **Physical Hardware Tampering:** We assume the adversary cannot decap the silicon, probe the buses with logic analyzers, or modify the synthesized RTL netlist.
*   **Transient Execution Attacks:** (See `LIMITATIONS.md`). We assume the adversary cannot leverage speculative execution (e.g., Spectre) to infer tainted data states before the pipeline squashes the branch.
*   **Side-Channel Analysis:** We assume the adversary does not have the physical proximity or sensor precision to conduct Differential Power Analysis (DPA) on the intent verification unit.
*   **Denial of Service:** The adversary can intentionally trigger intent violations to crash the system. TCA guarantees fail-stop semantics, not continuous availability.

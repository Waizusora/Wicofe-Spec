# Dynamic Kernel Modules — Cross-Component Contract

- **Identifier:** WIC-SPEC-DKM-XCOMP-001
- **Version:** 0.1.0
- **Status:** Draft
- **System contract owner:** Wicofe-Kernel
- **Coordination:** Wicofe-Specs
- **Consumers:** Wicofe-Drivers and kernel build/release tooling
- **Targets:** x86-64, AArch64

## 1. Authority

The normative DKM design is owned by the kernel repository: [DKM_SPEC.md](https://github.com/Waizusora/Wicofe-Kernel/blob/main/docs/DKM_SPEC.md). The kernel's [ABI boundary document](https://github.com/Waizusora/Wicofe-Kernel/blob/main/docs/ABI_BOUNDARIES.md) distinguishes privileged DKM ABI from the userspace syscall ABI.

This cross-component document records integration requirements only. It does not redefine the kernel's module ABI or select an object format.

## 2. Architecture invariants

- Wicofe remains a configurable monolithic kernel.
- A DKM executes with kernel privilege; ABI checks are not a sandbox.
- Components may be built-in, loadable, or disabled according to dependency and boot constraints.
- Only explicitly versioned kernel exports may be consumed by modules.
- Rust's unstable ABI, trait-object layout, and compiler-private types are forbidden at the module boundary.
- A module must not be published as active until validation and initialization complete.
- Unload is forbidden until quiescence and absence of reachable module code are proven.

## 3. Cross-repository responsibilities

| Concern | Authority |
|---|---|
| Module metadata, loader, relocation, lifecycle, privileged ABI | Wicofe-Kernel |
| Driver implementation and driver-specific tests | Wicofe-Drivers |
| Build configuration and artifact packaging | Wicofe-Build / Wicofe-Tools |
| Cross-component conformance scenarios | Wicofe-Tests |
| Specification index and cross-component change tracking | Wicofe-Specs |
| Third-party module provenance and license records | Wicofe-ThirdParty |

## 4. Required integration contract

Before a module artifact is considered consumable, its producer and kernel target must agree on:

1. Target architecture and required CPU features.
2. DKM ABI version and required interface capabilities.
3. Module identity, version, and dependency constraints.
4. Object/container format and permitted relocation subset.
5. Export namespace and symbol-version policy.
6. Memory permissions and instruction-cache synchronization.
7. Trust policy, signature verification, and key lifecycle.
8. Initialization, failure rollback, and unload policy.

Items 4–7 remain design decisions until accepted by Wicofe-Kernel. ELF is only a candidate in the current kernel draft.

## 5. Conformance gates

Wicofe-Tests should eventually cover malformed metadata, wrong architecture, ABI mismatch, missing exports, relocation overflow, dependency cycles, initialization rollback, signature failure, and unsafe-unload refusal.

A successful module link or compile is not evidence that loading, execution, or unloading is safe.

## 6. Open decisions

The canonical kernel specification must resolve object format, relocation types per architecture, ABI namespace/version rules, signing and revocation, module memory layout, module-to-module dependencies, boot transport, and initial unload support.

## 7. Change control

Any change to the privileged DKM ABI is reviewed and accepted in Wicofe-Kernel. Consumers must update their compatibility declarations and tests. This document is updated only to reflect cross-repository impact.

**No stable DKM ABI is declared by this draft.**

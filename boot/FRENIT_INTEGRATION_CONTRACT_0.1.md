# Frenit Integration Contract 0.1

- **Identifier:** WIC-SPEC-BOOT-FRENIT-001
- **Status:** Proposed
- **Version:** 0.1
- **Updated:** 2026-11-18
- **Canonical implementation:** [Waizusora/Frenit](https://github.com/Waizusora/Frenit)
- **Owners:** Frenit (init implementation), Wicofe-Kernel (kernel mechanisms), Wicofe-ABI (public userspace ABI), Wicofe-Specs (cross-component contract), Wicofe-Tests (conformance)

This document establishes only the repository ownership and currently evidenced integration boundary. It does not inherit any prior init-system proposal and does not define syscall numbers, data layouts, IPC wire formats, or unsupported kernel behavior.

## 1. Canonical identity

- Project: **Frenit — Free Init**.
- Repository: `Waizusora/Frenit`.
- Executable and Cargo package: `frenit`.
- CLI convention: `frenit <domain> <action> [argument]`.
- CLI terms include the singular names `diagnostic`, `dependent`, and `command`.

The Frenit repository README is authoritative for current project identity and CLI behavior.

## 2. Current implementation status

Frenit currently describes itself as an early-development foundation (0.1.0), and explicitly states that it is not yet safe to install as PID 1. The presence of CLI parsing or service-management models is not evidence of a working native init runtime.

No native Wicofe PID 1 capability is considered implemented until the required kernel/ABI contracts, executable handoff, and conformance tests exist.

## 3. Repository ownership

| Area | Owner |
|---|---|
| Init executable, CLI, service-management implementation | Frenit |
| Process creation, execution, exit, wait/reap, scheduling, signals, power mechanisms | Wicofe-Kernel |
| Public syscall numbers, structures, calling convention, and error model | Wicofe-ABI |
| Cross-component requirements and compatibility rules | Wicofe-Specs |
| Native boot, process, service, and shutdown conformance | Wicofe-Tests |
| Whole-system build orchestration | Wicofe-Build |
| Root filesystem and boot image composition | Wicofe-Image |
| Installation and first-boot deployment | Wicofe-Installer |

## 4. Integration gates

Before Frenit can be selected as the production PID 1, the project must define and verify, without inventing kernel interfaces:

1. Kernel-to-init executable selection and initial process handoff.
2. Process spawn/exec, exit status, child wait/reap, and PID identity semantics.
3. Signal delivery and safe handling of child-exit notifications.
4. Required filesystem, descriptor, clock, and IPC capabilities.
5. Controlled shutdown and power-transition interfaces.
6. Root-filesystem placement, permissions, configuration, and image manifest integration.
7. Native QEMU/hardware boot tests proving PID 1 startup and bounded failure behavior.

Each gate must identify its owning ABI/kernel specification and a reproducible test. Host-only tests and Linux cross-builds do not prove native Wicofe integration.

## 5. Compatibility and exclusions

- Do not reuse proposal-specific executable names, socket paths, unit schemas, command interfaces, or implementation code.
- Do not assume Linux syscall numbers or Linux binary ABI compatibility.
- Do not enable a Frenit PID 1 boot path while any mandatory kernel/ABI integration gate remains undefined or unverified.
- Keep unsupported operations explicit; never report an unimplemented privileged operation as successful.

## 6. Status tracking

Implementation and verification are tracked separately. Current status:

| Capability | Implementation | Verification |
|---|---|---|
| Frenit repository identity and CLI naming | Implemented in Frenit repository | CI evidence tracked by Frenit |
| Native Wicofe PID 1 handoff | Not started | Not run |
| Native process/signal/wait integration | Not started | Not run |
| Native service IPC and shutdown | Not started | Not run |
| Bootable image integration | Not started | Not run |

# Osynit Core Architecture Contract 0.1

- **Identifier:** WIC-SPEC-OSYNIT-CORE-001
- **Status:** Accepted architecture baseline; implementation and verification remain separate
- **Version:** 0.1
- **Date:** 2026-11-16
- **Canonical implementation/design:** [Waizusora/Osynit](https://github.com/Waizusora/Osynit)
- **Related boot/service contract:** [Osynit Boot and Service Integration Contract 0.1](OSYNIT_BOOT_AND_SERVICE_CONTRACT_0.1.md)

This document records the accepted architecture boundary for Osynit across Wicofe repositories. It does not freeze syscall numbers, wire-level IPC encoding, filesystem layout profiles, or unimplemented behavior.

## 1. Identity

The project name is **Osynit**, expanded as **Open System Initialize**. The single executable and command prefix are `osynit`. No `osynitco` or `osynitctl` executable is defined.

## 2. Mandatory Core Init responsibilities

All twelve responsibilities are mandatory parts of Core Init:

1. Kernel Interface
2. Process Supervisor
3. Service Manager
4. Dependency Manager
5. Boot Manager
6. System State Manager
7. Signal Manager
8. Shutdown Manager
9. Configuration Loader
10. Runtime Manager
11. Failure Recovery Manager
12. IPC / Control Interface

These are logical responsibilities, not a requirement for twelve separate processes or crates. The core must remain small, auditable, testable, and able to reach a safe state without optional extensions.

## 3. Optional Osynit modules

The following are optional and must not be prerequisites for Core Init:
- Mount and automount manager
- Socket activation
- Timer activation
- Logging backend/persistent journal
- Resource and cgroup integration
- Security sandbox/policy integration
- Watchdog integration
- Advanced job inspection/control

Core may parse or identify extension metadata but must report unsupported/unavailable when the matching module is absent. It must not silently claim success.

## 4. External service boundary

Network, device, storage, user/login/session, time synchronization, audio, graphics, desktop, and container domain behavior belongs in external services or narrower subsystem repositories. Osynit supervises their lifecycle and dependency relationships; it does not implement their domain logic inside PID 1.

## 5. Executable and IPC model

There is one executable:
- As the configured PID 1 program, `osynit` enters init mode.
- With explicit subcommands outside PID 1, it enters client mode and uses the local manager IPC endpoint.
- Client mode must never start a second init manager.
- Initial socket path: `/run/osynit/osynit.sock`.
- The transport must be versioned, framed, bounded, permission-checked, and authorized per operation. Unix `SOCK_SEQPACKET` is preferred where supported; a documented stream framing fallback is allowed.

## 6. Core command namespace

The command prefix is always `osynit`. The complete planned command surface is maintained in the [Osynit CLI Reference 0.1](https://github.com/Waizusora/Osynit/blob/main/docs/CLI_REFERENCE_0.1.md). Planned commands are not implementation evidence. Optional command groups are registered only when their modules are available.

## 7. Ownership across repositories

| Responsibility | Canonical owner |
|---|---|
| PID 1, core, unit format, CLI and internal service lifecycle | Osynit |
| Kernel process, signal, wait, socket and power mechanisms | Wicofe-Kernel |
| Public userspace ABI definitions | Wicofe-ABI |
| Cross-component requirements and this contract | Wicofe-Specs |
| External daemon implementations | Wicofe-Services and narrower subsystem repositories |
| Whole-system build orchestration | Wicofe-Build |
| Root filesystem and image composition | Wicofe-Image |
| Installation and first-boot deployment | Wicofe-Installer |
| Whole-system architecture and project decisions | Wicofe |

No repository may independently assign Wicofe syscall numbers, invent kernel-private process semantics, or fork the canonical Osynit service-unit schema.

## 8. Platform and compatibility policy

Wicofe's initial CPU targets are x86-64 and AArch64. Architecture support is not proof that a kernel primitive or optional feature exists. Osynit must use the Wicofe ABI and platform capability contract rather than assuming Linux-specific pidfds, cgroups, signal flags, or reboot syscalls.

Host tests, Android Bionic builds, and Linux musl builds do not establish native Wicofe PID 1 conformance.

## 9. Implementation and verification status

This specification is accepted as an architecture baseline. Implementation and verification must be tracked separately per requirement and target. A design document, successful host test, or successful cross-compile does not prove a bootable Wicofe system.

Conformance requires process/wait/reap tests, signal and PID-reuse tests, dependency and state-machine tests, bounded failure recovery, IPC authorization/framing tests, image handoff checks, and architecture-specific boot tests.

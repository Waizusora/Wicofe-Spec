# Osynit Boot and Service Integration Contract 0.1

- **Identifier:** WIC-SPEC-BOOT-OSYNIT-001
- **Status:** Proposed
- **Version:** 0.1
- **Updated:** 2026-11-16
- **Canonical Osynit design:** [Osynit System Specification 0.1](https://github.com/Waizusora/Osynit/blob/main/docs/OSYNIT_SYSTEM_SPEC_0.1.md)
- **Owners:** Wicofe-Specs (cross-component contract), Osynit (init implementation), Wicofe-Kernel (kernel implementation), Wicofe-ABI (userspace ABI)

This contract defines the boundary between the Wicofe kernel, Osynit PID 1, service daemons, build/image assembly, and installation. It does not define syscall numbers or kernel-private structures.

## 1. Terminology

**Osynit** means **Open System Initialize**. `osynit` is the only official executable name. It runs as PID 1 in init mode and as a control client when invoked with subcommands outside PID 1. A client invocation must never spawn a second init manager.

## 2. Responsibility boundary

- Kernel: process creation/exit primitives, scheduling, signal delivery, wait/reap semantics, syscall dispatch, VFS/socket primitives, and power-transition primitives.
- Osynit: PID 1 lifecycle, child reaping, service supervision, dependency ordering, boot targets, system state, failure recovery, IPC policy, and shutdown orchestration.
- External daemons: domain behavior such as network configuration, device policy, storage administration, logging backends, user/session, audio, and desktop services.
- Wicofe-ABI: stable userspace syscall numbers, structures, calling convention, and error definitions.
- Wicofe-Specs: versioned contracts shared across repositories.

Osynit is responsible for supervising system services; it is not responsible for implementing every service domain.

## 3. PID 1 requirements

The kernel must start the configured init image as PID 1 with a documented initial process environment and working directory contract. Osynit must:

1. detect init mode without relying on an ambiguous command-line convention;
2. initialize only the minimum runtime facilities required for safe process management;
3. receive child-exit notifications and reap every waitable child;
4. handle orphan adoption according to the kernel's process model;
5. preserve process identity safely across exit and PID reuse;
6. avoid unbounded blocking in signal and child-reaping paths;
7. enter a defined rescue/emergency path if boot-critical initialization fails.

The exact adoption semantics, wait interface, and signal behavior must be specified by Wicofe-Kernel and exposed through Wicofe-ABI.

## 4. Kernel/userspace capability contract

Before native PID 1 integration is declared supported, the following capabilities need versioned ABI definitions and kernel implementation evidence:

- process spawn/exec and exit status retrieval;
- wait/reap semantics for direct children and adopted descendants;
- signal mask, delivery, and safe signal notification;
- process identity and race-resistant signaling (pidfd or a documented alternative);
- monotonic clock and sleep/deadline facilities;
- Unix-domain sockets and peer credential retrieval, if supported by the platform;
- filesystem operations and permission semantics required for runtime/config paths;
- sync and controlled reboot/poweroff/halt operations;
- resource-limit/cgroup interfaces only when the optional resource module is enabled.

No syscall number, struct layout, or error value may be invented in Osynit. The implementation must use the canonical Wicofe-ABI definitions and report unavailable capabilities explicitly.

## 5. Boot handoff and target execution

The bootloader-to-kernel handoff remains owned by Wicofe-Kernel. The kernel then launches Osynit as PID 1. The kernel must not embed service ordering or domain service policy.

Osynit executes the configured initial target using a validated dependency graph. Required dependencies must be started before dependants; ordering-only relationships must not implicitly pull services into a transaction. Cycles and missing required units are fatal configuration errors for the affected transaction.

System state transitions are owned by Osynit and must be validated. Failure of optional services may result in a degraded state; failure of critical initialization may transition to rescue/emergency handling.

## 6. Service contract

Initial service types are foreground `simple` and `oneshot`. Services must not daemonize in `simple` mode. Service commands are argv-based and must not be interpreted by an implicit shell.

Each service definition declares at minimum its identity, executable/arguments, type, dependency edges, environment policy, restart policy, stop signal, grace timeout, and enablement/target association. The canonical service-file schema is owned by Osynit.

Network, device, storage, logging, user/session, time, audio, and desktop implementations are external services. They register service definitions and documented IPC; they are not built into the Osynit core.

## 7. Osynit control interface

The initial control endpoint is `/run/osynit/osynit.sock`. The protocol must be versioned, locally scoped, bounded, permission-checked, and authenticated using peer credentials where available. Read-only status operations and mutating lifecycle operations require distinct authorization.

The executable remains `osynit`; administrative subcommands are client mode. There is no separate required `osynitctl` executable.

The wire format and exact transport availability remain subject to ABI/platform capability review.

## 8. Shutdown contract

Osynit rejects new lifecycle transactions, computes reverse dependency order, requests graceful stop, enforces bounded timeouts, and escalates according to policy. The kernel exposes a controlled power-transition operation through Wicofe-ABI. Mount teardown is a separately validated operation; Osynit must not unmount arbitrary filesystems blindly.

Kernel and Osynit must define behavior for services that ignore termination, blocked I/O, and power-transition failure.

## 9. Optional capabilities

Mount orchestration, socket activation, timers, persistent logging, resource/cgroup management, sandboxing, watchdog, and advanced job control are optional Osynit modules. Their absence must not prevent the mandatory Core Init from booting.

A module may only be enabled when its required kernel/ABI capabilities are present. Unsupported operations return explicit errors.

## 10. Build, image, and installation integration

- Wicofe-Build treats Osynit as a target userspace component and records immutable source revision, target profile, toolchain, output hash, and validation evidence.
- Wicofe-Image includes the Osynit executable and the selected initial target/service definitions in the root filesystem manifest.
- Wicofe-Installer installs/configures the image's init selection and initial boot target only through a versioned image/deployment contract.
- First-boot tasks are split explicitly: Osynit performs service orchestration; dedicated first-boot services perform machine identity, user, locale, and device-specific setup.

## 11. Conformance and release gates

A native Wicofe integration is not complete until all claimed architectures pass:

- PID 1 launch and early initialization;
- child exit notification, orphan adoption, and zombie reaping;
- signal delivery and process identity race tests;
- deterministic dependency and target execution;
- service crash/restart rate limiting;
- IPC authorization, protocol-version, malformed-input, and size-limit tests;
- bounded shutdown and controlled reboot/poweroff tests;
- bootable image tests with the exact Osynit revision and service manifest.

Host tests and Linux compatibility tests are useful but do not substitute for native Wicofe kernel/ABI conformance.

## 12. Traceability

| Requirement area | Implementation authority | Verification authority |
|---|---|---|
| PID 1 and service lifecycle | Osynit | Wicofe-Tests |
| Process, signal, wait, power primitives | Wicofe-Kernel | Wicofe-Tests |
| Public syscall definitions | Wicofe-ABI | Wicofe-Tests |
| Cross-component contract | Wicofe-Specs | Wicofe-Tests |
| Service daemon behavior | Wicofe-Services / component repository | Wicofe-Tests |
| Build and target artifact | Wicofe-Build | Wicofe-Tests |
| Root filesystem and boot image | Wicofe-Image | Wicofe-Tests |
| Installation and first boot | Wicofe-Installer | Wicofe-Tests |

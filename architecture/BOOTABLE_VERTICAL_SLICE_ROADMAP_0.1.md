# Wicofe Bootable Vertical Slice Roadmap 0.1

- **Specification ID:** WIC-SYS-BOOT-ROADMAP-001
- **Version:** 0.1
- **Status:** Draft
- **Updated:** 2026-11-16
- **Coordinator:** Wicofe-Specs
- **Primary implementation owner:** Wicofe-Kernel
- **Contributors:** Wicofe-ABI, Osynit, Wicofe-Libc, Wicofe-Build, Wicofe-Image, Wicofe-Tests
- **Targets:** x86-64 and AArch64
- **Purpose:** Define an evidence-driven order for reaching a bootable Wicofe CLI system.

This roadmap coordinates implementation order and acceptance evidence. It does not replace component-owned specifications, freeze syscall numbers, or claim that planned work is implemented.

## 1. Product milestone

The first system milestone is a **Bootable CLI Vertical Slice**:

`Firmware/Bootloader → Wicofe Kernel → PID 1 (Osynit) → CLI shell → userspace program`

The system must reach a stable CLI, execute a userspace program, report process exit status, and remain diagnosable when a required boot stage fails. Persistent storage and installation are the next milestone, not prerequisites for the first RAM-backed/emulator vertical slice.

A boot log ending at kernel entry or PMM initialization is not a bootable OS milestone.

## 2. Verified starting point (2026-11-16)

The latest inspected Wicofe-Kernel main commit is `2f529b0d6513d03ec99ddc6a5a3555692e989923`.

Evidence in the repository and latest Kernel Actions run `43882914312`:

- Rust formatting and workspace unit tests passed.
- x86-64 `unknown-none` target check passed.
- x86-64 freestanding kernel ELF build and ELF layout verification passed.
- BIOS Limine ISO packaging passed.
- QEMU boot reached `WICOFE: PMM_READY` and `WICOFE: PMM_ALLOC_FREE_OK`.
- AArch64 `unknown-none` compile check passed.

Important limits:

- The QEMU test validates boot-information consumption and a single-frame PMM allocate/free smoke test; it does not validate VMM runtime behavior, exceptions, interrupts, scheduling, userspace, or hardware compatibility.
- AArch64 final ELF and runtime boot are not yet validated.
- PMM is single-core and not yet enabled for general kernel allocations.
- Osynit is a host-testable Rust core prototype, not yet a complete native PID 1 executable, IPC server, or bootable Wicofe integration.
- No end-to-end bootable Wicofe CLI system is claimed.

This baseline must be refreshed from current source and CI before each major phase is started.

## 3. Work sequencing and acceptance gates

### Phase 0 — Baseline and contract inventory

**Status:** Complete for the initial 2026-11-16 planning snapshot; refresh required at phase boundaries.

- Record exact repository commits, workflows, and artifacts.
- Separate implemented, compiled, emulator-tested, hardware-tested, proposed, and deferred capabilities.
- Identify canonical owners for every shared interface.
- Link every requirement to implementation and test evidence.

**Exit gate:** No roadmap item is marked implemented or passed without a source location and execution evidence.

### Phase 1 — Kernel memory and CPU fault foundation

**Priority:** First implementation phase.

**Owner:** Wicofe-Kernel. **Coordination:** Wicofe-ABI and Wicofe-Tests.

1. Review and test x86-64 VMM page-table operations against the memory specification.
2. Define and implement mapping, unmapping, protection, translation, and TLB invalidation invariants.
3. Add architecture exception entry and a minimal diagnostic/fail-stop path; page faults must be distinguishable from generic faults.
4. Establish the required CPU descriptor and exception state before enabling interrupts.
5. Add deterministic unit tests and QEMU runtime tests for valid mappings, permission violations, unmapped access, and recovery/fail-stop behavior.
6. Keep PMM single-core until synchronization and execution-context rules are implemented; do not expose unsafe general allocation prematurely.

**Exit gate:** VMM operations pass unit tests; QEMU demonstrates expected mapping and protection behavior; deliberate fault tests produce the expected diagnostic and controlled stop; no allocator ownership overlap is observed.

**AArch64 track:** Specify and implement the equivalent translation-table and exception-level foundation. Compilation alone does not pass the runtime gate.

### Phase 2 — Interrupts, timer, and synchronization

**Owner:** Wicofe-Kernel.

1. Implement architecture-specific exception/interrupt vectors and controller setup.
2. Add a monotonic kernel clock and timer interrupt path.
3. Define interrupt-context rules, lock ordering, and non-blocking constraints.
4. Add synchronization required for shared kernel state.
5. Add interrupt/timer QEMU tests and stress/failure-path tests.

**Exit gate:** Timer interrupts are observed reliably; interrupt acknowledgement and return paths are validated; no blocking allocator or unsafe lock use occurs in hard-interrupt context.

### Phase 3 — Heap, threads, and scheduler

**Owner:** Wicofe-Kernel.

1. Add a kernel heap only after VMM mapping and PMM ownership/synchronization contracts are safe.
2. Implement thread context, stack lifecycle, state transitions, and idle execution.
3. Implement a deterministic single-CPU scheduler before enabling SMP scheduling.
4. Validate preemption/yield, blocked/wakeup transitions, stack guards where supported, and thread termination.
5. Enable SMP only after per-CPU state, allocator synchronization, interrupt routing, and TLB coordination are tested.

**Exit gate:** Multiple kernel threads execute and switch in QEMU; state transitions and resource cleanup pass tests. SMP remains explicitly disabled until its separate gate passes.

### Phase 4 — Process, address spaces, and userspace ABI

**Owners:** Wicofe-Kernel (mechanisms), Wicofe-ABI (contract), Wicofe-Libc (userspace interface).

1. Define the minimum versioned syscall contract without assigning duplicate/private numbers.
2. Implement user address spaces, privilege transition, syscall entry/return, and argument validation.
3. Implement the minimum process lifecycle: create/execute, exit, wait/reap, parent/child and orphan semantics.
4. Define file descriptor and signal semantics needed by the first CLI.
5. Add ABI layout, invalid-pointer, privilege, process lifecycle, and syscall regression tests.

**Exit gate:** A ring-3 userspace test program can start, make documented syscalls, exit, and have its status reaped by the kernel. Host tests alone are insufficient.

### Phase 5 — PID 1 and CLI handoff

**Owners:** Osynit (PID 1 policy), Wicofe-Kernel (mechanisms), Wicofe-ABI (contract), Wicofe-Libc (runtime), Mysh (shell).

1. Implement the configured initial-process handoff with PID 1, argv, environment, credentials, working directory, and standard descriptors.
2. Implement and test child-exit notification, reaping, orphan adoption, monotonic timeouts, and safe process identity.
3. Build Osynit as the native target executable and make its PID 1 path enter a safe, diagnosable state.
4. Start a minimal CLI shell as a supervised userspace process.
5. Execute a known userspace program and verify exit status and failure reporting.

**Exit gate:** Automated QEMU test observes kernel handoff to native Osynit PID 1, shell startup, successful execution of a test program, and clean child reaping. This is the first Bootable CLI Vertical Slice milestone.

### Phase 6 — VFS, block device, and persistent root filesystem

**Owners:** Wicofe-Kernel (VFS/block mechanisms), Wicofe-Storage (device/storage policy), ONWFS (disk format), Wicofe-Tests.

1. Implement the minimal VFS and block I/O contracts required by boot.
2. Bring up a deterministic emulator block device.
3. Mount a root filesystem with explicit read/write and durability semantics.
4. Verify file creation, readback, metadata, unmount/sync behavior, and power-loss limitations.
5. Keep unsupported filesystem operations explicit; do not promise semantics not provided by the filesystem.

**Exit gate:** QEMU boots from a persistent disk image, mounts root, and executes the CLI and test program from that root filesystem.

### Phase 7 — Image, recovery, and installer integration

**Owners:** Wicofe-Build, Wicofe-Image, Wicofe-Installer, Wicofe-Recovery.

1. Build reproducible architecture-specific kernel and userspace artifacts.
2. Compose bootable images with manifests, hashes, provenance, SBOM, and license notices.
3. Test image verification and boot on each claimed architecture.
4. Implement safe installer preflight, explicit destructive consent, target revalidation, deployment, and first-boot confirmation.
5. Validate recovery paths and interruption behavior.

**Exit gate:** A clean emulator or test device can be installed from a verified image and complete first boot from persistent storage. Never equate image packaging with successful installation.

### Phase 8 — Package and system update

**Owners:** WPM (package interface), Wicofe-Update (whole-system orchestration), Wicofe-Tests.

1. Finalize package format, repository metadata, signature/trust, dependency, and transaction contracts.
2. Define `wpm sync` as metadata refresh/check and `wpm upgrade` as applying eligible updates.
3. Establish a versioned WPM-to-Wicofe-Update boundary and shared transaction lock.
4. Test interrupted updates, compatibility checks, recovery, and security out-of-band delivery.

**Exit gate:** Signed updates can be applied and verified without corrupting the system; recovery behavior is demonstrated. Delivery Release cadence is governance, not a substitute for technical update safety.

## 4. Architecture sequencing rules

- x86-64 is the first runtime integration path because it currently has BIOS/Limine QEMU boot evidence.
- AArch64 remains a first-class target. Compile success is not runtime support; add an explicit emulator/hardware runtime gate before claiming parity.
- Kernel mechanisms belong in Wicofe-Kernel; public syscall definitions belong in Wicofe-ABI.
- Osynit owns PID 1 policy and service lifecycle; it must not invent kernel syscalls or embed domain services.
- Wicofe-Specs coordinates shared contracts but does not replace component-owned specifications.
- Wicofe-Tests owns cross-repository conformance evidence; component unit tests remain with their implementations.
- Do not start installer, package update, or desktop integration as a substitute for closing the boot-to-userspace path.
- Avoid broad subsystem implementation before the minimum vertical slice works. Prefer the smallest testable capability that advances the next exit gate.

## 5. Evidence and status vocabulary

Track each item with three independent fields:

- **Specification:** Draft / Proposed / Accepted / Deprecated / Superseded.
- **Implementation:** Not started / In progress / Implemented / Removed.
- **Verification:** Not run / Blocked / Failed / Passed.

Verification evidence must name the architecture, environment, source commit, workflow/run or test command, and relevant log/artifact. Use Passed only for the exact tested scope.

## 6. Immediate next actions

1. Refresh Kernel main and latest Actions run before changing code.
2. Inspect VMM and x86-64 paging implementation, including TLB invalidation and permission handling.
3. Identify the smallest missing VMM/exception test that can be added without pretending a complete process or userspace model exists.
4. Implement one bounded Phase 1 change in Wicofe-Kernel.
5. Run the kernel workflow and inspect failing job logs if any.
6. Update this roadmap and component documentation only with verified outcomes.

## 7. Non-claims

This roadmap does not claim that VMM, exception/interrupt handling, scheduler, process execution, syscalls, native Osynit PID 1, CLI boot, persistent root filesystem, installer, or update pipeline are complete. Each requires its own implementation and verification gate.

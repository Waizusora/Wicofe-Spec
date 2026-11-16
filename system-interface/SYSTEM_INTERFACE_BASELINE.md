# Wicofe System Interface Baseline

- **Identifier:** WIC-SPEC-SYSIF-001
- **Version:** 0.1.0
- **Status:** Draft
- **Userspace ABI owner:** Wicofe-ABI
- **Kernel implementation owner:** Wicofe-Kernel
- **Runtime owners:** Wicofe-Libc and Wicofe-Lib
- **Coordination:** Wicofe-Specs

## 1. Scope and authority

This document describes the boundary between user processes and the kernel and the adjacent runtime responsibilities. It is an index and coordination baseline, not a syscall-number registry.

The canonical syscall contract is [Wicofe-ABI/SPECIFICATION_BASELINE.md](https://github.com/Waizusora/Wicofe-ABI/blob/main/SPECIFICATION_BASELINE.md). Kernel-side separation between userspace ABI and privileged DKM ABI is described in [Wicofe-Kernel/docs/ABI_BOUNDARIES.md](https://github.com/Waizusora/Wicofe-Kernel/blob/main/docs/ABI_BOUNDARIES.md).

The DKM ABI is separate and must never be conflated with the userspace system interface.

## 2. Layer model

`application` → `Rust std / libc / Wicofe-Lib` → `userspace syscall stubs` → `architecture syscall entry` → `kernel validation and dispatch` → `kernel subsystem`

Applications should use documented library interfaces where available. Architecture-specific register conventions belong in low-level syscall stubs and the ABI specification, not portable application code.

## 3. Boundary rules

- Use fixed-width integer types and explicitly specified C-compatible layouts for public structures.
- Never expose Rust compiler-private layout, references, slices, `String`, `Vec`, trait objects, or Rust enum representation as a stable syscall ABI.
- Treat all userspace pointers as untrusted; define range, alignment, access, lifetime, and fault semantics.
- Define argument width, signedness, reserved bits, and unknown-flag behavior for every call.
- Define raw success/error encoding so errors cannot be confused with valid return values.
- Document blocking, interruption, restart, cancellation, partial transfer, and side effects.
- Define permission and object-right checks at the kernel boundary.
- Version public structures and specify extension rules; do not rely on implicit padding.
- Keep userspace syscall ABI and privileged DKM ABI versioning independent.

## 4. Initial interface domains

The initial design order is:

1. Process and thread lifecycle.
2. Virtual memory and address-space operations.
3. File descriptors and filesystem operations.
4. Clocks, timers, and sleep.
5. Synchronization and IPC.
6. Signals and notifications.
7. Sockets and networking.
8. Identity, permissions, and resource limits.
9. System information and capability discovery.

This list is a planning scope only; it does not commit every domain to the first ABI release.

## 5. libc and Rust runtime relationship

Wicofe-Libc implements the C-facing API and translates kernel error results into the C `errno` contract. Wicofe-Lib and the Rust userspace runtime may provide idiomatic safe wrappers over documented system interfaces.

Rust `std` is preferred for userspace where supported. FFI is reserved for explicit OS boundaries, C interoperability, or libraries without a suitable Rust interface. This preference does not authorize bypassing the canonical syscall ABI.

## 6. Compatibility policy

Each public call and structure must state introduction version, stability, architecture availability, error semantics, and deprecation policy. Once marked stable, incompatible changes require a compatibility plan and coordinated updates to kernel, libc, language bindings, SDK, and tests.

The raw syscall register assignments, exact syscall numbers, and errno table remain decisions for Wicofe-ABI and are not selected here.

## 7. Required tests

Before a public ABI is declared stable, test structure size/alignment/offsets on x86-64 and AArch64, raw syscall entry/return semantics, invalid pointers, overflowed lengths, unknown flags, permission failures, interruption, partial I/O, and compatibility between kernel and userspace versions.

**No syscall numbers, register assignments, or stable ABI guarantees are frozen by this draft.**

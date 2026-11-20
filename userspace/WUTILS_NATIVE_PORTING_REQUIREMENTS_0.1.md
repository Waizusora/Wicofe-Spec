# Native Wicofe Rust Utilities Porting Requirements 0.1

- **Identifier:** WIC-SPEC-USERSPACE-WUTILS-001
- **Status:** Proposed
- **Version:** 0.1
- **Owner:** Wicofe-Spec
- **Implementation status:** Not started
- **Verification status:** Not run

## 1. Purpose

Define the native platform requirements for a Wicofe-maintained fork of the Rust-based uutils utilities project. The working project name is **Wutils**.

Wutils is intended to provide a native command-line utility suite for Wicofe. It is not a Linux port, GNU compatibility package, or Linux userspace emulation environment.

## 2. Project requirements

- Base the fork on the upstream Rust uutils project where its source architecture and license permit.
- Maintain Wicofe as a native target and treat POSIX utility behavior as the compatibility objective.
- Do not make GNU Coreutils behavioral compatibility a project requirement.
- Do not require Linux libc, glibc, musl-for-Linux, Bionic, or any Linux compatibility layer.
- Do not use Linux syscall numbers, Linux process ABI, Linux ELF assumptions, or GNU-specific runtime interfaces as the Wicofe platform contract.
- Preserve upstream notices and comply with the exact upstream license and all dependency licenses.
- Prefer permissively licensed dependencies. GPL and AGPL dependencies are not acceptable for normal integration. LGPL may be considered only when no suitable permissive alternative exists and after explicit approval.

## 3. Native Rust platform architecture

Wutils must be compiled for a dedicated Wicofe Rust target and native execution environment.

The platform stack is:

1. Wicofe kernel implements the documented Wicofe syscall ABI.
2. A Wicofe-native Rust platform layer / standard-library platform abstraction exposes required operating-system services to Rust.
3. Rust `std` and other required runtime facilities are built for the Wicofe target without relying on a Linux libc backend.
4. Wutils uses those native Rust interfaces for process, filesystem, terminal, signal, time, and I/O operations.

A custom target specification alone is insufficient if the selected Rust standard library still routes operating-system operations through Linux libc. The Wicofe Rust platform implementation must explicitly own those operating-system boundaries.

Wlibc is the separate C runtime project. Wutils must not require Wlibc merely because it is a Rust application. A future optional FFI dependency must be justified by an explicit architecture decision and must not replace the native Rust platform path.

## 4. Required platform services

The Wicofe Rust platform layer must provide and test the services required by enabled utilities, including:

- File descriptors and standard streams.
- File and directory operations, metadata, permissions, links, and path handling.
- Process creation, execution, waiting, exit status, and environment handling.
- Pipes, redirections, and descriptor inheritance.
- Signals and interruption behavior where required.
- Terminal detection, TTY operations, terminal dimensions, and terminal control.
- Clocks, timestamps, and time conversion.
- User and group identity where required by POSIX utilities.
- Locale and character handling to the extent required by the declared POSIX profile.

Each service must map to documented Wicofe ABI contracts. Missing kernel or ABI capabilities must be recorded as blockers rather than silently falling back to Linux behavior.

## 5. Utility compatibility and scope

Wutils targets the selected POSIX utility requirements. It does not promise GNU extensions, GNU command-line options, GNU-specific output formats, or GNU-specific corner-case behavior.

For each utility, maintain a compatibility record that identifies:
- The applicable POSIX edition and utility requirements.
- Implemented options and behavior.
- Unsupported or intentionally omitted extensions.
- Required Wicofe ABI services.
- Architecture coverage.
- Automated conformance and runtime test evidence.

Do not claim full POSIX conformance until the selected requirements have been tested on a running Wicofe system.

## 6. Architecture and build targets

Initial target architectures are x86_64 and aarch64. Native Wicofe ELF and executable-loader requirements are owned by the Wicofe executable/loader specifications and must be agreed before runtime claims are made.

Host builds and cross-compilation are development checks only. They do not establish that a Wutils binary is executable on Wicofe.

## 7. Licensing and upstream reuse

The uutils upstream source and each enabled dependency must be reviewed against the binding Wicofe dependency license policy. Keep upstream copyright and license notices intact. Do not copy files whose licensing or provenance is unclear.

A fork is preferred over a from-scratch utility suite when reusing the upstream implementation is technically practical and avoids disproportionate reimplementation effort. Wicofe-specific changes must remove Linux/GNU runtime assumptions from the native target rather than introducing a compatibility dependency.

## 8. Acceptance criteria

The Wutils native port may be declared operational only when:

1. The Wicofe Rust target and toolchain are reproducible.
2. The required Rust standard-library platform support does not depend on Linux libc.
3. The Wicofe ABI mapping for each implemented OS service is documented.
4. Initial utilities build and link as native Wicofe executables.
5. They execute on Wicofe under x86_64 and aarch64 test environments.
6. POSIX behavior is tested against the declared utility profile.
7. Dependency and upstream license provenance is recorded and approved.
8. CI distinguishes host tests, cross-build tests, emulator execution, and hardware validation.

## 9. Ownership boundaries

- **Wicofe-Kernel:** kernel mechanisms and syscall implementation.
- **Wicofe-ABI:** canonical syscall identifiers, calling conventions, data layouts, and error semantics.
- **Rust Wicofe platform/runtime:** native Rust OS services and `std` integration.
- **Wlibc:** separate C standard library and POSIX C runtime; not a mandatory Wutils dependency.
- **Wutils:** command-line utility implementations and POSIX utility behavior.
- **Wicofe-SDK / Wicofe-Build:** target toolchain, sysroot, reproducible builds, and CI orchestration.
- **Wicofe-Test:** cross-component and POSIX conformance tests.

## 10. References

- [Wicofe Project Principles](https://github.com/Waizusora/Wicofe-Doc/blob/main/PROJECT_PRINCIPLES.md)
- [Wicofe ABI](https://github.com/Waizusora/Wicofe-ABI)
- [Wicofe Kernel](https://github.com/Waizusora/Wicofe-Kernel)
- [Wicofe Third-Party Dependency License Policy](https://github.com/Waizusora/Wicofe-ThirdParty/blob/main/policies/DEPENDENCY_LICENSE_POLICY_0.2.md)
- [uutils upstream](https://github.com/uutils/coreutils)

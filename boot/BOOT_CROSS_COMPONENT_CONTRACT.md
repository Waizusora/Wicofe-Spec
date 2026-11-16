# Boot Handoff — Cross-Component Contract

- **Identifier:** WIC-SPEC-BOOT-XCOMP-001
- **Version:** 0.1.0
- **Status:** Draft
- **Canonical owner:** Wicofe-Kernel
- **Boot protocol target:** Limine Boot Protocol, base revision 6 currently requested by kernel code
- **Coordination:** Wicofe-Specs

## 1. Authority and scope

The normative boot foundation document is [Wicofe-Kernel/docs/BOOT_SPEC.md](https://github.com/Waizusora/Wicofe-Kernel/blob/main/docs/BOOT_SPEC.md). This cross-component contract records producer/consumer boundaries for boot artifacts and handoff. It does not redefine Limine's external protocol.

## 2. Boot chain responsibilities

- Firmware initializes the platform and transfers control to the bootloader.
- Limine loads the kernel ELF image and provides protocol responses.
- Wicofe-Kernel validates and normalizes the handoff into kernel-owned boot information.
- Wicofe-Build and Wicofe-Image produce reproducible kernel and boot-image artifacts.
- Wicofe-Installer may install the image and configure boot entries but must not reinterpret private kernel structures.
- Wicofe-Tests validates image structure and runtime boot behavior under named emulator/hardware configurations.

## 3. Handoff invariants

- Protocol-specific pointers and structures are consumed only in the boot adapter.
- Kernel subsystems receive normalized, kernel-owned representations rather than depending directly on bootloader response types.
- Required responses must be checked before use.
- All ranges, lengths, alignments, overlaps, and arithmetic must be validated.
- Bootloader-provided memory maps, module records, strings, and payloads are untrusted input.
- Kernel image and boot-module memory must remain reserved while referenced.
- Optional responses must have explicit absence semantics.
- Failure to validate mandatory state must stop boot with a bounded diagnostic path.

## 4. Architecture-specific obligations

x86-64 and AArch64 entry code must each document calling convention, initial CPU state, stack alignment, interrupt masking, address translation assumptions, required CPU features, and transition to kernel-owned execution state.

A successful cross-compile is not equivalent to runtime boot validation. Each architecture requires emulator validation; physical hardware validation is a separate evidence category.

## 5. Boot modules

Boot modules must have validated physical bounds, defined identity and metadata, explicit lifetime, and a trust policy. Loading bytes through the bootloader does not authenticate their publisher. Root-filesystem-critical modules must be available before the root filesystem is required and must not depend on that filesystem for retrieval.

## 6. Compatibility and versioning

The project must pin and record the bootloader release and protocol revision used for tested images. Changes to requested protocol revision, linker layout, ELF segments, entry trampoline, or boot metadata require coordinated kernel/build/image/test review.

The kernel's normalized boot-info structures are internal kernel interfaces unless separately promoted to a public contract. Do not treat Limine response structures as a stable Wicofe userspace ABI.

## 7. Conformance evidence

Tests should record bootloader version, image checksum, kernel commit, architecture, firmware mode, emulator/hardware, serial log, and expected milestones. Boot tests must distinguish ELF validation, image construction, emulator entry, memory-manager smoke tests, and full system boot.

The currently documented x86-64 QEMU/BIOS milestone is partial boot-foundation validation. AArch64 runtime validation remains pending according to the kernel README.

**This document does not claim complete boot support on both architectures.**

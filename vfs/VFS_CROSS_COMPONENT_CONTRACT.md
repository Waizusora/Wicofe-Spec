# Virtual Filesystem — Cross-Component Contract

- **Identifier:** WIC-SPEC-VFS-XCOMP-001
- **Version:** 0.1.0
- **Status:** Draft
- **Canonical owner:** Wicofe-Kernel
- **Coordination:** Wicofe-Specs
- **Consumers:** ONWFS, FAT32, future filesystem modules, process/file-descriptor layer, storage drivers

## 1. Authority

The canonical VFS and filesystem architecture is maintained in the kernel repository's [FILESYSTEM_SPEC.md](https://github.com/Waizusora/Wicofe-Kernel/blob/main/docs/FILESYSTEM_SPEC.md). This document captures cross-component responsibilities and required contract topics. It does not define Rust trait signatures or freeze internal kernel structures.

## 2. Layering

The intended path is:

`userspace syscall / libc` → `kernel file-descriptor layer` → `VFS` → `filesystem implementation` → `block I/O` → `device driver`

A filesystem must not depend directly on a particular storage controller. Block I/O and filesystem semantics remain separate contracts.

## 3. Ownership

- Wicofe-Kernel owns VFS core, mount namespace mechanics, object lifetime, operation dispatch, and block-I/O integration.
- ONWFS owns its on-disk format and filesystem-specific behavior.
- FAT32 support is initially a built-in interoperability implementation in Wicofe-Kernel.
- DKM filesystem modules may be considered only after the DKM and VFS module-facing contracts are accepted.
- Wicofe-ABI owns userspace-visible file and descriptor syscall semantics.
- Wicofe-Libc owns C wrappers and errno translation.
- Wicofe-Storage owns userspace storage administration tools, not kernel VFS internals.
- Wicofe-Tests owns cross-component filesystem conformance tests.

## 4. Semantic requirements

The VFS contract must define:

- Object identity separately from pathname identity.
- Mount crossing, mount lifetime, and busy unmount behavior.
- Inode/vnode and open-file-object lifetime.
- Descriptor duplication and shared-offset semantics.
- Partial read/write behavior and error propagation.
- Directory iteration consistency and termination.
- Rename and unlink behavior while objects remain open.
- Symlink traversal, loop limits, and path resolution rules.
- Metadata availability versus synthetic metadata.
- Permission and capability checks.
- Synchronization and durability guarantees.
- Unsupported-operation reporting and capability discovery.

The VFS must not fabricate POSIX semantics when a backing filesystem cannot provide them. Emulation must be explicit and documented.

## 5. Filesystem integration requirements

Each implementation must declare supported operations and capabilities, metadata fidelity, maximum name/path constraints, read-only/read-write modes, error mapping, concurrency assumptions, and crash/recovery behavior.

FAT32 does not natively store Unix ownership, POSIX mode bits, symlinks, or hard links. Any compatibility emulation must be identified as synthetic.

ONWFS is intended to support inode-based identity, symbolic and hard links, ownership, permissions, extended attributes, sparse files, and crash recovery, subject to its separately accepted disk-format specification.

## 6. Test requirements

Cross-component tests should exercise mount/unmount, path traversal, mount crossing, descriptor lifetime, partial I/O, rename/unlink with open handles, symlink loops, malformed filesystem metadata, concurrent access, and power-loss recovery.

Filesystem-specific unit tests remain in the implementation repository. Integration results must identify filesystem revision, kernel revision, architecture, image layout, and test environment.

## 7. Deferred decisions

VFS internal object types, locking strategy, cache policy, mount namespace semantics, exact error mapping, and stable module-facing operation table remain kernel-owned design work.

**This document does not declare a stable VFS ABI.**

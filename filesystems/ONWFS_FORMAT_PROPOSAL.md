# ONWFS On-Disk Format Proposal

- **Identifier:** WIC-SPEC-ONWFS-FORMAT-001
- **Version:** 0.2.1
- **Status:** Draft — cross-repository requirements and review checklist
- **Canonical design document:** [ONWFS Format Design 0.2](https://github.com/Waizusora/ONWFS/blob/main/docs/FORMAT_DESIGN_0.2.md)
- **Format owner:** ONWFS
- **Kernel integration owner:** Wicofe-Kernel
- **Coordination:** Wicofe-Specs
- **Initial architectures:** x86-64, AArch64 (format itself must be architecture-neutral)

## 1. Purpose

ONWFS means **Oh Nice WICOFE Filesystem** and is the planned native Wicofe filesystem. This document establishes cross-repository format requirements and a decision checklist. The ONWFS repository now contains a preliminary byte-layout proposal (Format Design 0.2.1). That proposal is not stable and must be reviewed and accepted by the ONWFS format owner before implementation claims compatibility.

The kernel's [filesystem specification](https://github.com/Waizusora/Wicofe-Kernel/blob/main/docs/FILESYSTEM_SPEC.md) remains the canonical architecture reference. The [VFS cross-component contract](../vfs/VFS_CROSS_COMPONENT_CONTRACT.md) defines integration responsibilities.

## 2. Design goals

The format is intended to support:

- Hierarchical directories and stable inode-like object identities.
- Symbolic links and hard links.
- UID/GID, POSIX-style permission metadata, and link counts.
- Extended attributes.
- Sparse files and large-file support.
- Explicit feature negotiation and format versioning.
- Metadata integrity checks and recoverable metadata transactions.
- Predictable behavior on power loss and interrupted writes.
- Efficient allocation on SSDs and conventional block devices.
- Offline inspection, checking, and repair tooling.

These are goals, not implemented or accepted format capabilities.

## 3. Format invariants

A future accepted format must specify:

1. Endianness, alignment, integer widths, and serialization rules.
2. Logical block size and supported physical sector sizes.
3. Primary and backup superblock locations and validation.
4. Filesystem UUID, generation, and format version.
5. Compatible, read-only-compatible, and incompatible feature flags.
6. Inode identity, allocation, generation, and reserved inode policy.
7. Directory record encoding, name length, and normalization/case policy.
8. File extent or block mapping representation.
9. Free-space metadata and consistency invariants.
10. Orphan/unlinked inode tracking and link-count semantics.
11. Timestamp epoch, resolution, range, and overflow behavior.
12. Extended-attribute namespace and size limits.
13. Checksum algorithm, seed, coverage, and versioning.
14. Journal/transaction layout, ordering, commit protocol, and replay.
15. Maximum filesystem, file, directory, and metadata sizes.
16. Clean/dirty state, mount modes, and read-only fallback.
17. Upgrade, downgrade, and forward/backward compatibility rules.

All arithmetic over disk-provided values must be checked for overflow and range before I/O or allocation. Unknown incompatible features must prevent read-write mounting.

## 4. Crash consistency and recovery

The format must choose and document a consistency model; metadata journaling, ordered data, and full data journaling are not interchangeable.

The specification must define persistence barriers/flush behavior, transaction atomicity assumptions, commit-record validation, replay idempotence, handling of torn writes, and behavior when recovery metadata is corrupt.

A successful write operation must not imply durable media persistence unless the defined synchronization operation has completed. If safe recovery cannot be established, the implementation must fail closed rather than silently mount inconsistent metadata read-write.

## 5. Integrity and threat model

Disk contents are untrusted input. Parsers must defend against malformed lengths, cyclic references, duplicate or overlapping extents, invalid inode references, integer overflow, and resource-exhaustion structures.

Checksums detect selected accidental corruption; they do not authenticate a filesystem or prevent malicious modification. Any authenticated-storage feature would require a separate key-management and threat-model decision.

## 6. Required companion tools

Before a format is declared usable, the project needs reproducible formatter, read-only inspector, consistency checker, and recovery/repair tooling. Repair operations must distinguish safe automatic repairs from destructive or ambiguous cases and require explicit operator confirmation for data-loss risks.

## 7. Decision record required before implementation

The ONWFS owner must publish an accepted format decision covering at minimum:

- Block and sector size policy.
- Superblock layout and redundant metadata.
- Inode and directory structures.
- Extent/allocation model.
- Journal design and durability guarantees.
- Checksums and corruption handling.
- Feature flags and version compatibility.
- Maximum limits and name semantics.
- Initial read-only/read-write implementation scope.

Until Format Design 0.2.1 is reviewed, unresolved layout decisions are closed, and a version 1 format is accepted, no on-disk layout is stable and no formatter should create media advertised as forward-compatible ONWFS.

## 8. Conformance plan

Wicofe-Tests should eventually include golden byte-level images, malformed-image fuzzing, boundary and overflow tests, interrupted-write/replay tests, cross-version mount tests, and independent checker validation. Test vectors must be versioned with the format.

**Status remains Draft. The canonical preliminary layout is maintained in the ONWFS repository; this cross-component document does not supersede it.**

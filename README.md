# Wicofe-Specs

Normative technical specifications and standards for the Wicofe operating system.

## Scope

This repository is the cross-project source of truth for specifications that define contracts shared by multiple components, including:

- System architecture and subsystem boundaries.
- Kernel/userspace ABI coordination (with syscall definitions owned by Wicofe-ABI).
- Dynamic Kernel Module (DKM) interface requirements.
- Filesystem and storage format specifications where ownership is cross-component.
- Boot, image, package, service, and interoperability contracts.
- Specification versioning, status, and compatibility policy.

## Ownership boundaries

A specification owned by a component remains authoritative in that component repository. Wicofe-Specs hosts cross-component specifications and indexes canonical specifications; it must not create competing definitions for Wicofe-ABI or Wicofe-Kernel interfaces.

## Specification lifecycle

Every specification must state:

- Unique identifier and title.
- Status: Draft, Proposed, Accepted, Implemented, Deprecated, or Superseded.
- Version and last-updated date.
- Scope, terminology, requirements, and compatibility rules.
- Security considerations and conformance criteria.
- References to implementation and test coverage, when available.

Changes to accepted specifications require review and a documented compatibility impact. A specification marked Accepted does not imply implementation.

## Suggested structure

- `architecture/`
- `abi/`
- `boot/`
- `dkm/`
- `filesystems/`
- `formats/`
- `process/`
- `governance/`

## Status

Initial repository baseline. No cross-component specification is declared stable solely by the existence of this repository.

## License

Original Wicofe specifications are intended to use MPL-2.0. Referenced external standards retain their own terms and attribution.

## Governance

- [Repository boundaries and specification governance](governance/REPOSITORY_BOUNDARIES.md)

This is a draft governance baseline. Component-owned ABI and kernel specifications remain authoritative in their respective repositories.

## Cross-component specification drafts

These documents coordinate contracts across repositories. They do not replace canonical component-owned specifications.

| Area | Cross-component document | Canonical authority | Status |
|---|---|---|---|
| DKM | [DKM contract](dkm/CROSS_COMPONENT_CONTRACT.md) | [Wicofe-Kernel DKM spec](https://github.com/Waizusora/Wicofe-Kernel/blob/main/docs/DKM_SPEC.md) | Draft |
| VFS | [VFS contract](vfs/VFS_CROSS_COMPONENT_CONTRACT.md) | [Wicofe-Kernel filesystem spec](https://github.com/Waizusora/Wicofe-Kernel/blob/main/docs/FILESYSTEM_SPEC.md) | Draft |
| ONWFS | [On-disk format proposal](filesystems/ONWFS_FORMAT_PROPOSAL.md) | ONWFS format owner; format decisions still open | Draft |
| Boot | [Boot handoff contract](boot/BOOT_CROSS_COMPONENT_CONTRACT.md) | [Wicofe-Kernel boot spec](https://github.com/Waizusora/Wicofe-Kernel/blob/main/docs/BOOT_SPEC.md) | Draft |
| System interface | [System interface baseline](system-interface/SYSTEM_INTERFACE_BASELINE.md) | [Wicofe-ABI baseline](https://github.com/Waizusora/Wicofe-ABI/blob/main/SPECIFICATION_BASELINE.md) | Draft |

## Important boundary

DKM ABI is a privileged kernel/module contract. The userspace syscall ABI is a separate contract owned by Wicofe-ABI. VFS internals and boot handoff implementation remain owned by Wicofe-Kernel. ONWFS disk layout is not finalized by the current proposal.


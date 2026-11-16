# Wicofe-Specs

Normative technical specifications and standards for the Wicofe operating system.

## Scope

This repository is the cross-project source of truth for specifications that define contracts shared by multiple components, including:

- System architecture and subsystem boundaries.
- Kernel/userspace ABI coordination (with syscall definitions owned by Wicofe-ABI).
- Dynamic Kernel Module (DKM) interface requirements.
- Filesystem and storage format specifications where ownership is cross-component.
- Boot, image, package, service, and interoperability contracts.
- Specification versioning, status, compatibility and traceability policy.

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

A specification marked Accepted does not imply implementation. Implementation status and verification evidence must be tracked independently.

## Suggested structure

- `architecture/`
- `abi/`
- `boot/`
- `dkm/`
- `filesystems/`
- `formats/`
- `process/`
- `governance/`

## Governance

- [Repository boundaries and specification governance](governance/REPOSITORY_BOUNDARIES.md)
- [Specification traceability and conformance policy](governance/SPECIFICATION_TRACEABILITY_POLICY_0.1.md)

This is a draft governance baseline. Component-owned ABI and kernel specifications remain authoritative in their respective repositories.

## Cross-component specification drafts

These documents coordinate contracts across repositories. They do not replace canonical component-owned specifications.

| Area | Cross-component document | Canonical authority | Status |
|---|---|---|---|
| DKM | [DKM contract](dkm/CROSS_COMPONENT_CONTRACT.md) | [Wicofe-Kernel DKM spec](https://github.com/Waizusora/Wicofe-Kernel/blob/main/docs/DKM_SPEC.md) | Draft |
| VFS | [VFS contract](vfs/VFS_CROSS_COMPONENT_CONTRACT.md) | [Wicofe-Kernel filesystem spec](https://github.com/Waizusora/Wicofe-Kernel/blob/main/docs/FILESYSTEM_SPEC.md) | Draft |
| ONWFS | [Cross-component format requirements](filesystems/ONWFS_FORMAT_PROPOSAL.md) | [ONWFS Format Design 0.2.2](https://github.com/Waizusora/ONWFS/blob/main/docs/FORMAT_DESIGN_0.2.md) and [Journal Proposal 0.1](https://github.com/Waizusora/ONWFS/blob/main/docs/JOURNAL_FORMAT_PROPOSAL_0.1.md) | Draft |
| Boot | [Boot handoff contract](boot/BOOT_CROSS_COMPONENT_CONTRACT.md) | [Wicofe-Kernel boot spec](https://github.com/Waizusora/Wicofe-Kernel/blob/main/docs/BOOT_SPEC.md) | Draft |
| System interface | [System interface baseline](system-interface/SYSTEM_INTERFACE_BASELINE.md) | [Wicofe-ABI baseline](https://github.com/Waizusora/Wicofe-ABI/blob/main/SPECIFICATION_BASELINE.md) | Draft |

## Important boundary

DKM ABI is a privileged kernel/module contract. The userspace syscall ABI is a separate contract owned by Wicofe-ABI. VFS internals and boot handoff implementation remain owned by Wicofe-Kernel. ONWFS disk layout is proposed in ONWFS Format Design 0.2 but is not accepted or stable.

## Documentation

See [docs/INDEX.md](docs/INDEX.md) for this repository's technical documentation. The [central repository documentation map](https://github.com/Waizusora/Wicofe-Docs/blob/main/REPOSITORY_DOCUMENTATION_MAP.md) identifies canonical ownership and cross-repository boundaries.

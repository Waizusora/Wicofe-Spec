# Wicofe-Spec

Normative technical specifications and standards for the Wicofe operating system.

## Scope

This repository is the cross-project source of truth for specifications that define contracts shared by multiple components, including:

- System architecture and subsystem boundaries.
- Kernel/userspace ABI coordination (with syscall definitions owned by Wicofe-ABI).
- Dynamic Kernel Module (DKM) interface requirements.
- Filesystem and storage format specifications where ownership is cross-component.
- Boot, image, package, service, and interoperability contracts.
- Unix userspace foundation: traditional WFH directory layout, Wlibc ISO C17/POSIX.1-2024 runtime requirements, and timezone database/runtime boundaries.
- Specification versioning, status, compatibility and traceability policy.

## Ownership boundaries

A specification owned by a component remains authoritative in that component repository. Wicofe-Spec hosts cross-component specifications and indexes canonical specifications; it must not create competing definitions for Wicofe-ABI or Wicofe-Kernel interfaces.

## Specification lifecycle and evidence

Every specification must state:

- Unique identifier and title.
- Lifecycle status: Draft, Proposed, Accepted, Deprecated, or Superseded.
- Version and last-updated date.
- Scope, terminology, requirements, and compatibility rules.
- Security considerations and conformance criteria.
- References to implementation and test coverage, when available.

Implementation state (Not started, In progress, Implemented, Removed) and verification state (Not run, Blocked, Failed, Passed) are recorded separately. An Accepted specification does not imply implementation, and an implemented feature is not necessarily verified.

## Suggested structure

- `architecture/`
- `abi/`
- `boot/`
- `dkm/`
- `filesystems/`
- `formats/`
- `process/`
- `userspace/`
- `governance/`

## Governance

- [Repository boundaries and specification governance](governance/REPOSITORY_BOUNDARIES.md)
- [Specification traceability and conformance policy](governance/SPECIFICATION_TRACEABILITY_POLICY_0.1.md)

This is a draft governance baseline. Component-owned ABI and kernel specifications remain authoritative in their respective repositories.

## System implementation roadmap

- [Bootable CLI vertical slice roadmap 0.1](architecture/BOOTABLE_VERTICAL_SLICE_ROADMAP_0.1.md) — staged, evidence-driven implementation plan from kernel foundation to a bootable CLI system and subsequent persistent installation/update milestones.

## Cross-component specification drafts

The [Native Wicofe Rust Utilities Porting Requirements 0.1](userspace/WUTILS_NATIVE_PORTING_REQUIREMENTS_0.1.md) defines the native Wutils direction: Wicofe Rust target and platform runtime, no Linux libc backend, no GNU compatibility requirement, and POSIX utility behavior.

These documents coordinate contracts across repositories. They do not replace canonical component-owned specifications.

| Area | Cross-component document | Canonical authority | Status |
|---|---|---|---|
| Unix userspace | [Unix userspace foundation 0.1](userspace/UNIX_USERSPACE_FOUNDATION_0.1.md) | [Wicofe-ABI](https://github.com/Waizusora/Wicofe-ABI), [Wicofe-Kernel](https://github.com/Waizusora/Wicofe-Kernel), [Wlibc](https://github.com/Waizusora/WLibc) | Proposed |
| DKM | [DKM contract](dkm/CROSS_COMPONENT_CONTRACT.md) | [Wicofe-Kernel DKM spec](https://github.com/Waizusora/Wicofe-Kernel/blob/main/docs/DKM_SPEC.md) | Draft |
| VFS | [VFS contract](vfs/VFS_CROSS_COMPONENT_CONTRACT.md) | [Wicofe-Kernel filesystem spec](https://github.com/Waizusora/Wicofe-Kernel/blob/main/docs/FILESYSTEM_SPEC.md) | Draft |
| ONWFS | [Cross-component format requirements](filesystems/ONWFS_FORMAT_PROPOSAL.md) | [ONWFS Format Design 0.2.2](https://github.com/Waizusora/ONWFS/blob/main/docs/FORMAT_DESIGN_0.2.md) and [Journal Proposal 0.1](https://github.com/Waizusora/ONWFS/blob/main/docs/JOURNAL_FORMAT_PROPOSAL_0.1.md) | Draft |
| Boot | [Boot handoff contract](boot/BOOT_CROSS_COMPONENT_CONTRACT.md) | [Wicofe-Kernel boot spec](https://github.com/Waizusora/Wicofe-Kernel/blob/main/docs/BOOT_SPEC.md) | Draft |
| Frenit | [Frenit integration contract](boot/FRENIT_INTEGRATION_CONTRACT_0.1.md) | [Frenit repository](https://github.com/Waizusora/Frenit) | Proposed; native PID 1 integration not verified |
| System interface | [System interface baseline](system-interface/SYSTEM_INTERFACE_BASELINE.md) | [Wicofe-ABI baseline](https://github.com/Waizusora/Wicofe-ABI/blob/main/SPECIFICATION_BASELINE.md) | Draft |

## Important boundary

DKM ABI is a privileged kernel/module contract. The userspace syscall ABI is a separate contract owned by Wicofe-ABI. VFS internals and boot handoff implementation remain owned by Wicofe-Kernel. ONWFS disk layout is proposed in ONWFS Format Design 0.2 but is not accepted or stable.

## Documentation

See [docs/INDEX.md](docs/INDEX.md) for this repository's technical documentation. The [central repository documentation map](https://github.com/Waizusora/Wicofe-Doc/blob/main/REPOSITORY_DOCUMENTATION_MAP.md) identifies canonical ownership and cross-repository boundaries.

## Wicofe-wide principles

Wicofe is an independent Unix-like, POSIX-compatible operating system and software ecosystem, designed as an alternative to established and widely adopted software ecosystems and technologies, including GNU. Native components must use Wicofe interfaces and must not require Linux libc or GNU compatibility layers. Prefer permissive licenses; avoid GPL/AGPL, and consider LGPL only when no suitable permissive alternative exists. Reuse or fork upstream software when implementing it from scratch would be disproportionately difficult. See the [canonical project principles](https://github.com/Waizusora/Wicofe-Doc/blob/main/PROJECT_PRINCIPLES.md).

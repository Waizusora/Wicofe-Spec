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

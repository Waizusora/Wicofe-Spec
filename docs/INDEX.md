# Cross-Component Specifications Index

This repository owns normative specifications that coordinate multiple Wicofe repositories. Component-owned specifications remain authoritative in their owning repositories.

## Governance
- [Repository boundaries and governance](../governance/REPOSITORY_BOUNDARIES.md)
- [Specification traceability and conformance policy 0.1](../governance/SPECIFICATION_TRACEABILITY_POLICY_0.1.md) — stable requirement IDs, separate specification/implementation/verification states, and evidence links.

## System implementation roadmap
- [Bootable CLI vertical slice roadmap 0.1](../architecture/BOOTABLE_VERTICAL_SLICE_ROADMAP_0.1.md) — evidence-driven phases from the existing kernel boot foundation through native PID 1, CLI, persistent storage, installer, and updates.

## Cross-component contracts
- [Unix userspace foundation 0.1](../userspace/UNIX_USERSPACE_FOUNDATION_0.1.md) — proposed traditional Unix filesystem hierarchy (WFH), Wlibc POSIX scope, native syscall boundary, and IANA TZif timezone runtime.
- [DKM cross-component contract](../dkm/CROSS_COMPONENT_CONTRACT.md)
- [VFS cross-component contract](../vfs/VFS_CROSS_COMPONENT_CONTRACT.md)
- [ONWFS cross-component requirements](../filesystems/ONWFS_FORMAT_PROPOSAL.md)
- [Boot handoff contract](../boot/BOOT_CROSS_COMPONENT_CONTRACT.md)
- [Frenit integration contract 0.1](../boot/FRENIT_INTEGRATION_CONTRACT_0.1.md) — canonical init-system identity, repository ownership, kernel/ABI prerequisites and native PID 1 integration gates.
- [System interface baseline](../system-interface/SYSTEM_INTERFACE_BASELINE.md)

## Ownership rules
- [Wicofe-ABI](https://github.com/Waizusora/Wicofe-ABI) owns public userspace syscall ABI definitions.
- [Wicofe-Kernel](https://github.com/Waizusora/Wicofe-Kernel) owns kernel mechanisms, VFS internals, boot implementation, and DKM ABI.
- [Wlibc](https://github.com/Waizusora/WLibc) owns the C runtime and POSIX C interfaces.
- [ONWFS](https://github.com/Waizusora/ONWFS) owns ONWFS disk layout.
- This repository owns cross-repository requirements and coordination; it must not create competing component specifications.

## Lifecycle
Every specification states identifier, version, status, scope, compatibility, security, conformance, and implementation references. Accepted does not mean implemented. Drafts do not freeze compatibility. Specification lifecycle, implementation state, verification state and release support must be tracked separately.

- [Central Wicofe documentation index](https://github.com/Waizusora/Wicofe-Doc/blob/main/INDEX.md)
- [Repository documentation map](https://github.com/Waizusora/Wicofe-Doc/blob/main/REPOSITORY_DOCUMENTATION_MAP.md)

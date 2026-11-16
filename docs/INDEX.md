# Cross-Component Specifications Index

This repository owns normative specifications that coordinate multiple Wicofe repositories. Component-owned specifications remain authoritative in their owning repositories.

## Cross-component contracts
- [Repository boundaries and governance](../governance/REPOSITORY_BOUNDARIES.md)
- [DKM cross-component contract](../dkm/CROSS_COMPONENT_CONTRACT.md)
- [VFS cross-component contract](../vfs/VFS_CROSS_COMPONENT_CONTRACT.md)
- [ONWFS cross-component requirements](../filesystems/ONWFS_FORMAT_PROPOSAL.md)
- [Boot handoff contract](../boot/BOOT_CROSS_COMPONENT_CONTRACT.md)
- [System interface baseline](../system-interface/SYSTEM_INTERFACE_BASELINE.md)

## Ownership rules
- [Wicofe-ABI](https://github.com/Waizusora/Wicofe-ABI) owns public userspace syscall ABI definitions.
- [Wicofe-Kernel](https://github.com/Waizusora/Wicofe-Kernel) owns kernel mechanisms, VFS internals, boot implementation, and DKM ABI.
- [ONWFS](https://github.com/Waizusora/ONWFS) owns ONWFS disk layout.
- This repository owns cross-repository requirements and coordination; it must not create competing component specifications.

## Lifecycle
Every specification states identifier, version, status, scope, compatibility, security, conformance, and implementation references. Accepted does not mean implemented. Drafts do not freeze compatibility.

- [Central Wicofe documentation index](https://github.com/Waizusora/Wicofe-Docs/blob/main/INDEX.md)
- [Repository documentation map](https://github.com/Waizusora/Wicofe-Docs/blob/main/REPOSITORY_DOCUMENTATION_MAP.md)

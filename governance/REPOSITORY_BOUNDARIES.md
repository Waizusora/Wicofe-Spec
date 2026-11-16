# Repository Boundary and Specification Governance

**Status:** Draft 0.1  
**Scope:** Cross-repository ownership and normative specification workflow.

## 1. Purpose

This document defines where authoritative contracts live and how changes that affect multiple Wicofe repositories are coordinated. It does not claim that proposed interfaces are implemented or stable.

## 2. Canonical ownership

- Wicofe-ABI owns userspace syscall identifiers, calling conventions, shared data layouts, and ABI compatibility policy.
- Wicofe-Kernel owns kernel mechanisms, VFS implementation, driver core, and the privileged DKM ABI.
- ONWFS owns its on-disk format and filesystem-specific implementation.
- Each component repository owns its implementation details and component-local tests.
- Wicofe-Specs owns cross-component specifications and indexes authoritative specifications; it must not fork or redefine component-owned contracts.
- Wicofe-Tests owns cross-repository conformance and integration tests.
- Wicofe-Docs owns explanatory user, administrator, and developer documentation.
- Wicofe-ThirdParty owns third-party provenance, license review records, and patch tracking.

## 3. Specification status

Use exactly one primary status:

- **Draft:** under active design; may change without compatibility guarantees.
- **Proposed:** ready for review; not yet accepted.
- **Accepted:** approved design; implementation is not implied.
- **Implemented:** implementation exists; this status alone does not mean tests pass.
- **Deprecated:** retained for migration; new use is discouraged.
- **Superseded:** replaced by a named newer specification.

A specification must also record its version, owner, reviewers, date, references, security considerations, and conformance criteria.

## 4. Change process

1. Identify all affected owning repositories and consumers.
2. Open or update the proposal in the canonical owner repository.
3. Describe motivation, alternatives, compatibility impact, security impact, and migration.
4. Obtain review from affected component owners.
5. Update implementation and conformance tests in their owning repositories.
6. Link the accepted revision and test evidence; do not mark implementation complete based on documentation alone.

## 5. Versioning and compatibility

Use explicit major/minor/patch versions for specifications that are consumed across repository boundaries. A major version denotes an incompatible contract change; a minor version adds backward-compatible behavior; a patch version clarifies or corrects text without changing the contract. The owning specification must define exceptions where this model is unsuitable.

Never infer ABI stability from a Git tag, crate version, or document status. Compatibility guarantees must be explicit.

## 6. Security and trust

Treat boot data, userspace pointers, device input, filesystem metadata, module metadata, package metadata, and network input as untrusted. Specifications must define validation, ownership, privilege, failure, and resource-lifetime rules where relevant.

## 7. Implementation status

Repository creation and accepted design documents are not implementation evidence. Claims of support must link to code and reproducible tests for the stated target.

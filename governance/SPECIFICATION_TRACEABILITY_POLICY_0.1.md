# Specification Traceability and Conformance Policy 0.1

**Identifier:** WIC-SPEC-GOV-TRACE-001  
**Version:** 0.1.0  
**Status:** Draft  
**Owner:** Wicofe-Specs  
**Consumers:** All Wicofe component repositories and Wicofe-Tests

## 1. Purpose

Define a common method for connecting normative requirements to their canonical owner, implementation, conformance tests and execution evidence. This policy is intended to prevent architecture documents, implementation status and test status from being conflated.

This policy governs metadata and coordination. It does not move ownership of component specifications into Wicofe-Specs.

## 2. Authority and ownership

- A component repository remains authoritative for contracts internal to that component.
- Wicofe-ABI owns userspace syscall ABI definitions.
- Wicofe-Kernel owns kernel mechanisms, VFS internals, boot implementation and privileged DKM ABI.
- ONWFS owns ONWFS on-disk format and filesystem-specific consistency semantics.
- Wicofe-Specs owns cross-component requirements, shared governance and indexes to canonical specifications.
- Wicofe-Tests owns cross-repository conformance and integration test plans/results.
- Implementation repositories own implementation evidence and component-local tests.

A cross-component document may summarize a component contract but must link to the canonical owner and must not define a competing version of its details.

## 3. Requirement identity

Every normative requirement should have a stable identifier that does not depend on its line number or heading position.

Recommended form:

`WIC-<DOMAIN>-<SUBSYSTEM>-<NNN>`

Examples:
- `WIC-BOOT-HANDOFF-001`
- `WIC-ABI-USERPTR-004`
- `WIC-ONWFS-JOURNAL-012`

Identifiers are illustrative. The owning repository allocates them and must not reuse an identifier after deletion; mark removed requirements as withdrawn or superseded.

Each requirement record must state:
- unique ID and concise normative statement;
- rationale and scope;
- requirement owner and affected consumers;
- priority or criticality;
- verification method and acceptance criteria;
- security/safety implications;
- status and revision history;
- linked implementation and test references, when available.

Use normative terms such as MUST, MUST NOT, SHOULD and MAY consistently. A MUST requirement needs objective, observable acceptance criteria.

## 4. Specification metadata and lifecycle

Each specification should include:
- stable identifier, title and version;
- one primary lifecycle status;
- canonical owner and affected consumers;
- last-updated date and review participants;
- scope, terminology and normative requirements;
- compatibility and migration policy;
- security and failure considerations;
- conformance criteria;
- implementation/test links, or an explicit statement that they are not yet available;
- references to superseding or related specifications.

The specification lifecycle has exactly one primary status:
- **Draft:** incomplete working material; not binding.
- **Proposed:** ready for technical review; not binding.
- **Accepted:** approved design; implementation is not implied.
- **Deprecated:** retained for migration; new use is discouraged.
- **Superseded:** replaced by a named newer specification.

**Implemented is not a specification lifecycle status.** Record implementation state independently. A specification may remain Accepted while one or more implementations are in progress or complete.

## 5. Separate design, implementation and verification state

Track these dimensions independently:

| Dimension | Example values | Meaning |
|---|---|---|
| Specification lifecycle | Draft, Proposed, Accepted, Deprecated, Superseded | Review/approval state of the contract |
| Implementation state | Not started, In progress, Implemented, Removed | State of code in the owning repository |
| Verification state | Not run, Blocked, Failed, Passed | Latest execution evidence for a defined test scope |
| Release support | Unsupported, Experimental, Supported, Deprecated | Explicit product support decision |

An Accepted specification does not prove implementation. An implementation marked complete does not prove conformance. A passing test proves only the tested revision, configuration, architecture and scope.

Never use a single status field to imply all four dimensions.

## 6. Traceability chain

The desired traceability chain is:

`requirement → canonical specification revision → implementation commit/path → test ID → execution report/artifact`

For each normative requirement:
1. Identify the canonical specification and exact revision.
2. Link implementation files or commits when implementation begins.
3. Link one or more tests that directly verify the acceptance criteria.
4. Link the latest execution report, including architecture and environment.
5. Record known gaps, waivers and risk acceptance.

A requirement may legitimately have no implementation or test yet. Mark the missing link explicitly as **not implemented** or **not verified**.

## 7. Conformance test requirements

A conformance test must identify:
- stable test ID;
- requirement ID and specification version;
- participating repositories and source commits;
- architecture, CPU baseline and environment;
- fixture/image digest and prerequisites;
- exact command/procedure;
- expected result and failure criteria;
- observed result, timestamp and status;
- retained logs/artifacts where safe.

Use PASS only after successful execution. Use FAIL for an observed violation, BLOCKED when prerequisites prevent execution, and NOT RUN when no execution occurred. Compilation, static inspection, emulator runtime and physical hardware runtime are different evidence levels.

Wicofe-Tests owns cross-repository test plans and results; component-local unit tests remain with the component.

## 8. Change control and impact analysis

Any normative change must include:
- reason and problem statement;
- affected requirement IDs;
- affected producers, consumers and repositories;
- compatibility classification;
- migration/deprecation plan;
- security and failure-mode impact;
- required implementation and test updates.

For accepted specifications, obtain review from affected contract owners before merging an incompatible change. Update downstream compatibility declarations and conformance tests in the same coordinated change window where practical.

Do not edit a component-owned specification through a cross-component summary. Submit the change to its canonical owner and update the cross-reference afterward.

## 9. Versioning and compatibility

Use explicit semantic versions where a specification is consumed across repository boundaries:
- major: incompatible contract change;
- minor: backward-compatible addition or clarified optional behavior;
- patch: editorial correction or clarification with no normative behavior change.

The canonical owner may define a different scheme when the artifact format or protocol requires it, but must document that scheme.

Version numbers do not independently guarantee runtime compatibility. Implementations must declare the exact supported specification/ABI range and test it.

## 10. Security and safety requirements

Prioritize traceability for trust boundaries and destructive operations, including:
- boot handoff and executable/module loading;
- syscall pointer and length validation;
- DKM privilege and lifecycle;
- filesystem parsing, journaling and repair;
- block-device identity and destructive storage plans;
- package/update signature and anti-rollback policy;
- privileged service IPC;
- backup, restore and recovery.

Acceptance criteria should include malformed input, resource exhaustion, interrupted operations, privilege failures and safe error behavior where relevant.

## 11. Review checklist

Before accepting a cross-component specification:
- [ ] Canonical owner and consumers are identified.
- [ ] No competing component-owned contract is introduced.
- [ ] Requirements have stable IDs and measurable criteria.
- [ ] Compatibility and failure behavior are explicit.
- [ ] Security and safety implications are reviewed.
- [ ] Implementation ownership is assigned.
- [ ] Conformance tests are identified or explicitly deferred.
- [ ] Status does not imply unverified implementation.
- [ ] Related indexes and repository maps are updated.

## 12. Open decisions

- Machine-readable requirement metadata format.
- Whether requirement IDs are maintained in Markdown front matter, tables or separate files.
- Automated link and traceability validation tooling.
- Review quorum for cross-repository accepted specifications.
- Policy for requirement waivers and expiration.
- Release-level conformance profile and minimum required test set.

## 13. Completion criteria

This policy is ready for acceptance when component owners agree on requirement identity, status separation, traceability fields, change review and conformance evidence. Automation and machine-readable schemas should be introduced only after the metadata contract is accepted.

**This is a draft governance policy. It does not declare existing specifications accepted or any implementation conformant.**

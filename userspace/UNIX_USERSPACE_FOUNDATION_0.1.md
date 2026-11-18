# Unix Userspace Foundation Specification 0.1

- **Identifier:** WIC-SPEC-UNIX-USERSPACE-001
- **Status:** Proposed
- **Version:** 0.1
- **Owner:** Wicofe-Specs
- **Applies to:** Wicofe, Wicofe-Kernel, Wicofe-ABI, Wlibc, Wicofe-Lib, Frenit, Wicofe-Services, Wicofe-SDK, Wicofe-Build, Wicofe-Image, Wicofe-Installer, Wicofe-Tests
- **Implementation status:** Not started as an integrated system contract
- **Verification status:** Not run

## 1. Purpose

Define the shared Unix-like userspace foundation for Wicofe: a traditional Unix filesystem hierarchy, POSIX-facing C runtime, and timezone data/runtime model. Wicofe targets POSIX conformance while retaining its own kernel, syscall ABI, and implementation architecture.

This document is a proposed cross-component contract. It does not claim that the hierarchy, complete POSIX runtime, timezone tooling, or related services are implemented.

## 2. Design principles

1. Use a traditional Unix directory organization with clearly assigned ownership and lifecycle.
2. Define POSIX interfaces at the userspace boundary and implement them against the canonical Wicofe syscall ABI.
3. Keep kernel mechanisms separate from userspace policy, databases, and conversion logic.
4. Keep UTC-based system time independent from local timezone presentation.
5. Use IANA Time Zone Database data and TZif files for timezone rules.
6. Track conformance by specification edition, interface, behavior, architecture, and test evidence.
7. Do not add GNU or BSD compatibility extensions as project goals. Additional POSIX interfaces may be implemented in a separate POSIX layer when that is cleaner than placing them in the core C library.

## 3. Wicofe Unix filesystem hierarchy (WFH)

WFH is the project-defined directory organization. POSIX specifies pathname and filesystem behavior but does not prescribe a complete operating-system directory hierarchy. WFH therefore defines Wicofe's system layout while preserving conventional Unix roles.

### 3.1 Proposed root layout

```text
/
├── boot/
├── bin/
├── sbin/
├── lib/
├── libexec/
├── etc/
├── dev/
├── proc/
├── sys/
├── run/
├── tmp/
├── home/
├── root/
├── usr/
│   ├── bin/
│   ├── sbin/
│   ├── lib/
│   ├── libexec/
│   ├── include/
│   ├── share/
│   └── local/
├── var/
│   ├── log/
│   ├── cache/
│   ├── lib/
│   ├── spool/
│   └── tmp/
└── mnt/
    └── media/
```

### 3.2 Directory roles

| Path | Wicofe role |
|---|---|
| `/boot` | Boot artifacts and boot configuration required by the platform |
| `/bin` | Essential user commands needed for basic system operation |
| `/sbin` | Essential system administration commands |
| `/lib` | Essential runtime libraries and loader support required before the full `/usr` environment is available |
| `/libexec` | Private helper executables for base system components |
| `/etc` | Host-specific system configuration |
| `/dev` | Device nodes and device-facing filesystem namespace |
| `/proc` | Process and selected kernel runtime information |
| `/sys` | Device and subsystem information/control interfaces |
| `/run` | Volatile system runtime state, recreated during boot |
| `/tmp` | Temporary files with system-defined cleanup and permission policy |
| `/home` | User home directories |
| `/root` | Administrator home directory |
| `/usr/bin` | General user commands |
| `/usr/sbin` | Non-essential system administration commands |
| `/usr/lib` | General shared libraries and architecture-specific support files |
| `/usr/libexec` | Private helper executables for installed software |
| `/usr/include` | System and development headers |
| `/usr/share` | Architecture-independent data, documentation, locale and timezone data |
| `/usr/local` | Locally administered software and data |
| `/var/log` | Persistent system and service logs |
| `/var/cache` | Re-creatable cached data |
| `/var/lib` | Persistent variable state owned by system components |
| `/var/spool` | Queued work and deferred processing data |
| `/var/tmp` | Temporary data intended to survive ordinary cleanup of `/tmp` |
| `/mnt` | Administrative mount points |
| `/mnt/media` | Removable media mount area |

The hierarchy is a policy convention, not a requirement that every directory reside on a separate filesystem. The root filesystem must contain enough essential tools, libraries, configuration, and mount points to reach the configured normal operating environment.

### 3.3 Mount and pseudo-filesystem policy

- Wicofe VFS owns namespace, mount traversal, file objects, and filesystem operation dispatch.
- Frenit coordinates boot-time mount ordering using declared mount configuration and dependency policy; it does not implement filesystem semantics.
- `/dev`, `/proc`, and `/sys` are mount points for explicitly defined device, process, and system interfaces. Their implementation and exposed ABI must be documented; the names alone do not imply Linux-specific semantics.
- `/run` is volatile runtime state and must be initialized before services that depend on it.
- Separate `/usr`, `/var`, and `/home` filesystems are supported when the boot dependency graph can mount them safely. Essential boot tools and libraries must remain available before `/usr` is mounted.
- Directory merging through mandatory symbolic links is not required. A path may be a real directory or a documented mount point.
- Filesystem implementations must document support and emulation for ownership, permission bits, links, timestamps, and other POSIX-visible metadata.

## 4. POSIX C runtime: Wlibc

**Wlibc (Wicofe C Library)** is the official C library and POSIX runtime project for Wicofe. Its implementation plan is to fork musl and port the required runtime to the native Wicofe syscall ABI, subject to source, license, and provenance review.

Wlibc is not a Linux syscall compatibility shim. Upstream Linux-specific syscall code, process assumptions, startup behavior, threading integration, and loader assumptions must be replaced or adapted to documented Wicofe contracts.

### 4.1 Conformance target

- Target the selected edition of ISO C and the applicable POSIX.1 interfaces as a coherent, testable conformance profile.
- The project goal is full conformance to the declared POSIX profile, not merely successful compilation of selected applications.
- GNU and BSD compatibility extensions are out of scope as project requirements.
- Do not expose non-standard extensions as required interfaces.
- Additional POSIX interfaces may be implemented in a separate Wicofe POSIX layer when appropriate. Such a layer must use the same public headers, ABI rules, error semantics, and conformance tests and must not create a competing API contract.
- Publish a per-interface conformance matrix showing implemented, partially implemented, unsupported, and verified behavior.

The exact POSIX edition and any permitted optional-feature profile must be explicitly selected before claiming conformance. A partial implementation must be described as partial.

### 4.2 Component boundaries

| Component | Responsibility |
|---|---|
| Wicofe-ABI | Canonical syscall numbers, calling convention, data layouts, raw errors, and ABI versioning |
| Wicofe-Kernel | Kernel mechanisms implementing the syscall ABI |
| Wlibc | ISO C runtime, POSIX C interfaces, startup/runtime glue, errno mapping, and conformance tests |
| Wicofe POSIX layer (if needed) | Additional POSIX interfaces whose implementation is better separated from libc internals |
| Wicofe-Lib | Shared Rust userspace libraries and safe wrappers; not a replacement for Wlibc |
| Wicofe-SDK | Target headers, compiler configuration, startup objects, linker configuration, and sysroot packaging |
| Wicofe-Build | Reproducible build and integration orchestration |

No consumer may independently assign syscall numbers or redefine public ABI structures. Kernel and userspace interfaces must be specified before Wlibc depends on them.

### 4.3 Required runtime domains

The Wlibc plan must cover the applicable ISO C and POSIX domains, including:
- process startup, arguments, environment, and termination;
- file descriptors, filesystem operations, directories, links, metadata, and I/O;
- process and thread lifecycle;
- signals and interruption semantics;
- memory allocation and memory mapping;
- pthread synchronization and thread-local storage;
- clocks, timers, and calendar-time conversion;
- pipes, polling, and IPC interfaces required by the selected POSIX profile;
- sockets and networking interfaces required by the selected POSIX profile;
- locale, character classification, multibyte conversion, and wide-character interfaces;
- mathematical and standard utility interfaces;
- dynamic loading only when its executable and loader ABI is specified and implemented.

The list is a scope checklist, not evidence that the corresponding kernel ABI or implementation already exists.

### 4.4 Linking and loader policy

Support for static and dynamic linking must be tracked independently. Dynamic linking requires a defined executable format, process entry contract, interpreter lookup, relocation rules, symbol resolution, TLS behavior, and loader/kernel interaction. Do not claim dynamic-link support until end-to-end tests pass.

Wlibc must not adopt GNU symbol-versioning or other GNU loader conventions as a compatibility objective. The Wicofe executable and shared-object ABI must be specified on its own terms.

## 5. Timekeeping and timezone database

### 5.1 Kernel timekeeping

The kernel provides clock sources and clock interfaces. The system clock represents Unix/POSIX time and is maintained independently of local timezone configuration. Monotonic clocks are separate from wall-clock time and must not be affected by timezone changes.

The kernel does not need to parse IANA timezone rules or store a local UTC offset.

### 5.2 Userspace timezone data

Wicofe uses the IANA Time Zone Database as the authoritative source of civil-time rules and TZif as the installed binary format.

Proposed paths:

- `/usr/share/zoneinfo/` — compiled TZif zone files organized by IANA identifiers.
- `/etc/localtime` — active timezone data, either a copy or a link to the selected zone file.
- `/etc/timezone` — optional canonical IANA identifier such as `Asia/Jakarta`.
- `/var/lib/tzdata/version` — installed database release identifier, if maintained by the package.

Wicofe should support TZif v2, v3, and v4, subject to parser conformance and explicit handling of unsupported extensions. The runtime must also support POSIX TZ environment strings as required by the selected POSIX profile.

### 5.3 Runtime ownership

Timezone parsing and conversion belong in Wlibc's time runtime or a tightly coupled timezone runtime used by Wlibc. Applications must not require a timezone daemon merely to call `localtime()`, `mktime()`, or `tzset()`.

The runtime must support UTC conversion, local civil-time conversion, historical transitions represented by the installed database, daylight-saving transitions, and per-process `TZ` overrides.

### 5.4 Configuration and updates

A userspace utility such as `tzconfig` may list, inspect, and select IANA timezone identifiers. The utility validates the requested zone, updates configuration safely, and refreshes any process/service state that explicitly caches timezone data.

Updating tzdata must not change the kernel's UTC clock. Package installation must validate files and activate a complete database update atomically so readers do not observe partially replaced TZif data.

Frenit may order time synchronization services and other consumers, but it does not implement timezone parsing or convert timestamps itself.

## 6. Cross-repository ownership

| Repository | Required responsibility |
|---|---|
| Wicofe | Whole-system architecture and project roadmap |
| Wicofe-Specs | This shared userspace foundation contract |
| Wicofe-Kernel | VFS, clock sources, clock syscalls, process/thread/memory mechanisms |
| Wicofe-ABI | Public syscall and data-layout contract consumed by Wlibc |
| Wlibc | C runtime, POSIX interfaces, TZif runtime, conformance matrix |
| Wicofe-Lib | Shared Rust userspace libraries |
| Frenit | Init/service lifecycle and boot-time mount orchestration |
| Wicofe-Services | Time synchronization and other system daemons |
| Wicofe-SDK | C headers, toolchain target, sysroot and linking support |
| Wicofe-Build | Cross-repository build graph |
| Wicofe-Image / Wicofe-Installer | Installation of hierarchy, base utilities, Wlibc, and tzdata packages |
| Wicofe-Tests | POSIX, filesystem, time, timezone, and integration conformance tests |

## 7. Security and reliability requirements

- Validate all user pointers, lengths, flags, and structures at the syscall boundary.
- Keep timezone data and configuration readable by applications but writable only by authorized package/configuration operations.
- Reject path traversal and invalid timezone identifiers in timezone management tools.
- Treat TZif files as untrusted input; parsers must validate counts, offsets, transitions, bounds, and arithmetic overflow.
- Preserve atomicity when replacing active timezone data or updating package-managed databases.
- Define permissions and ownership for runtime directories and device/process interfaces.
- Keep persistent state separate from volatile runtime state.
- Do not let timezone changes alter monotonic time or the underlying UTC timestamp.

## 8. Conformance and acceptance criteria

The specification can move from Proposed to Accepted only after the owners review directory ownership, POSIX profile, ABI dependencies, and timezone update semantics.

Implementation completion requires:
1. WFH directory roles and mount ordering documented and tested in boot images.
2. VFS semantics and filesystem metadata behavior validated against the selected POSIX requirements.
3. Wlibc upstream version, license, patch set, and native syscall backend recorded.
4. The selected ISO C/POSIX edition and optional feature profile explicitly declared.
5. A machine-readable or otherwise auditable Wlibc conformance matrix maintained.
6. Required POSIX tests executed on Wicofe targets, not only on the host.
7. TZif parser tests covering valid, malformed, historical, DST, and boundary inputs.
8. Time tests proving timezone changes do not modify UTC or monotonic clocks.
9. Package/update tests proving timezone data activation is atomic.
10. Cross-repository integration tests covering Wlibc, Wicofe-ABI, kernel, Frenit, SDK, and image assembly.

## 9. References

- POSIX.1-2024 / The Open Group Base Specifications: https://pubs.opengroup.org/onlinepubs/9799919799/
- IANA Time Zone Database: https://www.iana.org/time-zones
- TZif specification, RFC 9636: https://www.rfc-editor.org/rfc/rfc9636
- musl project: https://musl.libc.org/

## 10. Revision history

- 0.1 — Initial proposed Unix userspace foundation: WFH, Wlibc POSIX scope, and IANA TZif runtime.

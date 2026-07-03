# Changelog

## [Unreleased]

### Fixed

- `list-namespaces` returned an empty list for DEX/Dalvik programs. The handler
  scanned `getSymbolIterator()`, which only yields memory-location labels and so
  missed DEX package (`createNameSpace`) and class (`createClass`) namespaces
  — none of which are memory labels. It now walks the namespace tree from the
  global namespace via `getChildren()`, which works uniformly for native
  (ELF/PE) and DEX programs. Added integration regression tests
  (`TestDexNamespaces`, `TestNamespacesNative`).

### Added

- Android APK / DEX analysis guidance: new `docs/flows/android-apk.md` flow and a
  SKILL.md section covering Ghidra's built-in Dalvik/APK/DEX loaders, the
  class-qualified `::` symbol naming, ambiguous-method handling, the
  **multi-dex caveat** (`load app.apk` imports only the primary `classes.dex`;
  extract each `classes*.dex` and load them individually to cover the whole app),
  and the DEX **string vs. symbol address** behavior (`strings` reports the
  string-content address; use the `strings::`-labeled address from `symbols` for
  `xrefs-to`, since the two differ by the variable-length uleb128 prefix).

- `create-struct` explicit-offset layout: pass `--field OFFSET TYPE NAME`
  (repeatable; OFFSET is decimal or `0x` hex) to place fields at exact byte
  offsets. Gaps are auto-padded with undefined bytes — no manual pad fields —
  and overlapping fields are rejected. The original sequential `TYPE NAME`
  form is unchanged.
- `read-pointers` command: read N pointer-sized words at an address and resolve
  each to its function/symbol (respecting endianness and pointer size). Useful for
  vtables, import/jump tables, and RTTI pointer arrays.
- `list-vtable` command: dump a C++ vtable's slots as resolved methods. Accepts a
  symbol name or address; without an explicit count it stops at the next vftable
  symbol or the first non-function pointer, reporting `stopped_reason`.
- `batch-edit-variable` command: rename and/or retype many local variables in a
  single decompiler snapshot and one transaction. Fixes the auto-name renumbering
  that breaks chained single `rename-variable`/`retype-variable` calls, and lets you
  address a variable by its stable `storage` string (e.g. `Stack[-0x18]:4`, `EAX:4`)
  in addition to its current name. Per-item results carry a `verified` read-back flag.
- `rename-variable` command for renaming local variables in decompiler output.
- `list-instances` command to show all running daemon instances, and a
  `stop --all` flag to stop them all at once.
- Global session registry so daemons register/unregister themselves on
  start/stop; `ping` now reports `project_gpr`, `mode`, and `pid`.
- Integration test suite exercising every API domain against a real headless
  Ghidra daemon, plus supporting test fixtures.

### Changed

- Background daemon startup now fails fast with the captured daemon log if
  the subprocess exits early, instead of waiting out the full timeout.

### Fixed

- `find-bytes` tool.
- `xrefs` address resolution now prefers non-external symbols and handles
  thunk functions correctly, fixing inaccurate cross-reference lookups.
- GUI mode project mismatch detection: the daemon passes the `.gpr` path to
  `GhidraRun` so Ghidra opens the requested project on startup instead of
  restoring the last-used one, and `list-project-programs` now detects and
  warns about post-startup project switches in GUI mode.

## [0.1.0] - 2026-06-04

Initial public release.


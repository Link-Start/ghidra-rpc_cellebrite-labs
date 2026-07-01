# Changelog

## [Unreleased]

### Added

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


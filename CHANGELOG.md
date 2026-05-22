# Changelog

All notable changes to `qubithub` (the Python SDK and CLI for QubitHub) are
documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.2.0] — unreleased

First functional release of `qubithub` — the Python SDK and CLI for
[QubitHub](https://qubithub.co), developer infrastructure for quantum computing.

Published as `qubithub` (the package was previously slated for `qubithub-sdk`;
`qubithub-sdk` and `qubithub-cli` are kept as thin alias packages that depend on
`qubithub`). Supersedes the `0.0.1` name-reservation placeholder (2026-03-09),
which has been yanked from PyPI.

### Added

**Python SDK**
- `QubitHubClient` — synchronous client for the QubitHub API (circuits, runs, API keys).
- `AsyncQubitHubClient` — `async`/`await` client for notebook and concurrent workloads.
- Granular exception hierarchy: `QubitHubError`, `APIError`, `AuthenticationError`,
  `AuthorizationError`, `NotFoundError`, `ConflictError`, `ValidationError`,
  `RateLimitError`, `ServerError`, `NetworkError`, `ConfigurationError`.
- Typed Pydantic models — `CircuitCard`, `CircuitDetail`, `CircuitCreateRequest`,
  `RunCreateRequest`, `RunResponse`, `AuthResponse`, `User`. Ships `py.typed`.

**CLI** — `qubithub` (with `qhub` as a short alias)
- `qubithub login` / `logout` / `whoami` — authenticate and inspect the current user.
- `qubithub execute` — resolve a circuit by name, submit a run, and render results in
  the terminal; supports `--framework`, `--backend`, `--shots`, and `--watch`.
- `qubithub clone` — clone a circuit repository; uses `git` when available and falls
  back to an HTTP download (`--no-git`).
- `qubithub circuits list / info / fork / push / init / metadata` — browse, fork,
  publish, scaffold, and inspect circuits.
- `qubithub runs list / info / cancel / stats` — manage execution runs.
- `qubithub keys create / list / revoke` — manage API keys.
- `qubithub config set / get / list / unset` — manage CLI defaults in
  `~/.qubithub/config.toml`.

**Authentication**
- Three methods, checked in order: API key (`X-API-Key`), JWT bearer token
  (`Authorization: Bearer`), and stored credentials from `~/.qubithub/config.toml`.

### Notes
- `qubithub clone` fails loud with an actionable error (and a `--no-git` escape hatch)
  when a circuit's git repository is unreachable through a server-side proxy fault,
  instead of silently degrading to a `.git`-less download.
- Supports Qiskit, Cirq, and PennyLane circuits executed against QubitHub's managed
  simulators.

[0.2.0]: https://github.com/quantputation/qubithub/releases/tag/sdk-v0.2.0

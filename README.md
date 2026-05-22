# qubithub

Python SDK and CLI for [QubitHub](https://qubithub.co) — developer infrastructure for quantum computing.

```bash
pip install qubithub
```

## Quick Start

### CLI

```bash
# Authenticate
qubithub login

# Execute a circuit
qubithub execute QubitHub/bell-state --watch

# Browse circuits
qubithub circuits list --framework qiskit
qubithub circuits info QubitHub/bell-state

# Clone, edit, push (uses git when available; --no-git for HTTP fallback)
qubithub clone qubithub/bell-state
qubithub circuits push ./bell-state --create

# Manage config
qubithub config set default_shots 4096
qubithub config list
```

### Python SDK

```python
from qubithub import QubitHubClient, RunCreateRequest

client = QubitHubClient(token="your-jwt-token")

# Resolve a circuit by name
circuit = client.resolve_circuit("QubitHub/bell-state")

# Submit an execution run
run = client.create_run(RunCreateRequest(
    circuit_id=str(circuit.id),
    backend_name="qiskit_aer",
    shots=1024,
))
print(f"Run {run.id}: {run.status}")
```

### Async Client

```python
import asyncio
from qubithub import AsyncQubitHubClient, RunCreateRequest

async def main():
    async with AsyncQubitHubClient(token="your-jwt-token") as client:
        circuit = await client.resolve_circuit("QubitHub/bell-state")
        run = await client.create_run(RunCreateRequest(
            circuit_id=str(circuit.id),
            backend_name="qiskit_aer",
            shots=1024,
        ))
        print(f"Run {run.id}: {run.status}")

asyncio.run(main())
```

## Authentication

Three methods, checked in order:

| Method | Flag / Env Var | Header |
|--------|---------------|--------|
| API key | `--api-key` / `QUBITHUB_API_KEY` | `X-API-Key` |
| JWT token | `--token` / `QUBITHUB_TOKEN` | `Authorization: Bearer` |
| Stored credentials | Auto from `~/.qubithub/config.toml` | — |

```bash
# Interactive login (stores JWT + refresh token)
qubithub login

# API key (for CI/scripts)
qubithub login --api-key qak_your_key_here

# Direct token
qubithub login --token eyJhbGci...
```

## CLI Commands

```
qubithub login              Log in (interactive, --api-key, or --token)
qubithub logout             Clear stored credentials
qubithub whoami             Show current user

qubithub clone              Clone a circuit (git when available, HTTP fallback)
qubithub execute            Execute a circuit (flagship command)

qubithub circuits list      List / search circuits
qubithub circuits info      Show circuit details
qubithub circuits fork      Fork a circuit
qubithub circuits push      Push local directory to QubitHub
qubithub circuits init      Scaffold a new circuit from template

qubithub runs list          List your runs
qubithub runs info          Show run details + results
qubithub runs cancel        Cancel a run
qubithub runs stats         Show run statistics

qubithub keys create        Create an API key
qubithub keys list          List API keys
qubithub keys revoke        Revoke an API key

qubithub config set         Set a CLI default
qubithub config get         Get a CLI default
qubithub config list        List all CLI defaults
qubithub config unset       Remove a CLI default
```

Global options: `--base-url`, `--api-key`, `--token`, `--format (rich|json)`.

## Error Handling

The SDK provides a granular exception hierarchy:

```python
from qubithub import (
    QubitHubError,       # Base — catch everything
    APIError,            # Any HTTP 4xx/5xx
    AuthenticationError, # 401
    AuthorizationError,  # 403
    NotFoundError,       # 404
    ConflictError,       # 409
    ValidationError,     # 422
    RateLimitError,      # 429 (includes retry_after)
    ServerError,         # 5xx
    NetworkError,        # Connection/timeout failures
    ConfigurationError,  # Bad local config
)

try:
    circuit = client.resolve_circuit("user/nonexistent")
except NotFoundError:
    print("Circuit not found")
except QubitHubError as e:
    print(f"Error: {e.message}")
```

## Configuration

CLI defaults are stored in `~/.qubithub/config.toml`:

```toml
[defaults]
default_framework = "pennylane"
default_shots = 4096
default_format = "json"
```

## Requirements

- Python 3.10+
- Dependencies: `httpx`, `pydantic`, `typer`, `rich`, `tomli-w`

## License

Apache 2.0 — see [LICENSE](LICENSE).

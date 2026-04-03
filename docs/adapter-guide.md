# Adapter Development Guide

This guide walks you through creating a new robot adapter for Mosoro.

## Requirements

- Python 3.9+
- `mosoro-core>=1.0.0`

## Community Adapters

Pre-built adapters are available in [mosoro-adapters-community](https://github.com/mosoroio/mosoro-adapters-community):

| Vendor | Package | Install |
|--------|---------|---------|
| Locus Robotics | `mosoro-adapter-locus` | `pip install mosoro-adapter-locus` |
| MiR | `mosoro-adapter-mir` | `pip install mosoro-adapter-mir` |
| Fetch Robotics | `mosoro-adapter-fetch` | `pip install mosoro-adapter-fetch` |
| Geek+ | `mosoro-adapter-geekplus` | `pip install mosoro-adapter-geekplus` |
| Stretch | `mosoro-adapter-stretch` | `pip install mosoro-adapter-stretch` |

### Locus Robotics

The Locus adapter connects to the Locus REST API and normalizes robot state into the Mosoro standard schema.

```bash
pip install mosoro-adapters-community
```

**Configuration — environment variables (recommended):**

| Variable | Description | Default |
|---|---|---|
| `LOCUS_HOST` | Hostname or IP of the Locus API server | `localhost` |
| `LOCUS_PORT` | Port of the Locus API server | `8080` |
| `LOCUS_API_KEY` | API key for authentication | _(none)_ |

```bash
export LOCUS_HOST=192.168.1.50
export LOCUS_PORT=8080
export LOCUS_API_KEY=your-api-key
```

**Configuration — YAML (overrides env vars):**

```yaml
robot_id: locus-001
vendor: locus
api_base_url: http://192.168.1.50:8080/api
api_key: your-api-key
```

> **Note:** `api_base_url` in the YAML takes precedence over `LOCUS_HOST`/`LOCUS_PORT` env vars. If omitted, env vars are used. If neither is set, defaults to `http://localhost:8080/api`.

Supported commands: `move_to`, `pause`, `resume`.

## Overview

An adapter translates between a robot vendor's native API and the Mosoro standard message format (MosoroMessage).

## Step 1: Subclass BaseMosoroAdapter

```python
from agents.adapters.base_adapter import BaseMosoroAdapter

class MyRobotAdapter(BaseMosoroAdapter):
    async def connect(self):
        # Connect to your robot's API
        pass

    async def disconnect(self):
        # Clean up connections
        pass

    async def poll_status(self):
        # Fetch status and return as MosoroPayload dict
        pass

    async def send_command(self, command):
        # Translate and send command to robot
        pass
```

## Step 2: Create a Config File

```yaml
adapter:
  type: my_robot
  vendor: my_vendor
  robot_id: my-robot-001

connection:
  base_url: http://robot-api:8080
  timeout: 10
```

## Step 3: Test Your Adapter

Use the shared test harness from `mosoro-adapters-community`:

```bash
pytest tests/test_adapter_contract.py
```

## Step 4: Submit

- **Community adapters**: Submit a PR to [mosoro-adapters-community](https://github.com/mosoroio/mosoro-adapters-community)
- **Core adapters**: Submit a PR to [mosoro-core](https://github.com/mosoroio/mosoro-core)

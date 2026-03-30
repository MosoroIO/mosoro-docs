# Adapter Development Guide

This guide walks you through creating a new robot adapter for Mosoro.

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

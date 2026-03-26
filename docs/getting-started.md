# Getting Started

## Prerequisites

- Python 3.11+
- Docker and Docker Compose v2
- An MQTT broker (Mosquitto included)

## Installation

### Option 1: Docker Compose (Recommended)

```bash
git clone https://github.com/mosoro/mosoro-core.git
cd mosoro-core
docker compose -f docker/docker-compose.prod.yml up --build
```

### Option 2: pip install

```bash
pip install mosoro-core[all]
```

## Your First Fleet

1. Start the MQTT broker and gateway
2. Configure an agent for your robot
3. Start the agent
4. Open the API at http://localhost:8000/docs

See [Architecture](architecture.md) for how the components fit together.

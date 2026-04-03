# Getting Started

## Prerequisites

- Python 3.9+ (3.11+ recommended for production deployments)
- Docker and Docker Compose v2
- An MQTT broker (Mosquitto included)

## Installation

### Option 1: Docker Compose (Recommended)

```bash
git clone https://github.com/mosoroio/mosoro-core.git
cd mosoro-core
docker compose -f docker/docker-compose.prod.yml up --build
```

This starts the full Mosoro stack including the MQTT broker, gateway, API server, and the React frontend dashboard.

### Option 2: pip install

```bash
pip install mosoro-core[all]
```

## Your First Fleet

1. Start the MQTT broker and gateway
2. Configure an agent for your robot
3. Start the agent
4. Open the API at http://localhost:8000/docs
5. Open the **Dashboard** at http://localhost:3000 to visualize your fleet in real time

The frontend dashboard provides a live fleet overview, robot status monitoring, task assignment, map visualization, and an events feed — all powered by WebSocket connections to the API.

## What's Running

| Service | URL | Description |
|---------|-----|-------------|
| API Server | http://localhost:8000 | REST API + WebSocket backend |
| API Docs | http://localhost:8000/docs | Interactive OpenAPI documentation |
| Dashboard | http://localhost:3000 | React + Vite frontend for fleet management |
| MQTT Broker | localhost:8883 | Eclipse Mosquitto (TLS) |

## Next Steps

- [Architecture](architecture.md) — Learn how the components fit together
- [Adapter Guide](https://github.com/mosoroio/mosoro-adapters-community) — Add support for your robot vendor
- [Python SDK](https://github.com/mosoroio/mosoro-py) — Integrate Mosoro into your applications

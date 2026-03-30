# Mosoro

**The neutral bridge for multi-vendor warehouse robot fleets.**

Mosoro is an open-source semantic interoperability gateway that enables mixed-vendor robot fleets to communicate through a unified MQTT-based protocol. It provides deterministic protocol translation, real-time routing, and a unified API and dashboard — without the complexity of a full HFMS platform.

## Key Features

- **Vendor-Neutral Protocol** — Normalize messages from any robot vendor into a common schema
- **MQTT-Based Architecture** — Real-time, event-driven communication
- **Edge Agent Framework** — Lightweight adapters for each robot type
- **React Dashboard** — Real-time fleet visualization, task assignment, and monitoring
- **Rules Engine** — Automated responses to fleet events
- **Plugin System** — Extend the core with custom modules
- **Zero-Trust Security** — mTLS, JWT, and ACL-based access control

## Quick Start

```bash
pip install mosoro-core[all]
```

See [Getting Started](getting-started.md) for the full setup guide.

## Documentation

- **[Getting Started](getting-started.md)** — Prerequisites, installation, and your first fleet
- **[Architecture](architecture.md)** — System design, components, MQTT topics, and data flow
- **[Adapter Guide](https://github.com/mosoroio/mosoro-adapters-community)** — Community-contributed robot adapters and how to create your own
- **[Python SDK](https://github.com/mosoroio/mosoro-py)** — Official Python client library for the Mosoro API

## Repositories

| Repository | Description |
|------------|-------------|
| [mosoro-core](https://github.com/mosoroio/mosoro-core) | Gateway, API, agents, frontend dashboard |
| [mosoro-adapters-community](https://github.com/mosoroio/mosoro-adapters-community) | Community robot adapters (Locus, Stretch, Geekplus, MiR, Fetch) |
| [mosoro-py](https://github.com/mosoroio/mosoro-py) | Python SDK for the Mosoro API |
| [mosoro-docs](https://github.com/mosoroio/mosoro-docs) | This documentation site |

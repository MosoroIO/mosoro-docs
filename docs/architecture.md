# Architecture

Mosoro is a lightweight, open-core middleware platform that bridges multi-vendor warehouse robot fleets through a unified MQTT-based protocol. This document describes the high-level architecture, component responsibilities, and data flow.

## High-Level Architecture

```
┌──────────────────────────────────────────────────────────────────────┐
│                          Mosoro Stack                                │
│                                                                      │
│  ┌────────────┐   ┌──────────────────────┐   ┌───────────────────┐  │
│  │ Edge Agents │   │   Central Gateway    │   │   Unified API     │  │
│  │ (per robot) │   │ (MQTT + Rules Engine)│   │  (REST + WS)      │  │
│  ├────────────┤   ├──────────────────────┤   ├───────────────────┤  │
│  │ Locus      │──▶│                      │──▶│ GET  /robots      │  │
│  │ Stretch    │──▶│  Topic routing        │   │ GET  /robots/{id} │  │
│  │ Geekplus   │──▶│  Rules engine         │   │ POST /tasks       │  │
│  │ MiR        │──▶│  State management     │   │ GET  /events      │  │
│  │ Fetch      │──▶│  Traffic control      │   │ WS   /ws/fleet    │  │
│  │ [custom]   │──▶│                      │──▶│                   │  │
│  └────────────┘   └──────────┬───────────┘   └────────┬──────────┘  │
│         │                    │                         │             │
│         │         ┌──────────▼───────────┐   ┌────────▼──────────┐  │
│         │         │  Eclipse Mosquitto   │   │  React + Vite     │  │
│         └────────▶│  MQTT Broker         │   │  Frontend         │  │
│                   │  Port 8883 (TLS)     │   │  Port 3000        │  │
│                   └──────────────────────┘   └───────────────────┘  │
└──────────────────────────────────────────────────────────────────────┘
```

## Components

### Edge Agents

Each physical robot runs a lightweight **edge agent** that translates vendor-specific APIs into the common `MosoroMessage` schema. Agents are deployed as Docker containers alongside the robots they manage.

- **Location:** `mosoro-core/agents/`
- **Responsibilities:** Poll robot status, translate to `MosoroMessage`, publish to MQTT, receive and execute commands
- **Adapters:** Community adapters live in [mosoro-adapters-community](https://github.com/mosoroio/mosoro-adapters-community) and are auto-discovered via Python entry points

### Central Gateway

The gateway is the brain of the Mosoro stack. It subscribes to all agent topics, applies routing rules, manages fleet state, and handles traffic coordination.

- **Location:** `mosoro-core/gateway/`
- **Responsibilities:** Message routing, rules engine evaluation, in-memory state store with TTL, traffic yield/update broadcasting
- **Configuration:** Rules defined in `rules.yaml` (YAML-based DSL)

### Unified API

A FastAPI-based REST API and WebSocket server that exposes fleet data to external consumers and the frontend dashboard.

- **Location:** `mosoro-core/api/`
- **Endpoints:**
    - `GET /robots` — List all robots with current status
    - `GET /robots/{id}` — Get detailed robot information
    - `POST /tasks` — Assign a task to a robot
    - `GET /events` — Retrieve recent fleet events
    - `WS /ws/fleet` — Real-time WebSocket stream of fleet updates
    - `POST /auth/token` — JWT authentication

### Frontend Dashboard

A React + Vite single-page application that provides real-time fleet visualization.

- **Location:** `mosoro-core/frontend/`
- **Features:** Fleet overview with summary cards, robot list with filtering, interactive map view, task assignment interface, live events feed
- **Tech:** React 18, TypeScript, Vite, TailwindCSS, WebSocket for live updates

### MQTT Broker

Eclipse Mosquitto 2.x serves as the message backbone. All inter-component communication flows through MQTT with TLS encryption and ACL-based access control.

- **Location:** `mosoro-core/docker/mosquitto/`
- **Security:** mTLS on port 8883, per-agent ACLs, password authentication

## Data Flow

1. **Robot → Agent:** The edge agent polls the robot's vendor API (REST, ROS 2 bridge, etc.) on a configurable interval
2. **Agent → MQTT:** The agent publishes a `MosoroMessage` to `mosoro/v1/agents/{robot_id}/status` (QoS 1)
3. **MQTT → Gateway:** The gateway subscribes to `mosoro/v1/agents/+/status` and updates its in-memory state store
4. **Gateway → Rules:** The rules engine evaluates incoming messages against configured rules and may trigger commands or alerts
5. **MQTT → API:** The API server subscribes to relevant topics and pushes updates to connected WebSocket clients
6. **API → Frontend:** The React dashboard connects via WebSocket at `/ws/fleet` and renders live fleet data

## MQTT Topic Hierarchy

```
mosoro/v1/agents/{robot_id}/birth       ← Agent connection announcement
mosoro/v1/agents/{robot_id}/lwt         ← Last Will & Testament (disconnect detection)
mosoro/v1/agents/{robot_id}/status      ← Periodic status updates (QoS 1)
mosoro/v1/agents/{robot_id}/events      ← Task completions, errors, alerts
mosoro/v1/agents/{robot_id}/commands    ← Gateway → agent commands (QoS 1)
mosoro/v1/traffic/yield                 ← Broadcast traffic yield requests
mosoro/v1/traffic/update                ← Broadcast traffic state updates
mosoro/v1/admin/rules                   ← Admin-only rules management
```

### Topic Design Principles

- **Namespaced:** All topics begin with `mosoro/v1/` for versioning
- **Per-agent isolation:** Each robot publishes only to its own `{robot_id}` subtree
- **QoS 1 for critical data:** Status and command messages use at-least-once delivery
- **Wildcard subscriptions:** The gateway uses `+` wildcards to subscribe to all agents efficiently

## Security Architecture

Mosoro follows a zero-trust model designed for OT/warehouse environments:

- **mTLS** — Mutual TLS between agents and the MQTT broker (both sides present certificates)
- **TLS 1.3** — Enforced on MQTT port 8883
- **JWT** — All REST API calls require a valid bearer token
- **ACLs** — Each agent can only publish to its own topic subtree
- **Non-root containers** — All services run as non-root user `mosoro`

## Plugin System

Mosoro Core supports extensions via Python entry points:

- **API Routers** — Additional REST endpoints mounted under `/plugins/{name}/`
- **MQTT Topics** — Additional topic subscriptions
- **Gateway Hooks** — Event handlers for message processing

See the [mosoro-core README](https://github.com/mosoroio/mosoro-core#plugin-system) for plugin development details.

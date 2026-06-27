# EDGE_CASE

**Break it before your users do.**

EDGE_CASE is a chaos engineering and resilience testing tool that allows developers, solo builders, and teams to simulate real system failures, observe their impact in real time, and understand how their application behaves under stress before it reaches production users.

## Why EDGE_CASE Exists

Modern applications are increasingly built:

- by solo entrepreneurs shipping fast
- using AI-generated code
- with multiple external dependencies

In this environment, systems often work in ideal conditions but fail unpredictably under stress.

EDGE_CASE is built to answer one critical question:

**What happens when something breaks?**

Instead of guessing, EDGE_CASE lets you:

- simulate failure scenarios
- observe real impact on your system
- understand the blast radius instantly
- take action before users are affected

## Who This Is For

### Solo Builders and Founders

If you are building quickly and deploying frequently, EDGE_CASE helps you validate that your system will not break under real-world conditions.

### Developers Using AI-Generated Code

AI tools can generate working code, but not always resilient systems. EDGE_CASE helps ensure that your application behaves correctly when dependencies fail or degrade.

### Teams Preparing for Scale

Before increasing traffic or launching features, EDGE_CASE allows you to stress test critical paths and identify weak points early.

## What EDGE_CASE Does

EDGE_CASE provides a full loop from failure simulation to action:

1. Simulate failures
2. Observe system behavior
3. Understand impact in plain English
4. Get actionable next steps

## Core Features

### Failure Simulation

Trigger real system-level failures in a controlled environment:

- Database outage (`DB Down`)
- Latency injection (`Latency Spike`)
- Traffic surge (`Request Flood`)

These are executed using container-level controls and network manipulation.

### Real-Time System Visibility

EDGE_CASE continuously monitors the system during a drill and shows:

- service status (application and database)
- success rate and error count
- response latency (`p95`)
- time of first failure
- event timeline

All metrics are derived from real responses, not simulated values.

### MCP-Based Control Plane

EDGE_CASE uses a control layer to execute and track system changes:

- container stop and restart
- latency injection via proxy
- load generation

This makes the system transparent and reproducible.

### Live Interpretation Layer

EDGE_CASE translates system signals into human-readable insights.

Instead of raw logs or dashboards, it explains:

- what is happening
- why it is happening
- how it affects users

This makes it accessible even to non-expert users.

### Technical Verdict

After each drill, EDGE_CASE provides a clear diagnostic view:

- what failed
- how severe the failure was
- supporting evidence (metrics and timeline)
- likely cause

### Action Plan for Recovery and Improvement

EDGE_CASE generates a structured next-step plan:

- what to fix immediately
- what to improve next
- how to make the system more resilient

These steps are written in a way that can be directly used with AI tools or engineering workflows.

## Example Scenarios

### Database Outage

Simulate the database going offline and observe:

- checkout failures
- application degradation
- Full loss of functionality in critical flows

### Latency Injection

Introduce delay in database communication and observe:

- increased response time
- degraded user experience
- potential cascading failures

### Traffic Surge

Apply load to the system and observe:

- system saturation
- latency spikes
- failure thresholds

## How to Run

### Start the System

```bash
podman compose up --build
```

### Verify Services

```bash
curl http://localhost:5001/health
curl -X POST http://localhost:5001/checkout -H "Content-Type: application/json" -d '{}'
```

### Run Backend

```bash
cd backend
uvicorn main:app --reload
```

### Run MCP Server

```bash
cd mcp-server
uvicorn server:app --host 127.0.0.1 --port 9100
```

### Open Frontend

```bash
python -m http.server 5500 --directory frontend
```

Then open http://127.0.0.1:5500 in your browser.

## Architecture Overview

- Frontend: HTML, JavaScript, CSS
- Backend: FastAPI
- Control Plane: MCP server (Podman + Toxiproxy)
- Demo Application: Flask + Postgres
- Infrastructure: container-based environment

## What Makes EDGE_CASE Different

EDGE_CASE is not just a monitoring tool.

It combines:

- failure simulation
- real-time observability
- interpretation
- action planning

This creates a complete workflow from:

**failure -> understanding -> resolution**

## Future Direction

- deeper AI-assisted failure analysis
- automated resilience scoring
- integration with CI/CD pipelines
- support for additional failure types and environments

## Summary

EDGE_CASE helps you answer:

- What breaks when my system is under stress?
- How quickly does it fail?
- What do I need to fix before users are impacted?

Instead of discovering failures in production, EDGE_CASE lets you discover them safely and early.

# Model Selection Rationale — Edge_Case

Edge_Case is a chaos-engineering and incident-response system. It injects controlled
failures into a live application, observes the blast radius, then interprets the
evidence and produces a recovery plan. Five distinct LLM-driven tasks power the
interpretation and planning layer.

## Current architecture: fully local

All LLM tasks run **locally on Ollama (`llama3`)** via `http://127.0.0.1:11434`, with
deterministic rule-based fallbacks if the model is unavailable.

| # | Task (function) | Model | Why this model |
|---|-----------------|-------|----------------|
| 1 | Classify fear → drill type (`classify_fear_with_ollama`) | llama3 (local) | Short structured classification; cheap and instant |
| 2 | Expected-impact text (`generate_expected_impact_with_ollama`) | llama3 (local) | One-sentence generation; no frontier needed |
| 3 | Resilience verdict (`generate_ollama_verdict`) | llama3 (local) | Reasons over metrics/logs that may contain sensitive infra data |
| 4 | Action plan (`generate_ollama_action_plan`) | llama3 (local) | Structured remediation list; bounded output |
| 5 | Live narration (`generate_ollama_live_interpretation`) | llama3 (local) | High-frequency dashboard text; latency-sensitive |

## Cost / latency / quality justification

- **Cost:** $0 — every inference runs on-device. No per-token API spend even when a
  drill triggers dozens of live-narration calls.
- **Privacy:** Drill evidence (logs, error traces, metrics) can contain sensitive
  infrastructure detail. Keeping all inference local means **no system telemetry ever
  leaves the machine** — a hard requirement for the enterprise/SRE use case.
- **Latency:** Local inference avoids network round-trips, which matters most for the
  live-narration path (#5) that updates the dashboard during an active drill.
- **Resilience (fitting for a chaos tool):** Every LLM call has a deterministic
  rule-based fallback (`classify_fear_text`, `build_fallback_action_plan`,
  per-drill evidence templates). The product stays fully functional even if the model
  is down — the system practices the resilience it preaches.

## Quality tradeoff (acknowledged)

Local `llama3` is weaker at nuanced reasoning than a frontier model. We accept this
tradeoff because: (a) outputs are tightly constrained to JSON schemas with grounding
rules ("use only the provided evidence; do not invent facts"), and (b) the rule-based
fallbacks guarantee a sane floor on output quality.

## Possible future routing (not implemented)

A hybrid path would route the two quality-critical reasoning tasks (#3 verdict, #4
action plan) to a frontier model while keeping the high-volume, privacy-sensitive
tasks (#1, #2, #5) local. This is documented as a roadmap option; the current system
is intentionally all-local for zero-cost, zero-egress operation.

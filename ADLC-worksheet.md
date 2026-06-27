# ADLC Worksheet — Edge_Case

Agent Development Life Cycle: scope -> design -> build -> evaluate -> deploy -> observe -> iterate.

## 1. Scope
**Problem:** Teams ship fast (often with AI-generated code) but rarely test how their
system behaves when dependencies fail. Failures are discovered in production, by users.
**User:** Solo founders, small eng teams, and developers shipping quickly who need
resilience validation without a dedicated SRE team.
**Success:** Given a stated "fear," Edge_Case runs a controlled drill, shows live
impact, and returns a grounded diagnosis + prioritized recovery plan.

## 2. Design
**Components:**
- Frontend (HTML/JS/CSS dashboard)
- Backend (FastAPI) — orchestrates drills, interpretation, action planning
- MCP control plane (Podman + Toxiproxy) — injects real failures
- Demo app (Flask + PostgreSQL) — the target under test

**Failure types:** db_down, latency_spike, request_flood, credential_exposure,
pii_exposure, dependency_api_failure, ai_risk_suite.

**Model routing:** all LLM tasks local on Ollama `llama3` with rule-based fallbacks.
See `model-rationale.md`.

## 3. Build
- [x] MCP server injects failures via Podman/Toxiproxy
- [x] Backend probes live endpoints and captures real metrics (success rate, p95, errors)
- [x] 5 LLM tasks: classify, expected-impact, verdict, action-plan, live-narration
- [x] Deterministic fallbacks for every LLM call
- [x] Remediation apply + verification re-test flow

## 4. Evaluate
**Test scenarios:**
1. `db_down` drill -> verdict identifies hard DB dependency + missing fallback; action
   plan recommends retry/circuit-breaker/graceful-degradation.
2. `latency_spike` drill -> verdict flags timeout exposure; plan recommends bounded
   timeouts + isolation.
3. Model-down case -> rule-based fallback still returns a coherent verdict/plan.

**Assertion style:** verdict must reference user-facing impact (not just "db stopped"),
and must stay grounded in provided evidence.

## 5. Deploy
- Public GitHub repo (Edge_Case) with README + MIT license
- `podman compose up --build` brings up the full stack
- Health verified via `curl http://localhost:5001/health`

## 6. Observe
- Live drill dashboard shows real-time status, success rate, p95 latency, timeline
- Backend logs every probe, MCP call, and LLM verdict/fallback decision

## 7. Iterate
- Optional hybrid model routing for the two reasoning-heavy tasks (verdict, action plan)
- Expand drill library (memory pressure, disk-full, partial network partition)
- Add an eval harness (evals.json) comparing verdict quality with vs. without grounding rules
- Track resilience trendlines across repeated drills

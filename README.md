# EXA Agentic Workflow — Starter Repository

This repository contains the three artifacts the EXA Agentic Workflow research identified as directly executable or validatable, rather than reference documentation to be read and manually implemented. They are reproduced here **verbatim** from the ratified source deliverables, at the versions ratified 2026-08-11.

> Source project: `EXA_AgenticWorkflow_ProjectBrief_v1.0.md`. All nine steps of the research are ratified. See `EXA_AgenticWorkflow_Step6_SynthesisAssembly_v1_1.md` §2.3 for the origin of this repository's scope.

## What's here

| File | What it is | Ratified in |
|---|---|---|
| `.exa/workspace-boundary.yml` | **Layer 0** — a local pre-commit/IDE boundary config that blocks and reports autonomous agent writes to constraint-library paths | Step 4 §4.0, D-066 |
| `.exa/registry-check.yml` | **Registry CI enforcement config** — the required shape for the Cross-Enterprise Semantic State Registry's duplicate-detection check | Step 8 §1.3a |
| `.exa/schemas/EXA_Ingestion_Adapter_Schema.json` | **The ingestion JSON Schema** — the one normalized event shape every telemetry adapter must produce before an event reaches the aggregated override log | Step 8 §2.3a |
| `docs/adapter-validation-rules.md` | The validation-rule table the ingestion schema is checked against, and the migration note for pre-existing adapters | Step 8 §2.3a |
| `docs/layer3-provenance-verification.md` | What Layer 3's constraint provenance check actually validates, its custody controls, and the Deploy-Time Heartbeat that closes its deploy-time residual | Step 4 §4.3 |

## What's deliberately not here

**No Consumer Register schema.** Step 8 §2.4b formalizes the Burn-In Calibration Phase as shared infrastructure five mechanisms register against, but the ruling that created it (Q-051/D-084) explicitly declined to specify a payload schema or CI pipeline syntax for how a registration is technically transmitted — that is implementation detail below the level this research specifies anywhere in Step 8, consistent with the same discipline that has kept the clustering algorithm and every derived threshold unset throughout the research. **If you build a Consumer Register integration, that schema is yours to design against your own telemetry stack — this repository does not prescribe one, and treating an absent schema here as an oversight rather than a deliberate boundary would misread the ruling.**

**No variance-ceiling figures, no anomaly multiples, no clustering thresholds.** Every numeric parameter the Burn-In Calibration Phase derives (Step 8 §2.4a) is intentionally left unset in the source research and is therefore absent here too. Run your own Burn-In shadow-ingestion window; do not copy a number from anywhere, including this repository.

**No compensation figures, no funding-form selection, no HR banding numbers.** Enterprise-specific by design (Q-030c, Q-007).

## Before you deploy any of this

1. **Read the "what this is not" section of each file.** `workspace-boundary.yml` states inline that it does not close Disqualifier X-6 — it is advisory, and pairs with Layer 3's provenance check as the actual enforcement boundary. Deploying it alone and treating the constraint library as protected is the single most-named failure mode across the source research.
2. **`${...}` placeholders are not defaults.** `EXA_REGISTRY_URL`, `TARGET.exa_constraint_id`, `WORKSPACE.squad_id`, and similar are resolved by your own CI/telemetry infrastructure. None of them ship with a working value.
3. **The registry check and the ingestion schema both fail closed on ambiguity, and are meant to.** An adapter must never infer or backfill `exa_constraint_id` — see `docs/adapter-validation-rules.md`'s first rule. If you find yourself writing code to guess at a missing identity field to make validation pass, stop; that is the exact Silent Render failure these artifacts exist to prevent, reproduced at the telemetry layer.
4. **This is a starter, not a platform.** These three artifacts are the seed the source research identified as directly portable. Everything else in the framework — the Amendment Protocol, the Cultural Veto, the Specification Scorecard, the full constraint-block format — is specification you implement against, not code you copy.

## Two open items outliving the research

The source research closed with two explicitly non-blocking open questions, neither resolved by anything in this repository:

- **Q-023** — whether GM-6 (Telemetry Integrity Rate) is part of the ratified six-metric Specification Scorecard structure or a Step 5–local addition.
- **Q-050** — whether the Executive Summary should assert formal alignment with external regulatory frameworks (NIST CAISI, OWASP, ISO/IEC 42001).

Neither affects anything in this repository. Both remain the framework owner's to rule on, through the Amendment Protocol, as post-ratification activity.

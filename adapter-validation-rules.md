# Adapter validation rules — `EXA_Ingestion_Adapter_Schema` v1.1

**Source:** `EXA_AgenticWorkflow_Step8_RatifiedSourceImplementationDetail_v1.4.md` §2.3a
**Governs:** `.exa/schemas/EXA_Ingestion_Adapter_Schema.json`

Every adapter that writes to the aggregated override log (Step 4 §4.4, Step 5 §6.1) is validated against this schema and these rules before an event is admitted. Schema validation must run **before** an event reaches the aggregated log, never after.

| Validation rule | If violated | Consequence | Severity |
|---|---|---|---|
| `exa_constraint_id` must resolve to an existing, currently-certified ratified object of any class | The adapter infers, guesses, or backfills an ID for an event that did not carry a resolvable one | Manufactures a false Structural Signal match — a Silent Render at the telemetry layer | **CRITICAL** |
| `exa_constraint_class` must be present and must be the class the resolved `exa_constraint_id` actually belongs to | An adapter defaults every event to `state_constraint`, or omits the field where the schema is leniently enforced | Compositional Drift and the Structural Signal cluster across object classes that should never be compared. Silent, and it corrupts the trigger rather than disabling it, which is worse | **CRITICAL** |
| `exa_constraint_class` must never be substituted for `exa_constraint_id` as a join key, and no parallel ID field may be introduced alongside it | An implementer reads the class field as license to key generative events on `envelope_id` after all, treating class as a namespace selector | Reintroduces the exact multi-primary-key fracture the Q-034 ruling rejected, under cover of a change that appears to follow it. The Structural Signal stops firing across the estate and presents as a quiet, well-specified portfolio | **CRITICAL** |
| `squad_id` must identify a distinct submitting squad, never a shared, default, or pipeline-level placeholder value | An adapter defaults every event from one CI system to a single `squad_id` regardless of which squad actually triggered it | The rolling-window "three distinct squads" threshold (Step 5 §6.2) fires falsely on a single squad's repeated activity, or never fires because events from genuinely distinct squads collapse into one counted squad | **HIGH** |
| `event_type` must be one of the five enumerated values | An adapter passes through a native event type the schema doesn't recognize | The event is rejected at ingestion rather than silently admitted with an unrecognized type the trigger-evaluation logic cannot interpret | **HIGH** |
| Schema validation itself must run before an event reaches the aggregated log, never after | An adapter writes directly to the log and validates asynchronously, or not at all | A malformed event pollutes the aggregated log before anyone notices | **CRITICAL** |

## Migration note

`exa_constraint_class` is a **required** field in v1.1, so every v1.0 adapter fails validation until it appends the class string. This is a ten-minute adapter change — a static string per adapter, since any single adapter almost always emits for one object class.

**Adapters must not be granted a grace period in which the field is optional.** An optional discriminator defaults silently, and a silently defaulted class is exactly the CRITICAL corruption named above. Fail closed, fix the adapter.

## Source-type normalization

| Source type | Native event shape | Normalization requirement |
|---|---|---|
| **Jira-based override filing** | Free-text ticket, manually filed by a squad member | `exa.constraint.id` must be a required, structured field on the filing template — not extracted from free text. A ticket filed without a resolvable constraint ID is not ingestible and is returned at filing |
| **CI/CD pipeline rejection** | Structured, but pipeline-specific | Per-pipeline adapter maps the pipeline's native rejection schema to the normalized event shape. The adapter is a translation layer only — it may not alter, infer, or backfill an `exa.constraint.id` the pipeline did not itself emit. A pipeline rejection with no resolvable constraint ID is logged as an ingestion failure, not silently dropped and not guessed at |
| **Agent gateway telemetry** | Already normalized — `gen_ai.*` spans with W3C Baggage propagation | No adapter needed |

> **Consequence if an adapter is permitted to infer or backfill a missing constraint ID: CRITICAL.** An adapter guessing which constraint an ungoverned event belongs to manufactures Structural Signal matches that never happened, and the amendment queue fills with amendments responding to a pattern the pipeline invented rather than one the estate actually produced.

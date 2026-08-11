# Layer 3 — Constraint provenance verification

**Source:** `EXA_AgenticWorkflow_Step4_AgenticToolchain_v1.5.md` §4.3
**Ratified:** F-05/D-067 (2026-08-10); custody controls added D-072 (2026-08-10); Step 4 v1.5 ratified 2026-08-11

Layer 0's `workspace-boundary.yml` is advisory. **This is the actual enforcement boundary** for the constraint library's write authority, and it is the reason Layer 0 can legitimately be advisory without leaving the constraint library unguarded.

## The check

Every constraint-library file in a changeset is hashed and checked against its Constraint Set Certification Record (A-26). A content hash that does not match the certification record for that constraint's current version **fails the build** — regardless of whether the file still parses, still validates against schema, and still resolves its bindings.

**Why this is a distinct check from every other Layer 3 check.** Every other Layer 3 check validates *structure*: does it parse, does it resolve, does it reconcile. None validates *provenance* — whether the content arrived through ratification. A constraint block whose `AGENT MUST NOT` clause has been weakened, but which remains structurally well-formed, passes every structural check cleanly. Only the provenance hash catches it.

## Custody controls (D-072, added 2026-08-10)

A provenance check is only as trustworthy as the pipeline that runs it. Three controls protect the check's own integrity:

1. **Organization-level required status check** — not repository-scoped, so no individual repository admin can disable it for their own repository.
2. **Protected-path ownership** on the CI configuration itself and on the constraint-library path — the pipeline definition that runs this check cannot be edited by the same actors the check exists to constrain.
3. **A-26 write authority scoped exclusively to the ratifying identity** — only the account that performed ratification may append to the Constraint Set Certification Record the hash check validates against.

**The residual this does not close, stated rather than left implicit:** an actor holding the ratifying identity itself remains outside this control's reach by construction. That residual is a Deploy-Time Heartbeat concern (see below), not a Layer 3 concern.

## Deploy-Time Heartbeat (D-083, added 2026-08-11)

Layer 3's custody controls bind at merge time. A surface deployed through a break-glass procedure or a legacy pipeline that bypasses branch protection can reach production having never passed this check at all, and produces no signal that it lacks one.

On successful Layer 3 certification, the CI pipeline emits a signed `deploy_certified` span to the aggregated telemetry sink, keyed to the surface ID and deployed version. A continuous rule watches for any surface beginning to emit production telemetry with no corresponding heartbeat, and fires an immediate CRITICAL alert to the Enterprise Experience Architect and Ops.

**This is alert-only, not fail-closed** — ruled explicitly, upholding the same non-fail-closed telemetry-absence position (D-044, D-074) rather than reversing it a third time. An absent heartbeat can mean an uncertified deployment; it can equally mean a telemetry sink lagging or a version-string mismatch. Disposition of the alert is a human decision, not a system-triggered halt.

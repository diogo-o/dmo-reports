# DMO Reports — Index

This index is the entry point for implementation-state material.

## 1. Plans

Path: `plans/`

Active implementation sequencing and delivery plans.

- `plans/IMPLEMENTATION_ROADMAP_DRAFT.md` — current working roadmap. It is intentionally a draft and will be refined from current authority, current source evidence, verified database state, and accepted implementation results.

## 2. Baselines

Path: `baselines/`

Short, evidence-based snapshots of what the application actually implements at a given point in time.

Expected artifact:

- `IMPLEMENTATION_BASELINE.md`

Baselines describe implementation state only. They do not define product behavior.

## 3. Gap maps

Path: `gaps/`

Targeted comparisons between implemented surfaces/capabilities.

Expected artifact:

- `FRONTEND_BACKEND_CURRENT_GAP_MAP.md`

Gap maps identify matched, partial, disconnected, stale, or architecture-dependent seams. They must not invent missing product rules.

## 4. Runtime

Path: `runtime/`

Build, migration, startup, environment, database, authentication, route, and operational runtime findings.

Examples:

- build/startup proof
- PostgreSQL runtime proof
- migration/runtime compatibility
- environment gates
- route/runtime defects

Runtime evidence is implementation evidence, not authority.

## 5. Reconciliation

Path: `reconciliation/`

Small, bounded reconciliations caused by a known authority or architecture change.

Examples:

- `controlo_id` impact reconciliation
- Boquilhas repair-trace reconciliation
- targeted identity/relationship follow-up

Do not use this area for broad rediscovery audits unless explicitly requested.

## 6. Archive

Path: `archive/`

Superseded implementation reports or plans retained only when historical traceability is useful.

Archived files must never be treated as current authority or current implementation state.

## Repository discipline

Use this decision rule:

```text
product rule / canonical model
→ dmo-app/dmo-authority

application code
→ dmo-app/dmo-app-beta

plan / baseline / gap / runtime finding / reconciliation
→ diogo-o/dmo-reports
```

Before using a report to drive implementation:

1. confirm it is indexed here;
2. confirm whether it is active or archived;
3. verify important implementation claims against current source;
4. verify product behavior against current authority;
5. do not revive historical Beta behavior simply because an older report is more detailed.

# DMO — Implementation Roadmap (Draft)

Status: **DRAFT — to be refined as current implementation evidence is reconciled**

This roadmap is an implementation plan, not product authority.

## Governing rule

- `dmo-authority` describes the canonical product model.
- Application repositories show current implementation state.
- Reports/plans track gaps, sequencing, blockers, defects, and delivery work.
- Historical Beta material may be used as evidence only after revalidation against current authority and current code.
- Do not infer product behavior from stale plans, tests, or legacy repositories.

---

## R0 — Close the canonical model needed for implementation

### R0.1 — `controlo_id`

Close the persistent Controlo context in authority before structural database work.

Current accepted direction:

- `controlo_id` is the persistent identity of one Controlo context.
- It does not replace `jobon_id`.
- It does not replace `cm_id`, `mf_id`, or `bq_id`.
- It does not replace function-record identities such as `peso_id`, `comparacao_id`, or `pegamentos_id`.
- It provides a directly addressable context for Controlo-owned facts and functions.
- Controlo-specific production facts such as selected tampão / applicable calote belong here rather than on canonical Tool.
- Peso consumes relevant Controlo context; it must not create a second authority for those facts.

**Gate:** no Controlo schema redesign until this is reflected in current authority.

### R0.2 — Boquilhas identity / repair trace

Close the current Boquilhas identity model in authority.

Current accepted direction:

- `tool_id` = canonical physical BQ Tool.
- `bq_id` = BQ production context.
- `bq_repair_trace_id` = stable identity for the external repair-flow trace.
- A repair trace may exist before a production `bq_id` is known.
- Later association to the correct `bq_id` is explicit.
- Do not create a fake Job On or fake BQ production context.
- Do not introduce a synthetic `Início` event.
- Do not introduce explicit start/stop/close lifecycle solely to mark repair activity.
- Activity is movement-driven.

**Gate:** current authority and implementation must use the same identity/lifecycle language before Boquilhas structural changes.

### R0.3 — Ferramentas

Treat as closed unless current evidence shows a direct contradiction.

Canonical application model already established:

- `tool_id`
- type
- reference
- lot
- compatible machine/line
- repair state: `Novo | Reparado | Não reparado`
- Tool creation
- direct Tool-registry consultation
- table + filters
- explicit human opening/selection
- no auto-selection
- consultation/selection creates no production-context identity
- Peso consumes the Tool/context repair state
- production contexts preserve the Tool snapshot applicable to that production

Do not reopen this during implementation without explicit authority conflict.

---

## R1 — Establish the current implementation baseline

Perform a short current-source reconciliation, not a new global audit.

Inspect the current frontend and backend and classify each meaningful surface as:

- IMPLEMENTED
- PARTIAL
- NOT_IMPLEMENTED
- CONFLICT_WITH_AUTHORITY

Areas:

- Access / Admin / Login
- Ferramentas
- Job On
- Controlo Create
  - Peso
  - Comparação
  - Pegamentos
  - Resumo/Folha
  - Definições
- Controlo Approve
- Boquilhas
- Documents / PDF / Email
- Routes / Availability
- Shared Razor/frontend shell

Use current source as primary implementation evidence.

**Output:** `FRONTEND_BACKEND_CURRENT_GAP_MAP.md` and, if needed, a short consolidated `IMPLEMENTATION_BASELINE.md`.

No authority changes and no product-rule invention in this step.

---

## R2 — Database and identities

### R2.1 — Establish the real database baseline

Work from the actual/restored database state.

Sequence:

```text
inspect restored DB
→ inspect migration history
→ compare schema with current code
→ identify exact required delta
```

Do not assume a clean database and do not run migrations blindly over a restored backup.

### R2.2 — Implement `controlo_id`

Introduce the persistent Controlo context and only the relationships required by current authority.

Preserve existing natural identities:

- `tool_id`
- `jobon_id`
- `cm_id`
- `mf_id`
- `bq_id`
- `peso_id`
- `comparacao_id`
- `pegamentos_id`

Do not turn `controlo_id` into a god-parent for unrelated records.

Required evidence:

- migration/schema delta
- persistence integration tests
- historical queries/relationships remain valid
- existing identity chains are not rewritten incorrectly

### R2.3 — Controlo-owned production facts

After `controlo_id` exists, attach only facts whose ownership is confirmed as Controlo context.

Initial confirmed example:

```text
selected tampão
→ applicable calote
→ consumed by Peso where required
```

Do not create duplicate authorities.

---

## R3 — Complete backend functional seams

Only after R2 is stable.

### R3.1 — Job On + Ferramentas

Verify and complete only what current authority requires:

- canonical Tool selection
- production context creation
- Tool snapshot into applicable `cm_id / mf_id / bq_id`
- preservation of canonical `tool_id`
- Tool registry consultation
- repair-state consumption
- explicit human selection

Preserve already-correct code.

### R3.2 — Controlo Create

Connect the persistent Controlo context to the current functions.

Preserve working Peso behavior first.

Then reconcile actual gaps for:

- Peso
- Comparação
- Pegamentos
- Resumo/Folha

Do not import detailed behavior from historical roadmaps unless current authority supports it.

### R3.3 — Controlo Approve

Maintain independent Create/Approve capability enforcement.

Approve must operate on the same canonical records where applicable; do not create approval-copy identities without authority.

### R3.4 — Boquilhas

Align implementation with the accepted model:

```text
tool_id
→ bq_id where production context exists
→ bq_repair_trace_id
→ movements
```

Remove or replace only behavior proven incompatible with current authority.

Do not invent lifecycle state to fill gaps.

---

## R4 — Routes, availability, and authorization

Make a surface operational only when the required stack is real:

```text
canonical module/capability
+ server-side authorization
+ route registration
+ build availability
+ usable backend
+ usable frontend
```

Tasks:

- close P2-T10/current module availability
- register only real routes/surfaces
- preserve fail-closed direct-route enforcement
- fix the known `/Account/Login` redirect defect (`LoginPath` unset) without redesigning authentication

Do not register unfinished modules merely to expose navigation.

---

## R5 — Razor frontend

Begin broad frontend work only after the structural backend/database base is stable.

Implementation order:

1. shared Razor foundation / shell
2. Login / Admin
3. Ferramentas
4. Job On
5. Controlo
6. Boquilhas
7. remaining authorized surfaces

Migration rule:

```text
preserve first
normalize second
refactor third
```

Do not redesign while migrating.

Shared shell target:

- header shows current module
- Planeamento is the shared navigation entry/area
- second navigation area contains functions of the current module
- modules are separate surfaces, not tabs
- View/Create/Approve are capabilities, not tabs

---

## R6 — Documents / PDF / Email

Implement or complete these only after canonical identities/read models are stable.

Pattern:

```text
canonical structured record
→ read model
→ PDF/document output
→ storage/availability
→ optional send
```

Rules:

- structured data is truth
- PDF is derived output
- do not use filename/path as domain identity
- verify current code before assuming generation/storage/send is missing
- do not freeze stale identity semantics into documents

---

## R7 — Full integration

Test real cross-module flows, not only isolated CRUD.

Primary path:

```text
User
→ Access Template
→ module/capability
→ Tool
→ Job On
→ production context
→ Controlo
→ Peso / Comparação / Pegamentos / applicable summary surfaces
→ Approve
→ Documents
```

Boquilhas path:

```text
BQ Tool
→ repair trace
→ movements
→ explicit production-context association when applicable
```

Verify historical traversal and snapshot behavior.

---

## R8 — Release gate

Do not call the implementation complete until the relevant gates pass:

```text
BUILD: PASS
DB COMPATIBILITY: PASS
MIGRATIONS: PASS
AUTHORIZATION: PASS
ROUTES: PASS
MODULE AVAILABILITY: PASS
BACKEND FLOWS: PASS
FRONTEND FLOWS: PASS
HISTORY: PASS
DOCUMENTS: PASS
E2E: PASS
```

Final cleanup may then remove:

- proven dead code
- stale tests
- stale implementation documentation
- legacy role/profile residue
- references to rejected identities or superseded flows

Cleanup must follow evidence; do not use it to redesign the product.

---

## Immediate execution order

Current working order:

1. Close `controlo_id` in authority.
2. Close Boquilhas identity / repair-trace semantics in authority.
3. Complete current frontend ↔ backend gap reconciliation.
4. Consolidate the short implementation baseline.
5. Inspect the restored/real DB state.
6. Plan the exact `controlo_id` schema delta.
7. Implement `controlo_id` and required relationships.
8. Re-run build/database/regression proof.
9. Complete actual backend gaps revealed by the baseline.
10. Close routes / module availability / authorization integration.
11. Continue Razor migration.
12. Complete Documents / PDF / Email.
13. Run cross-module E2E and release gates.

---

## Refinement rule

This roadmap is intentionally a draft.

Refine it only from:

- current authority decisions;
- current frontend/backend evidence;
- verified database state;
- accepted implementation results.

Do not refine it from stale historical roadmaps or by assuming that older Beta behavior is canonical.

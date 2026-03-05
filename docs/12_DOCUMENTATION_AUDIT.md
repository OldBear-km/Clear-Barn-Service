# Documentation Audit Report

## Scope

Reviewed all Markdown documentation in the repository:
- root-level docs (`README.md`, `docs/*.md`)
- domain docs for catalog and label printing (`docs app/docs ...`)

## Critical inconsistencies

1. **Duplicate architecture content**
   - `docs/01_ARCHITECTURE.md` and `docs/02_TENANT_ISOLATION.md` currently contain almost identical Tenant Isolation text.
   - This creates ambiguity: file `01_ARCHITECTURE` is expected to be system architecture, but actually duplicates tenant isolation.

2. **Corrupted text in tenant isolation checklist**
   - In `docs/02_TENANT_ISOLATION.md` the checklist line contains garbage text (`...по времени шлллолзцorganization_id?`).
   - This looks like an editing artifact and reduces trust in the document.

3. **Data model mismatch in payroll section**
   - `docs/05_DATABASE_ARCHITECTURE.md` defines `payroll_records` with `employment_id`, but a later query example filters payroll by `user_id` in `payroll_records`.
   - That query cannot work with the schema as documented.

4. **Status model mismatch in label printing**
   - `02_DOMAIN_MODEL.md` describes print job statuses as `queued / claimed / printed / failed`.
   - `08_QUEUE_ARCHITECTURE.md` additionally includes `cancelled`.
   - Terminal API does not document a cancel endpoint, despite Queue UI mentioning Cancel action.

5. **Catalog overview is truncated**
   - `docs app/docs catalog/01_OVERVIEW.md` ends mid-thought at `- печати этикеток товаров`.
   - The section about Manufacturer usage is incomplete.

6. **API completeness gap (manufacturers)**
   - `docs app/docs catalog/04_API.md` documents only `GET /catalog/manufacturers` and `POST /catalog/manufacturers`.
   - Missing `GET by id`, `PATCH`, `DELETE` compared to all other catalog entities.

7. **Terminology mismatch for hierarchy**
   - Core docs describe Organization as branch-level isolation object.
   - Catalog docs state catalog works at organization level *not branch level*.
   - Label printing introduces `venue_id` in addition to `organization_id` without a canonical hierarchy definition in core docs.

8. **Command typo in developer workflow**
   - In `docs/10_DEVELOPMENT_WORKFLOW.md`, Alembic command uses an em dash (`—autogenerate`) instead of `--autogenerate`.

9. **Weak project entry point documentation**
   - `README.md` is a placeholder and does not link to architecture docs, setup, or module map.

## Medium-priority quality issues

1. **Inconsistent language style**
   - Mix of Russian/English naming and description style across docs.

2. **Formatting inconsistencies**
   - Several table separators use non-standard symbols (e.g. `|——|——|`, `|-—|-—|`) and may render inconsistently.

3. **Non-standard docs path with spaces**
   - `docs app/docs ...` increases friction for tooling, scripts, and contributors.

## Recommended remediation plan

### Phase 1 (must-fix)

1. Redefine `docs/01_ARCHITECTURE.md` as true top-level architecture overview:
   - context diagram
   - module boundaries
   - data ownership
   - integration points
2. Keep `docs/02_TENANT_ISOLATION.md` focused on tenant model only and remove corruption.
3. Fix payroll query example to match schema:
   - either join `employments` by `employment_id`
   - or add explicit `user_id` to `payroll_records` and document why.
4. Align print-job lifecycle across all label-printing docs:
   - either add `cancelled` everywhere + API endpoint
   - or remove cancelled state from queue docs.
5. Complete catalog manufacturer API section and finish truncated overview text.

### Phase 2 (consistency)

1. Add `Glossary` doc with canonical terms:
   - organization
   - branch/venue
   - global scope vs organization scope
2. Standardize naming convention:
   - one term in schema + API + architecture docs (`venue_id` vs `branch_id`)
3. Normalize Markdown tables and punctuation.
4. Rename docs directory to a tooling-friendly path (e.g. `docs/app/...`).

### Phase 3 (developer experience)

1. Expand `README.md`:
   - project purpose
   - quick start
   - documentation map
   - module matrix
2. Add documentation quality checks in CI:
   - markdownlint
   - link check
   - spell check (ru/en)
3. Add a docs change checklist to PR template.

## Suggested Definition of Done for docs quality

A docs update is complete when:
- terminology is consistent across architecture, schema, and API docs;
- every entity has complete CRUD/API description (or explicit rationale for deviations);
- examples are executable/correct (`--autogenerate`, valid SQL);
- no truncated sections or corrupted text remain;
- README points to all key docs.

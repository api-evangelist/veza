---
name: Run a Veza assessment query and report
description: Create and evaluate authorization assessment queries against the Veza Entity Catalog and group them into reports.
api: Veza Open Authorization API (OAA)
operations: [create_assessment_query, list_assessment_queries, create_report, list_reports]
source: github.com/veza/oaaclient-py
---

# Run a Veza assessment query and report

Use this flow to programmatically manage the saved authorization queries and
reports Veza evaluates over the Entity Catalog.

## Auth
- Per-tenant API key as `Authorization: Bearer <api_key>` against
  `https://<tenant>.vezacloud.com`.

## Steps
1. **List existing queries** — `GET /api/v1/assessments/queries` (optionally
   including inactive queries).
2. **Create a query** — `POST /api/v1/assessments/queries` with the query body.
   Fetch one back with `GET /api/v1/assessments/queries/{id}`.
3. **List reports** — `GET /api/preview/assessments/reports`.
4. **Create a report** — `POST /api/preview/assessments/reports`, then attach or
   read its queries via
   `GET /api/preview/assessments/reports/{report_id}/queries/{query_id}`.

## Rules
- Reports live under the `/api/preview` surface — treat as preview/subject to
  change (see `lifecycle/veza-lifecycle.yml`).
- Same retry/backoff and error-envelope rules as all OAA calls
  (`conventions/veza-conventions.yml`, `errors/veza-problem-types.yml`).

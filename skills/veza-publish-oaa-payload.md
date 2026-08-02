---
name: Publish an OAA payload to Veza
description: Register a custom provider and data source, then push identity/authorization metadata (users, resources, permissions) into the Veza Entity Catalog via the Open Authorization API.
api: Veza Open Authorization API (OAA)
operations: [create_custom_provider, create_data_source, push_oaa_metadata]
source: github.com/veza/oaaclient-py
---

# Publish an OAA payload to Veza

Use this flow to make a custom or unsupported application searchable in Veza by
pushing its authorization metadata via the Open Authorization API (OAA).

## Auth
- Get a per-tenant API key from the Veza console.
- Send it as `Authorization: Bearer <api_key>` on every request.
- All calls target your tenant host: `https://<tenant>.vezacloud.com`.
- The Python SDK: `OAAClient(url=veza_url, token=veza_api_key)`.

## Steps
1. **Create (or find) a custom provider** — `POST /api/v1/providers/custom` with a
   name and a template (`application` or `idp`). List with
   `GET /api/v1/providers/custom`. You cannot push without an existing provider
   (error `NO_PROVIDER`).
2. **Create a data source** under the provider —
   `POST /api/v1/providers/custom/{provider_id}/datasources`.
3. **Model the payload** — build a `CustomApplication` (or IdP) with local users,
   groups, roles, resources and permissions. In the SDK this is
   `oaaclient.templates`.
4. **Push the metadata** —
   `POST /api/v1/providers/custom/{provider_id}/datasources/{data_source_id}:push`.
   For payloads near the 100MB limit, use the multipart `:parts` endpoint with
   compression (payloads over 100MB are rejected with `OVERSIZE`).

## Rules
- The push replaces the data source's metadata declaratively — there is no
  idempotency key; re-pushing the current state is the reconciliation model.
- Retry `429/500/502/503/504` with exponential backoff (the SDK does this
  automatically).
- On error, read `code`, `message`, and `request_id` from the JSON response for
  support correlation (see `errors/veza-problem-types.yml`).

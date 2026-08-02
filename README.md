# Tricentis

Tricentis is an enterprise continuous testing and quality engineering company, founded in
Austria in 2007 and headquartered in Vienna with US operations in Austin, Texas. Its platform
spans model-based UI and API test automation (Tosca, Tosca Cloud), AI-driven test management
(qTest), performance testing (NeoLoad), Jira-native test management (Tricentis Test Management
for Jira), SAP change impact analysis (LiveCompare), data and ETL validation (Data Integrity),
regulated-industry validation (Vera), and quality analytics.

- Website — https://www.tricentis.com/
- Documentation — https://docs.tricentis.com/all/home.htm
- Status — https://status.tricentis.com/
- Trust & security — https://www.tricentis.com/trust/security

## APIs in this repo

| API | Spec | Operations |
|---|---|---|
| qTest Manager v3 | `openapi/tricentis-qtest-manager-openapi.yaml` (Swagger 2.0) | 190 |
| qTest Explorer Sessions | `openapi/tricentis-qtest-sessions-openapi.yaml` (Swagger 2.0) | 116 |
| Tricentis NeoLoad v3 | `openapi/tricentis-neoload-openapi.yaml` (OpenAPI 3.0.1) | 62 |
| Tricentis Analytics (OData v4) | `openapi/tricentis-qtest-analytics-openapi.json` (OpenAPI 3.0.2) | 40 |
| Tricentis Test Management for Jira | `openapi/tricentis-ttm4j-openapi.json` (OpenAPI 3.0.3) | 34 |
| qTest Parameters | `openapi/tricentis-qtest-parameters-openapi.yaml` (Swagger 2.0) | 32 |
| qTest Pulse | `openapi/tricentis-qtest-pulse-openapi.yaml` (Swagger 2.0) | 31 |
| qTest Scenario | `openapi/tricentis-qtest-scenario-openapi.yaml` (Swagger 2.0) | 8 |
| qTest Data Export | `openapi/tricentis-qtest-data-export-openapi.yaml` (Swagger 2.0) | 2 |

Where the specs came from:

- The qTest family is published as versioned artifacts in a public S3 bucket
  (`https://qtest-config.s3.amazonaws.com/api-docs/`), referenced by the qTest Swagger
  console at https://api.qasymphony.com/. Sixty-one qTest Manager builds are retrievable
  there, which makes that bucket a de-facto API changelog.
- The TTM4J spec is at `https://swagger.ttm4j.tricentis.com/swagger.json`, referenced by the
  Swagger UI at https://api.ttm4j.tricentis.com/.
- The NeoLoad specs (v1/v2/v3) are at `https://neoload-api.saas.neotys.com/explore/`.

> `openapi/tricentis-qtest-scenario-openapi.yaml` is saved verbatim as the provider publishes
> it. It contains duplicate YAML anchors (which strict PyYAML rejects) and declares a staging
> host. Both are the provider's, not ours, and neither has been edited.

## Agent surface

Tricentis was among the first enterprise quality-engineering vendors to ship remote MCP
servers. Four are catalogued in `mcp/tricentis-mcp.yml` — Tosca Cloud (51 published tools),
qTest, Tricentis Test Management for Jira (9 published tools) and an in-process Tosca
Commander server. All are tenant-scoped and auth-gated, so no live `tools/list` could be
introspected; the tool inventories here are the provider's own published lists.

`skills/` holds 27 agent skills Tricentis publishes open-source under Apache 2.0 at
https://github.com/Tricentis/mcp-skills, saved verbatim, plus three skills generated from the
OpenAPI in this repo to cover the qTest and TTM4J REST surfaces the provider catalog does not
reach.

No A2A agent card is published on any Tricentis host.

## Notable gaps

- No idempotency contract on any API — writes are not safely retryable.
- No RFC 9457 problem details; errors are a `{code, message}` JSON envelope.
- No RFC 9116 `security.txt`, despite a published disclosure policy and a `vdp@tricentis.com`
  contact.
- No AsyncAPI; the event surface is qTest webhooks (15 event types) plus qTest Pulse triggers.
- No RFC 8594 `Sunset`/`Deprecation` headers; deprecations are announced on a docs page.

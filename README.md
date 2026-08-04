# Tricentis

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

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

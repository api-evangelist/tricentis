---
name: ttm4j-author-and-execute
description: Author a test-case folder tree and test cases in Tricentis Test Management
  for Jira, link them to requirements, then create a cycle, import JUnit execution
  results and file defects against the failing runs.
api: openapi/tricentis-ttm4j-openapi.json
operations:
- HEAD /v1/api-key/is-alive
- GET /v1/projects
- POST /v1/projects/{project-key}/folders
- POST /v1/projects/{project-key}/test-cases
- POST /v1/projects/{project-key}/test-cases/requirements
- POST /v1/projects/{project-key}/test-cases/{key}/requirement/link
- POST /v1/projects/{project-key}/cycles
- POST /v1/projects/{project-key}/import/execution/junit
- POST /v1/projects/{project-key}/test-runs/search
- POST /v1/projects/{project-key}/test-runs/{test-run-key}/defects
- GET /v1/projects/{project-key}/jobs/{job-id}
generated: '2026-08-02'
method: generated
source: openapi/tricentis-ttm4j-openapi.json
---

# Author and execute tests in Tricentis Test Management for Jira

TTM4J is the Jira-native Tricentis test management product. Everything is scoped by
Jira `project-key`. The published spec declares no `operationId`s, so operations are
referenced here as `METHOD path`, exactly as the provider publishes them.

## Base URL and auth

- Base URL: `https://api.ttm4j.tricentis.com`
- Auth: a single API key sent in the `Authorization` header
  (spec scheme `api_key`, `in: header`).
- Validate the key first with `HEAD /v1/api-key/is-alive` — cheap, and it fails fast
  on a rotated key.

## Steps

1. **Check the key** — `HEAD /v1/api-key/is-alive`.
2. **Resolve the project** — `GET /v1/projects` returns the Jira projects the key
   can see; take the `project-key`.
3. **Build the folder tree** — `POST /v1/projects/{project-key}/folders`
   (nested creation supported); rename or move later with
   `PUT /v1/projects/{project-key}/folders/{folderId}`.
4. **Create test cases** — `POST /v1/projects/{project-key}/test-cases`, batched,
   with steps and metadata. Update with
   `PUT /v1/projects/{project-key}/test-cases/{key}`.
5. **Trace to requirements** — create them with
   `POST /v1/projects/{project-key}/test-cases/requirements` or create-and-link in
   one call with `POST /v1/projects/{project-key}/test-cases/{key}/requirements`;
   link existing ones with
   `POST /v1/projects/{project-key}/test-cases/{key}/requirement/link`.
6. **Mark automation coverage** —
   `PUT /v1/projects/{project-key}/test-cases/{key}/automation`.
7. **Create the cycle** — `POST /v1/projects/{project-key}/cycles`.
8. **Import results from CI** —
   `POST /v1/projects/{project-key}/import/execution/junit` with the JUnit XML. This
   creates the test runs.
9. **Poll the job** — long-running operations return a job id; poll
   `GET /v1/projects/{project-key}/jobs/{job-id}` until it is terminal.
10. **Find failures** — `POST /v1/projects/{project-key}/test-runs/search`.
11. **File and link defects** —
    `POST /v1/projects/{project-key}/test-runs/{test-run-key}/defects`, or link
    existing Jira issues with `.../defects/link`.
12. **Attach evidence** —
    `POST /v1/projects/{project-key}/test-runs/{test-run-key}/attachments` and the
    per-step variant `.../steps/{step-number}/attachments`. Confirm the upload landed
    in storage with `PUT /v1/projects/{project-key}/test-runs/attachments/{id}`.

## Rules

- **Do not use the deprecated reads.** `GET /v1/projects/{project-key}/test-cases`
  and `POST /v1/test-cases/search` are both marked `deprecated: true` in the spec.
  Use `GET /v1/projects/{project-key}/test-cases/bulk` and
  `POST /v1/test-cases/search/jql` instead.
- **No idempotency key exists.** Retrying a create after a timeout duplicates the
  artifact — search first, then create.
- The same nine authoring capabilities are exposed as MCP tools at
  `https://ttm4j.tricentis.com/v1/mcp`; the REST↔tool bindings are recorded in
  `mcp/tricentis-tool-crosswalk.yml`.

---
name: qtest-submit-automation-results
description: Authenticate against Tricentis qTest, locate or create the test run for a
  build, and submit automation results (with attachments) so the execution shows in
  qTest Manager.
api: openapi/tricentis-qtest-manager-openapi.yaml
operations:
- postAccessToken
- getProjects
- searchArtifact
- createTestRun
- getStatusValuable
- submitAutomationLog
- submitAutomationTestLogsWithTreeStructure
- track
generated: '2026-08-02'
method: generated
source: openapi/tricentis-qtest-manager-openapi.yaml
---

# Submit automation results to qTest Manager

Use this skill to push CI/automation results into Tricentis qTest so they appear as
test logs against the right test run and release.

## Base URL and auth

- Base URL is your qTest site: `https://<site>.qtestnet.com` (the published spec's
  try-out host is `https://apitryout.qtestnet.com/`).
- Get a bearer token with `postAccessToken` — `POST /oauth/token`. The client
  credentials come from the qTest **Resources → API & SDK** page.
- Send it on every call as `Authorization: bearer <token>` (the spec declares one
  `apiKey`-in-header scheme named `Authorization`).
- Check a token with `tokenStatus` — `GET /oauth/status`.

## Steps

1. **Authenticate.** Call `postAccessToken`. Store `access_token` and note the
   expiry; do not re-authenticate per request.
2. **Resolve the project.** Call `getProjects` and pick the `id` of your project, or
   go straight to a known `projectId`.
3. **Find the target test run.** Call `searchArtifact`
   (`POST /api/v3/projects/{projectId}/search`) with a query on the test-case or
   test-run automation key. If nothing matches, create one with `createTestRun`.
4. **Resolve the execution status.** Call `getStatusValuable`
   (`GET /api/v3/projects/{projectId}/test-runs/execution-statuses`) to get the
   valid status ids — never hard-code a status id, they are per-project fields.
5. **Submit the result.**
   - Single run: `submitAutomationLog`
     (`POST /api/v3/projects/{projectId}/test-runs/{testRunId}/auto-test-logs`).
   - Whole suite in one call, creating the module tree as needed:
     `submitAutomationTestLogsWithTreeStructure`
     (`POST /api/v3/projects/{projectId}/auto-test-logs`). This one is queued.
6. **Poll the queue.** A queued submission returns a job id — poll `track`
   (`GET /api/v3/projects/{projectId}/queue-processing/{id}`) until it reports a
   terminal state before declaring the upload complete.
7. **Correct a bad log** with `modifyAutomationLog`
   (`PUT /api/v3/projects/{projectId}/test-runs/{testRunId}/auto-test-logs/{id}`).

## Rules

- **Rate limits are real.** qTest throttles SaaS API calls and returns `429`. Read
  `x-ratelimit-remaining-minute` and `x-ratelimit-remaining-month` on every response
  and back off before you are cut off. See `conventions/tricentis-conventions.yml`.
- **There is no idempotency key.** qTest publishes no idempotency contract, so a
  blind retry of `submitAutomationLog` creates a duplicate test log. On a timeout,
  re-read the run's logs with `getTestLogsList` before retrying.
- **Pagination is `page` + `pageSize`**, 1-based, on the list operations.
- Errors are plain JSON, not RFC 9457 — see `errors/tricentis-problem-types.yml`.

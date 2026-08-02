---
name: qtest-register-webhook
description: Register, verify and maintain a Tricentis qTest webhook so an external
  system receives qTest test-case, test-run, test-log, defect, requirement and project
  events as they happen.
api: openapi/tricentis-qtest-manager-openapi.yaml
operations:
- postAccessToken
- getAllEventNames
- createWebhook
- getAllWebhooks
- getWebhookById
- updateWebhook
- deleteWebhookById
generated: '2026-08-02'
method: generated
source: openapi/tricentis-qtest-manager-openapi.yaml + https://docs.tricentis.com/qtest-saas/content/apis/webhooks.htm
---

# Register a qTest webhook

qTest webhooks are user-defined POST callbacks fired when an event occurs in a
project. This is qTest's event surface — there is no AsyncAPI document.

## Steps

1. **Authenticate.** `postAccessToken` (`POST /oauth/token`), then send
   `Authorization: bearer <token>`.
2. **List the legal event names.** `getAllEventNames`
   (`GET /api/v3/webhooks/events`). Do not hard-code event strings from memory —
   read them from the API. The documented set is:
   `project_created`, `project_updated`,
   `testcase_created`, `testcase_updated`, `testcase_deleted`,
   `testrun_created`, `testrun_updated`, `testrun_deleted`,
   `testlog_submitted`, `testlog_modified`,
   `requirement_created`, `requirement_updated`, `requirement_deleted`,
   `defect_submited`, `defect_modified`.
   Note `defect_submited` is spelled that way by the provider.
3. **Register.** `createWebhook` (`POST /api/v3/webhooks`) with the webhook name,
   the target URL, the project scope and the event list. Set a **secret key** so
   qTest signs each payload.
4. **Verify the signature on receipt.** When a secret key is configured, qTest sends
   a hash of the payload in the `x-qTest-signature` header. Reject any callback whose
   signature does not verify.
5. **Confirm registration.** `getAllWebhooks` (`GET /api/v3/webhooks`) or
   `getWebhookById` (`GET /api/v3/webhooks/{webhookId}`).
6. **Change the target or event list** with `updateWebhook`
   (`PUT /api/v3/webhooks/{webhookId}`); **remove** with `deleteWebhookById`.

## Payload

Each callback body carries `event_timestamp`, `event_type`, and an object with the
subject's id plus `project_id`. Treat the payload as a notification, not a record —
re-read the object over the REST API before acting on it.

## Rules

- Both HTTP and HTTPS endpoints are accepted by qTest; always register HTTPS.
- Tricentis publishes no retry/backoff contract for webhook delivery — build the
  consumer to tolerate missed events by reconciling with
  `getLastChanged` (`GET /api/v3/projects/{projectId}/defects/last-change`) and the
  list operations.
- For rule-based fan-out to Jenkins, Jira, GitHub and similar, use qTest Pulse
  triggers instead — see `openapi/tricentis-qtest-pulse-openapi.yaml`.

---
name: Poll AMCS Vision AI events
description: Authenticate and poll AMCS Vision AI for contamination and overfilled-container events from resource-stream imagery.
api: openapi/amcs-group-visionai-openapi-original.json
operations: [GetContaminationEventChanges, GetOverfilledContainerEventChanges]
---

# Poll AMCS Vision AI events

Pull automated waste/recycling insight events produced by AMCS Vision AI from container and collection imagery.

## Auth
1. Authenticate to the Vision AI developer environment (`https://o1-dev-svc-core.amcsplatform.com/`) using the PAT -> session-cookie flow; Vision AI carries the session as the `cookieAuth` cookie.

## Steps
1. **Contamination events** — call `GetContaminationEventChanges` to fetch contamination detections changed since your last checkpoint.
2. **Overfilled containers** — call `GetOverfilledContainerEventChanges` to fetch overfilled-container detections.
3. Persist the high-water mark from each response and repeat on your polling interval.

## Rules
- This is a change-polling (delta) surface, not a webhook/push feed — poll on an interval.
- Handle `401`/`403` by re-authenticating or checking access; `5xx` (500-504) are transient — back off and retry.
- Event payloads may reference imagery (`image/jpeg`); download separately as needed.
- See `errors/amcs-group-problem-types.yml`.

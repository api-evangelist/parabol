---
name: Run a Parabol retrospective end to end
description: Start a retrospective meeting for a team, add reflection cards, advance through the meeting stages, and end the meeting using the Parabol GraphQL API.
api: graphql/parabol-schema.graphql
operations: [startRetrospective, createReflection, navigateMeeting, endRetrospective]
---

# Run a Parabol retrospective end to end

Use the Parabol GraphQL API to run a full retrospective for a team.

## Prerequisites
- A Parabol **Personal Access Token** created at https://action.parabol.co/me/profile.
- Token must carry the `meetings:write` scope (and `teams:read` to resolve the team).
- Endpoint: `POST https://action.parabol.co/graphql`
- Auth: `Authorization: Bearer <token>`

## Steps
1. **Start the meeting.** Call `startRetrospective` with the target `teamId`. Capture the returned meeting `id`.
2. **Collect reflections.** For each observation, call `createReflection` with the `meetingId`, the reflect `promptId` (retro column), and the card `content`.
3. **Advance stages.** Call `navigateMeeting` with the `meetingId` to move the group through Reflect → Group → Vote → Discuss.
4. **End the meeting.** Call `endRetrospective` with the `meetingId` to close the retro and generate the summary.

## Rules
- Mutations are **not** documented as idempotent — do not blindly retry a write; re-query `viewer` to confirm state before retrying.
- Business-rule failures return in-band as `error { title message }` (StandardMutationError) on the payload; check it even on HTTP 200.
- Transport/auth/scope failures appear in the top-level GraphQL `errors[]` array.
- Request only the scopes the flow needs (least privilege).

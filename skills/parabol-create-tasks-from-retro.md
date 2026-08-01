---
name: Create and comment on tasks from meeting outcomes
description: Turn meeting discussion outcomes into actionable Parabol tasks and add comments, using the Parabol GraphQL API.
api: graphql/parabol-schema.graphql
operations: [createTask, addComment]
---

# Create and comment on tasks from meeting outcomes

Turn retro/check-in outcomes into tracked work in Parabol.

## Prerequisites
- Parabol **Personal Access Token** from https://action.parabol.co/me/profile.
- Scopes: `tasks:write` (create tasks) and `comments:write` (add comments).
- Endpoint: `POST https://action.parabol.co/graphql`, `Authorization: Bearer <token>`.

## Steps
1. **Create the task.** Call `createTask` with the `teamId`, task `content` (rich text), and optionally the `meetingId` it originated from. Capture the returned task `id`.
2. **Add context.** Call `addComment` with the discussion `threadId`/`discussionId` and `content` to record rationale or follow-up.
3. **Optional integration.** A task can be pushed to an integration (Jira, GitHub, GitLab, Linear, Azure DevOps) — resolve the integration provider on the team first.

## Rules
- Check the in-band `error { title message }` (StandardMutationError) on each payload even on HTTP 200.
- List/read fields use Relay cursor pagination (`first`/`after`, `edges { node } pageInfo { hasNextPage endCursor }`).
- No idempotency key is supported; de-dupe on the client before creating tasks.
- Use least-privilege scopes.

# AGENTS.md

## 1. Purpose
This file is the operating contract for AI/human agents working on this repository.
Goal: build and maintain a production-like Slack-lite chat platform with GraphQL + WebSocket subscriptions.

Primary outcomes:
- predictable architecture and coding style
- reliable real-time behavior
- testable and maintainable code
- consistent task execution across different agents

## 2. Product Scope (v1)
Build the following core capabilities:
- Authentication: register, login, logout
- Workspaces: create workspace, invite users, list members
- Channels: public/private channels, list channels per workspace
- Messaging: send/edit/soft-delete messages, message history with pagination
- Threads: replies using `parentMessageId`
- Reactions: add/remove reactions with aggregation
- Real-time: new messages, thread updates, typing, presence, reaction updates, read receipts

Out of v1 scope unless explicitly requested:
- full-text search
- enterprise-grade RBAC matrix
- mobile client

## 3. Locked Technical Decisions
Default stack:
- Frontend: React + TypeScript + Apollo Client (or urql) + Zustand
- Backend: Node.js + TypeScript + GraphQL Yoga/Apollo + graphql-ws
- Data: PostgreSQL + Prisma
- Realtime/scaling: Redis pub/sub
- Infra: Docker Compose

If an agent proposes changing core stack components, it must provide:
1) migration cost
2) clear benefit
3) compatibility plan

## 4. Architecture Rules
Use clean layers and do not bypass them:
- API Layer: GraphQL schema, resolvers, DTO mapping
- Service Layer: business logic, permission checks, transactions, domain events
- Data Layer: Prisma/repositories only
- Realtime Layer: publish/subscribe, connection state, fan-out

Mandatory rule:
- Resolvers must stay thin. Business logic belongs in services.

## 5. Data and Domain Invariants
Required entities:
- User
- Workspace
- WorkspaceMember
- Channel
- ChannelMember
- Message (`parentMessageId` for thread replies)
- Reaction
- ReadReceipt

Invariants:
- user must be workspace member to view workspace resources
- user must be channel member to read/write channel messages
- one reaction per (`messageId`, `userId`, `emoji`)
- message delete is soft delete (`deletedAt`)
- edited message tracks `editedAt`
- message ordering is stable by (`createdAt`, `id`)

## 6. GraphQL Contract Baseline
Queries:
- `me`
- `workspaces`
- `channels(workspaceId)`
- `messages(channelId, cursor, limit)`
- `thread(messageId, cursor, limit)`

Mutations:
- `sendMessage`
- `editMessage`
- `deleteMessage`
- `addReaction`
- `removeReaction`
- `createChannel`
- `markAsRead`

Subscriptions:
- `messageCreated(channelId)`
- `threadMessageCreated(messageId)`
- `reactionUpdated(messageId)`
- `userTyping(channelId)`
- `userPresenceChanged(workspaceId)`

Schema changes must include:
- backward-compatibility note
- migration note (if DB impact exists)

## 7. Realtime Reliability Rules
- Use server-generated event IDs and dedupe on client by event/message ID.
- WebSocket reconnect must resubscribe automatically.
- Presence and typing are ephemeral; Redis-backed in multi-instance mode.
- Do not use polling for chat updates when subscriptions are available.
- Keep publish payloads minimal; fetch heavy data via query if needed.

## 8. Coding Standards
General:
- TypeScript strict mode.
- No `any` unless justified in a comment.
- Keep functions focused and small.
- Use explicit return types in public service functions.

Backend:
- Validate auth and membership in services.
- Wrap multi-step writes in transactions.
- Standardized error mapping to GraphQL errors.

Frontend:
- Separate server cache state (Apollo/urql) from UI ephemeral state (Zustand).
- Use optimistic updates for sending messages.
- Keep subscription handlers idempotent.

## 9. Testing Requirements
Minimum for each feature PR/task:
- Unit tests for service logic and permission checks
- Integration tests for GraphQL operations (query/mutation/subscription path)
- Critical E2E smoke:
  - user A sends message
  - user B receives real-time update
  - reconnect does not duplicate messages

Focus areas:
- cursor pagination correctness
- duplicate prevention
- reconnect behavior
- soft delete/edit visibility

## 10. Task Workflow for Agents
For every task, follow this order:
1) Read current contract and impacted code
2) State assumptions explicitly
3) Implement smallest correct slice
4) Add/adjust tests
5) Run verification commands
6) Document what changed and why

When splitting work between agents, assign ownership explicitly:
- Schema Agent: GraphQL schema/types/contracts
- Backend Agent: resolvers/services/repositories
- Realtime Agent: subscriptions/ws/redis/presence
- Frontend Agent: UI/state/subscriptions integration
- QA Agent: tests, edge cases, regression checks

No agent should revert another agent's changes without explicit instruction.

## 11. Definition of Done (DoD)
A task is done only if all are true:
- feature works per acceptance criteria
- permission checks and edge cases are covered
- tests added/updated and pass locally
- no obvious regressions in related flows
- documentation/comments updated when behavior changed

## 12. PR/Change Template
Every significant change should include:
- What changed
- Why this approach
- API/schema changes (if any)
- Data model/migration impact (if any)
- Realtime impact (if any)
- Test evidence
- Follow-up tasks (optional)

## 13. First Implementation Milestones
Recommended sequence:
1) Scaffold app + DB + GraphQL skeleton
2) Auth + workspace/channel membership
3) Message CRUD + cursor pagination
4) Subscriptions + WS reconnect
5) Threads + reactions + read receipts
6) Presence/typing + Redis scaling
7) Docker/deploy/readme polish

## 14. Keep This File Updated
Update this file when:
- architectural decisions change
- stack changes
- domain invariants change
- testing policy changes

If code and this file disagree, align one of them immediately in the same task.

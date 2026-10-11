---
description: "Task list for the Ideas & Tasks System feature"
---

# Tasks: Ideas & Tasks System

**Input**: Design documents from `/specs/001-ideas-tasks-system/`

**Prerequisites**: plan.md, spec.md, research.md, data-model.md, contracts/, quickstart.md

**Tests**: Not explicitly requested in the spec. A small number of unit tests are included only
where plan.md's Technical Context explicitly calls for them (idempotent promotion, frontmatter
round-tripping) — not a full TDD/contract-test-first suite.

**Organization**: Tasks are grouped by user story (spec.md) to enable independent implementation
and testing of each story. All file paths are relative to the new `lab-hub` repository's root (see
plan.md's Project Structure — this repo cannot host the application itself).

## Format: `[ID] [P?] [Story] Description`

- **[P]**: Can run in parallel (different files, no dependencies)
- **[Story]**: Which user story this task belongs to (US1-US7)

## Phase 1: Setup

**Purpose**: Stand up the new repository, hosting, and the project data this feature depends on.

- [X] T001 Create GitHub repo `paulalexeevich/lab-hub` and scaffold a Next.js (App Router,
      TypeScript) project in it as the initial commit, per plan.md's Project Structure
- [X] T002 [P] Add dependencies to `lab-hub/package.json`: `next`, `react`, `react-dom`,
      `typescript`, `@octokit/rest`, `@octokit/graphql`, `gray-matter`, an MCP server SDK
      (`@vercel/mcp-adapter` or `@modelcontextprotocol/sdk`)
- [X] T003 [P] Configure Vitest, ESLint, and Prettier in `lab-hub`
- [~] T004 Create a Vercel project linked to `lab-hub` and configure environment variables
      (`GITHUB_TOKEN`, `HUB_ACCESS_TOKEN`, `TELEGRAM_BOT_TOKEN`, `TELEGRAM_CHAT_ID`,
      `TELEGRAM_WEBHOOK_SECRET`, `GITHUB_WEBHOOK_SECRET`) per `contracts/auth.md` — PARTIAL: an
      initial attempt was linked under the wrong personal Vercel account
      (`pavelpopkov-2586s-projects`, not connected to the `paulalexeevich` GitHub login) and hit
      Vercel's Hobby-plan commit-author verification block (deployment stuck `BLOCKED`); re-linked
      and deployed successfully under the correct `paulalexeevichs-projects` scope (live, `READY`,
      `ssoProtection` confirmed on), which also auto-connected GitHub push-to-deploy.
      `HUB_ACCESS_TOKEN`/`GITHUB_WEBHOOK_SECRET`/`TELEGRAM_WEBHOOK_SECRET` generated and set there.
      `GITHUB_TOKEN` still requires Pavel's manual action — a broad-scope personal credential was
      judged too high-blast-radius to auto-provision. `TELEGRAM_BOT_TOKEN`/`TELEGRAM_CHAT_ID`
      require Pavel's manual @BotFather step (see README.md).
- [X] T005 Resolve FR-016 for the three currently repo-less projects: add/verify
      `projects/catalogue.md` in Pavel-lab with `repo: paulalexeevich/Pavel-lab`; create new GitHub
      repos for retail-analytics and sku-analyzer and add `projects/retail-analytics.md` /
      `projects/sku-analyzer.md` with their `repo:` fields set

**Checkpoint**: Repo, hosting, and project data prerequisites are in place.

---

## Phase 2: Foundational (Blocking Prerequisites)

**Purpose**: The shared `lib/` core every user story is built on (Constitution IV — thin clients,
shared core). No user story work should start before this phase is complete.

**⚠️ CRITICAL**: No user story work can begin until this phase is complete

- [X] T006 Implement a GitHub Contents API client in `lab-hub/lib/github-contents.ts` (read a file,
      create/update a file with SHA handling) used by all catalogue read/write
- [X] T007 [P] Implement `lab-hub/lib/catalogue.ts`: `readProjects()` and `readIdeas()` — list and
      parse every `projects/*.md` / `ideas/*.md` frontmatter via `gray-matter` (depends on T006)
- [X] T008 [P] Implement `lab-hub/lib/catalogue-write.ts`: `writeIdeaFile()` and
      `updateIdeaFrontmatter()` — additive-only writes that never disturb existing file content
      (depends on T006)
- [X] T009 [P] Implement `lab-hub/lib/tasks.ts`: `listIssues(repo)` and
      `createIssue(repo, title, body)` via Octokit REST, plus a batched GraphQL query for
      cross-repo open/closed counts and last-activity (depends on T002)
- [X] T010 [P] Implement `lab-hub/lib/auth.ts`: bearer-token check against `HUB_ACCESS_TOKEN`,
      used by every UI route, the MCP route, and non-webhook API routes (`contracts/auth.md`)
- [X] T011 [P] Implement `lab-hub/lib/notify.ts` with a stub `sendNotification(event)` that logs to
      console — unblocks idea/task flows ahead of the real Telegram integration (US5) — NOTE:
      implemented combined with T033 (graceful fallback to console.log built in from the start)
- [X] T012 Define shared domain types in `lab-hub/lib/types.ts`: `Project`, `Idea`, `Task` per
      `data-model.md`, including the new Idea frontmatter fields (`project`, `promoted_to_issue`,
      `source`, `meeting_url`, `draft`)
- [X] T013 Set up the base Next.js app shell in `lab-hub/app/layout.tsx` and
      `lab-hub/app/(dashboard)/layout.tsx`, gated by `lib/auth.ts` (depends on T010) — plus
      `/login` page and `/api/login` route for the bearer-token session cookie (not separately
      tracked in tasks.md but required for the UI auth gate to be usable)

**Checkpoint**: Foundation ready — user story implementation can now begin.

---

## Phase 3: User Story 1 - See project status and task health at a glance (Priority: P1) 🎯 MVP

**Goal**: Every catalogued project appears with its status and an open/closed task count + last
activity, even when a repo has no tasks or GitHub is temporarily unreachable.

**Independent Test**: `quickstart.md` Scenario 1.

- [X] T014 [US1] Implement `getProjectOverview()` in `lab-hub/lib/overview.ts`, joining
      `readProjects()` (T007) with per-repo task counts/last-activity (T009); on a per-repo GitHub
      failure, mark that project `taskDataStale: true` instead of failing the whole call (FR-002,
      FR-015) (depends on T007, T009)
- [X] T015 [US1] Add "no tasks tracked" handling in `getProjectOverview()` for a project with no
      `repo` configured, or whose `repo` is not a GitHub repo (e.g., `owner/name` doesn't resolve
      via the GitHub API — covers `ailet-kb`'s GitLab repo), distinct from the stale/error case
      (Edge Case)
- [X] T016 [US1] Build the overview page `lab-hub/app/(dashboard)/page.tsx` rendering status, task
      counts, last-activity, and a staleness indicator per project (depends on T014, T015)
- [X] T017 [P] [US1] Add a unit test for the stale-fallback behavior of `getProjectOverview()` in
      `lab-hub/tests/unit/overview.test.ts`

**Checkpoint**: User Story 1 is fully functional and independently testable — Pavel can see every
project's status and task health in one view.

---

## Phase 4: User Story 2 - Capture an idea in seconds (Priority: P1)

**Goal**: A new idea (general or project-tied) can be captured from the app, the bot, or an agent,
landing in `ideas/{slug}.md` in the existing catalogue format.

**Independent Test**: `quickstart.md` Scenario 2.

- [X] T018 [US2] Implement `createIdea(input)` in `lab-hub/lib/ideas.ts`: slugify the name, build
      frontmatter (`name`, `status: idea`, `added`, optional `project`), call
      `writeIdeaFile()` (T008), call `lib/notify.ts` (T011 stub) (depends on T008, T011, T012)
- [X] T019 [US2] Add slug-collision (409) and required-field (400) validation to `createIdea()` per
      `contracts/mcp-tools.md`'s `create_idea` error cases
- [X] T020 [P] [US2] Build the "new idea" page `lab-hub/app/(dashboard)/ideas/new/page.tsx` with an
      optional project picker, calling `createIdea()` via a server action (depends on T018)
- [X] T021 [P] [US2] Build the ideas list page `lab-hub/app/(dashboard)/ideas/page.tsx` listing
      every idea via `readIdeas()` (T007)
- [X] T022 [US2] Add a unit test asserting `createIdea()`'s output file matches the existing
      `/catalogue idea` frontmatter shape in `lab-hub/tests/unit/ideas.test.ts`

**Checkpoint**: User Stories 1 and 2 both work independently — the overview and idea capture are
usable end to end.

---

## Phase 5: User Story 3 - Browse tasks across and within projects (Priority: P2)

**Goal**: A consolidated cross-project task list, and a list scoped to one project.

**Independent Test**: `quickstart.md` Scenario 3.

- [X] T023 [US3] Implement `listTasks({ project? })` in `lab-hub/lib/tasks-query.ts`, filtering
      `lib/tasks.ts` (T009) results by project when given, or aggregating across all repos
      otherwise (depends on T009)
- [X] T024 [US3] Build the cross-project task list page `lab-hub/app/(dashboard)/tasks/page.tsx`,
      each row labeled with its project (depends on T023)
- [X] T025 [P] [US3] Build the per-project task list page
      `lab-hub/app/(dashboard)/projects/[slug]/tasks/page.tsx` (depends on T023)
- [X] T026 [P] [US3] Add the `linkedIdeaSlug` reverse-lookup (idea whose `promoted_to_issue`
      matches a task's `url`) to task list rows per `data-model.md`'s Task entity

**Checkpoint**: User Stories 1-3 all work independently.

---

## Phase 6: User Story 4 - Promote an idea into a tracked task (Priority: P2)

**Goal**: One explicit action creates exactly one GitHub Issue from an idea, updates the idea's
frontmatter, and can never create a duplicate for the same idea.

**Independent Test**: `quickstart.md` Scenario 4.

- [X] T027 [US4] Implement `promoteIdeaToTask(slug)` in `lab-hub/lib/promote.ts`: return
      `alreadyPromoted: true` with the existing issue URL if `promoted_to_issue` is already set;
      otherwise resolve the idea's project repo, call `createIssue()` (T009) with a body linking
      back to the idea file, then `updateIdeaFrontmatter()` (T008) to set `promoted_to_issue`
      (depends on T007, T008, T009)
- [X] T028 [US4] Add the "idea has no associated project" validation error (400) to
      `promoteIdeaToTask()` per `contracts/mcp-tools.md`
- [X] T029 [US4] Wire a `lib/notify.ts` (T011) call into `promoteIdeaToTask()` on successful
      creation
- [X] T030 [P] [US4] Add a "Promote to task" action with a visible confirmation step to
      `lab-hub/app/(dashboard)/ideas/[slug]/page.tsx`, calling `promoteIdeaToTask()`
      (Constitution VI) (depends on T027)
- [X] T031 [US4] Show an "already promoted" state (linking to the existing issue) instead of the
      promote action when `promoted_to_issue` is set (FR-008) (depends on T027, T030)
- [X] T032 [US4] Add a unit test for promotion idempotency (second call returns `alreadyPromoted`,
      no second Issue is created) in `lab-hub/tests/unit/promote.test.ts`

**Checkpoint**: User Stories 1-4 all work independently — the full idea→task loop works end to end
through the app.

---

## Phase 7: User Story 5 - Get notified without opening the app (Priority: P3)

**Goal**: Telegram notifications for idea capture, task created/closed, and a periodic digest.

**Independent Test**: `quickstart.md` Scenario 5.

- [X] T033 [US5] Implement the real Telegram send in `lab-hub/lib/notify.ts` using the Telegram Bot
      API (`TELEGRAM_BOT_TOKEN`, `TELEGRAM_CHAT_ID`), replacing the Phase 2 stub, with message
      shapes from `contracts/webhooks-and-notifications.md` (depends on T011)
- [X] T034 [US5] Implement the GitHub `issues` webhook handler
      `lab-hub/app/api/github/webhook/route.ts`: verify `X-Hub-Signature-256`, send "task
      created"/"task closed" notifications on `opened`/`closed` events (depends on T033)
- [X] T035 [P] [US5] Register the GitHub `issues` webhook (pointed at `/api/github/webhook`) on
      every project repo and document the steps in `lab-hub/README.md` — done and verified directly
      via `gh api repos/{repo}/hooks` on all 5 GitHub-hosted repos (Pavel-lab, UniqEqAgent, lab-hub,
      retail-analytics, sku-analyzer); `ailet-kb` (GitLab) correctly skipped
- [X] T036 [US5] Implement the digest composer `lab-hub/lib/digest.ts` using
      `getProjectOverview()` (T014) and `lib/notify.ts` (T033)
- [X] T037 [US5] Implement the Vercel Cron target `lab-hub/app/api/cron/digest/route.ts` calling
      `lib/digest.ts`, and add the schedule to `lab-hub/vercel.json` (depends on T036)
- [X] T038 [P] [US5] Implement the Telegram webhook handler
      `lab-hub/app/api/telegram/webhook/route.ts`: verify
      `X-Telegram-Bot-Api-Secret-Token`, parse `/idea <name> | <description>` into
      `createIdea()` (T018) (depends on T033)

**Checkpoint**: User Stories 1-5 all work independently — Pavel gets proactive notifications
without opening the app.

---

## Phase 8: User Story 6 - Let an agent act on Pavel's behalf (Priority: P3)

**Goal**: An agent can call `list_projects`, `list_ideas`, `list_tasks`, `create_idea`, and
`promote_idea_to_task` over MCP, with the same confirmation guarantees as the app.

**Independent Test**: `quickstart.md` Scenario 6.

- [X] T039 [US6] Implement the MCP server route `lab-hub/app/api/mcp/route.ts` (HTTP transport),
      authenticated via `lib/auth.ts` (T010)
- [X] T040 [US6] Register the `list_projects` tool wrapping `getProjectOverview()` (T014) per
      `contracts/mcp-tools.md` (depends on T039)
- [X] T041 [P] [US6] Register the `list_ideas` tool wrapping `readIdeas()` (T007) with an optional
      project filter (depends on T039)
- [X] T042 [P] [US6] Register the `list_tasks` tool wrapping `listTasks()` (T023) with an optional
      project filter (depends on T039)
- [X] T043 [US6] Register the `create_idea` tool requiring `confirm: true`, wrapping
      `createIdea()` (T018), rejecting with 400 when `confirm` is not `true` (depends on T039)
- [X] T044 [US6] Register the `promote_idea_to_task` tool requiring `confirm: true`, wrapping
      `promoteIdeaToTask()` (T027) (depends on T039)
- [X] T045 [US6] Add an integration test exercising all five MCP tools against a stubbed GitHub API
      in `lab-hub/tests/integration/mcp-tools.test.ts`

**Checkpoint**: User Stories 1-6 all work independently — an agent can do everything the UI can,
under the same explicit-confirmation rules.

---

## Phase 9: User Story 7 - Turn meeting discussion into draft ideas (Priority: P4)

**Goal**: An agent with access to Granola and this system's MCP server can create draft ideas
linked back to a meeting, associated with a project when identifiable, and can never create a task
through this path.

**Independent Test**: `quickstart.md` Scenario 7.

- [X] T046 [US7] Extend `createIdea()` (T018) to accept `source: "meeting"` + `meetingUrl`
      (required together) and set `draft: true` in frontmatter per `data-model.md`'s Idea
      validation rules
- [X] T047 [US7] Extend the `create_idea` MCP tool (T043) input schema to accept
      `source`/`meetingUrl`/`draft` per `contracts/mcp-tools.md` (depends on T046)
- [X] T048 [P] [US7] Add a draft/meeting-sourced badge to the ideas list and detail pages
      (`lab-hub/app/(dashboard)/ideas/page.tsx`, `.../ideas/[slug]/page.tsx`) for ideas with
      `draft: true`
- [X] T049 [US7] Add a test asserting no code path reachable from `createIdea()` or the meeting
      flow ever calls `promoteIdeaToTask()`/`createIssue()` — only the explicit promote action does
      (FR-012) in `lab-hub/tests/unit/ideas.test.ts`

**Checkpoint**: All seven user stories are independently functional.

---

## Phase 10: Polish & Cross-Cutting Concerns

**Purpose**: Hygiene and verification that spans every story.

- [X] T050 [P] Document all required environment variables and secret-handling conventions in
      `lab-hub/README.md` per Constitution VII; confirm `.gitignore` covers any local `.env` —
      verified: README has a full env-var table + manual-setup steps, `.gitignore` covers `.env*`
      and `.vercel`, full git history scan found no committed secrets
- [X] T051 Catalogue `lab-hub` itself into Pavel-lab as `projects/lab-hub.md` (status, stack,
      `repo:` field) — the reflexive application of FR-016 to this project — done, committed locally
      in Pavel-lab (not yet pushed — left for Pavel to review/push)
- [X] T052 [P] Add graceful error messages for GitHub API rate-limit/failure cases in
      `lab-hub/lib/tasks.ts` and `lab-hub/lib/github-contents.ts` — verified: `describeGitHubError()`
      in `lib/config.ts` handles 403+rate-limit, 401, 404 distinctly, used by both files
- [X] T053 Run `quickstart.md` end to end through all 7 scenarios and record the results — all 7
      verified against the live deployment with real data: US1 overview (including a real GraphQL
      partial-failure bug found and fixed — see `lib/tasks.ts`), US2 idea capture via both the real
      Telegram bot and the API, US3 task listing (via MCP parity), US4 promotion with verified
      idempotency and untouched idea-file content, US5 GitHub webhook delivery confirmed (200) and
      Telegram idea-capture notifications confirmed received, US6 MCP handshake/tools/explicit-confirm
      enforcement, US7 meeting-derived draft idea with correct frontmatter and no task created. All
      test artifacts (3 idea files, 1 GitHub Issue) cleaned up afterward.
- [X] T054 [P] Audit that the UI, the MCP route, and the Telegram/GitHub webhook handlers only ever
      call `lib/*` core functions — no logic duplicated per surface (Constitution IV) — verified: the
      only `fetch()`/`Octokit` references outside `lib/` are client components calling this app's own
      `/api/*` routes, not direct GitHub calls

---

## Dependencies & Execution Order

### Phase Dependencies

- **Setup (Phase 1)**: No dependencies — start immediately
- **Foundational (Phase 2)**: Depends on Setup — BLOCKS all user stories
- **User Stories (Phase 3-9)**: All depend on Foundational completion; within that constraint:
  - US1 and US2 (both P1) have no dependency on each other
  - US3 depends only on Foundational (T009/lib/tasks.ts), not on US1/US2
  - US4 depends on US2's `createIdea()` existing (an idea must exist to promote) but is otherwise
    independent of US1/US3
  - US5 depends on US2 (idea capture) and US4 (promotion) already emitting events to notify about,
    but its own Telegram/webhook plumbing is new work
  - US6 wraps US1/US2/US3/US4's functions in MCP tools — depends on all four being implemented
  - US7 extends US2's `createIdea()` and US6's `create_idea` tool — depends on both
- **Polish (Phase 10)**: Depends on all desired user stories being complete

### Parallel Opportunities

- T002, T003 (Setup) can run in parallel
- T007, T008, T009, T010, T011 (Foundational) can all run in parallel once T006 is done
- Within each user story, tasks marked [P] touch different files and can run in parallel
- US1 and US2 can be implemented in parallel once Foundational is complete
- US3 can be implemented in parallel with US1/US2 once Foundational is complete

---

## Parallel Example: Foundational Phase

```bash
# After T006 (GitHub Contents API client) is done, launch together:
Task: "Implement lib/catalogue.ts readProjects()/readIdeas() in lab-hub/lib/catalogue.ts"
Task: "Implement lib/catalogue-write.ts in lab-hub/lib/catalogue-write.ts"
Task: "Implement lib/tasks.ts in lab-hub/lib/tasks.ts"
Task: "Implement lib/auth.ts in lab-hub/lib/auth.ts"
Task: "Implement lib/notify.ts stub in lab-hub/lib/notify.ts"
```

---

## Implementation Strategy

### MVP First (User Stories 1 & 2 — both P1)

1. Complete Phase 1: Setup
2. Complete Phase 2: Foundational (CRITICAL — blocks all stories)
3. Complete Phase 3 (US1) and Phase 4 (US2)
4. **STOP and VALIDATE**: Run `quickstart.md` Scenarios 1 and 2 independently
5. Deploy/demo if ready — this alone gives Pavel a live status view and a working idea inbox

### Incremental Delivery

1. Setup + Foundational → foundation ready
2. US1 + US2 → validate → deploy (MVP)
3. US3 → validate → deploy (task browsing)
4. US4 → validate → deploy (the idea→task loop closes)
5. US5 → validate → deploy (proactive notifications)
6. US6 → validate → deploy (agent/MCP parity)
7. US7 → validate → deploy (meeting-derived ideas)
8. Polish

Each increment adds value without breaking the stories already shipped.

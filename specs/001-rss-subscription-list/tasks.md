# Tasks: RSS Subscription List

**Input**: Design documents from `specs/001-rss-subscription-list/`
**Prerequisites**: `plan.md`, `spec.md`, `research.md`, `data-model.md`, `contracts/openapi.yaml`, `quickstart.md`

**Tests**: Automated tests are required by the project constitution for observable behavior. Use xUnit with `WebApplicationFactory` for API integration tests and bUnit for UI component tests.

**Organization**: Tasks are grouped by the single prioritized user story so it can be implemented and verified as an independent MVP increment.

## Phase 1: Setup (Shared Infrastructure)

**Purpose**: Create the solution, application projects, and test projects.

- [ ] T001 Create `RSSFeedReader.sln` using the .NET 10 solution template with the legacy `.sln` format.
- [ ] T002 [P] Create the .NET 10 ASP.NET Core Web API project in `backend/RSSFeedReader.Api/RSSFeedReader.Api.csproj`.
- [ ] T003 [P] Create the .NET 10 standalone Blazor WebAssembly project in `frontend/RSSFeedReader.UI/RSSFeedReader.UI.csproj`.
- [ ] T004 [P] Create the .NET 10 xUnit test project in `backend/RSSFeedReader.Api.Tests/RSSFeedReader.Api.Tests.csproj`.
- [ ] T005 [P] Create the .NET 10 xUnit test project in `frontend/RSSFeedReader.UI.Tests/RSSFeedReader.UI.Tests.csproj`.
- [ ] T006 Add all four projects to `RSSFeedReader.sln`, add each test project's reference to its corresponding application project in `backend/RSSFeedReader.Api.Tests/RSSFeedReader.Api.Tests.csproj` and `frontend/RSSFeedReader.UI.Tests/RSSFeedReader.UI.Tests.csproj`, add `Microsoft.AspNetCore.Mvc.Testing` 10.0.x to the API test project, and add `bunit` to the UI test project.

---

## Phase 2: Foundational (Blocking Prerequisites)

**Purpose**: Prepare the UI route and local API/UI configuration before story implementation.

**CRITICAL**: Complete this phase before beginning User Story 1 implementation.

- [ ] T007 Remove `frontend/RSSFeedReader.UI/Pages/Home.razor`, `frontend/RSSFeedReader.UI/Pages/Counter.razor`, and `frontend/RSSFeedReader.UI/Pages/Weather.razor`; remove their links from `frontend/RSSFeedReader.UI/Layout/NavMenu.razor`; create a minimal root-route shell at `frontend/RSSFeedReader.UI/Pages/Subscriptions.razor` and make it the sole `@page "/"` route.
- [ ] T008 Configure the allowed frontend origins in `backend/RSSFeedReader.Api/appsettings.json`, read that allowlist in `backend/RSSFeedReader.Api/Program.cs` without wildcard origins, and set `ApiBaseUrl` to `http://localhost:5151/api/` in `frontend/RSSFeedReader.UI/wwwroot/appsettings.json`.
- [ ] T009 Verify demo-page cleanup and compile the scaffolded projects with `dotnet clean` and `dotnet build` for `frontend/RSSFeedReader.UI/RSSFeedReader.UI.csproj` and `backend/RSSFeedReader.Api/RSSFeedReader.Api.csproj`.

**Checkpoint**: Solution and application projects build; one root UI route exists; the API base URL and CORS allowlist agree on the local UI/API origins.

---

## Phase 3: User Story 1 - Add and View Subscriptions (Priority: P1) - MVP

**Goal**: Let a local user add non-empty feed URL text and immediately see every accepted entry in insertion order, including duplicates, without contacting the submitted addresses.

**Independent Test**: Start with an empty API list, add two values (including a duplicate or non-URL value), and confirm the UI displays all entries in order. Confirm blank input leaves the list unchanged, markup-like values render as text, and a fresh API host starts empty.

### Tests for User Story 1

> Write these tests before implementing the store, endpoints, API client, or add/list UI behavior. Use no live feed URLs or external network access.

- [ ] T010 [P] [US1] Add API integration tests in `backend/RSSFeedReader.Api.Tests/SubscriptionEndpointsTests.cs` for the empty GET response, POST followed by GET, insertion order, duplicate values, acceptance of non-URL text, 400 ProblemDetails for empty/whitespace-only input without mutating prior entries, and an empty list from a fresh `WebApplicationFactory<Program>` host.
- [ ] T011 [P] [US1] Add bUnit tests in `frontend/RSSFeedReader.UI.Tests/SubscriptionsComponentTests.cs` for initial empty rendering, successful add and immediate list update, insertion order and duplicates, ignoring blank input without an API request, preserving the existing list after a failed add, and rendering markup-like input as text rather than markup.

### Implementation for User Story 1

- [ ] T012 [P] [US1] Implement the ordered process-local subscription collection in `backend/RSSFeedReader.Api/Subscriptions/InMemorySubscriptionStore.cs`; reject null, empty, and whitespace-only values without trimming accepted values, allowing duplicates and performing no URL validation or network request.
- [ ] T013 [US1] Implement `GET /api/subscriptions` and `POST /api/subscriptions` in `backend/RSSFeedReader.Api/Program.cs` using `backend/RSSFeedReader.Api/Subscriptions/InMemorySubscriptionStore.cs`; return ordered string arrays, return standard ProblemDetails with HTTP 400 for blank or malformed input, and expose `Program` for `WebApplicationFactory` tests.
- [ ] T014 [P] [US1] Implement `SubscriptionsApiClient` in `frontend/RSSFeedReader.UI/Services/SubscriptionsApiClient.cs` to GET and POST the paths and JSON shapes in `specs/001-rss-subscription-list/contracts/openapi.yaml` using the configured local API base URL only.
- [ ] T015 [US1] Implement the add form, input state, empty state, and ordered subscription list in `frontend/RSSFeedReader.UI/Pages/Subscriptions.razor` using `frontend/RSSFeedReader.UI/Services/SubscriptionsApiClient.cs`; use a text input rather than URL validation, ignore blank input, keep existing entries on request failure, and render values as text.
- [ ] T016 [US1] Run `dotnet test RSSFeedReader.sln` and build `backend/RSSFeedReader.Api/RSSFeedReader.Api.csproj` plus `frontend/RSSFeedReader.UI/RSSFeedReader.UI.csproj`; resolve failures against the User Story 1 tests and contract.

**Checkpoint**: The P1 story works independently through the UI and API, and its automated tests pass without external network access.

---

## Phase 4: Polish & Cross-Cutting Concerns

**Purpose**: Confirm the local workflow and security boundary match the documented MVP.

- [ ] T017 Run every manual scenario in `specs/001-rss-subscription-list/quickstart.md`, verify browser requests go only to the local API and that restarting the API clears the list, and correct any mismatch in `specs/001-rss-subscription-list/quickstart.md`.

---

## Dependencies & Execution Order

### Phase Dependencies

- **Setup (Phase 1)**: T001 is first; T002-T005 can run in parallel after it. T006 depends on all four projects.
- **Foundational (Phase 2)**: Depends on Setup; T007 and T008 touch separate UI/API configuration surfaces and can run in parallel. T009 depends on both and blocks User Story 1.
- **User Story 1 (Phase 3)**: Depends on the complete foundation. T010 and T011 are independent test files and can be authored in parallel before feature behavior is implemented. T012 and T014 are independent implementation files; T013 depends on T012, and T015 depends on T014. T016 depends on all story implementation tasks.
- **Polish (Phase 4)**: T017 depends on the P1 implementation and automated test pass.

### User Story Dependencies

- **User Story 1 (P1)**: No dependency on another story; starts after Phase 2.
- There are no P2/P3 stories in this MVP. Feed fetching, feed-item display, persistence, removal, and polling remain deferred.

### Parallel Opportunities

- Setup: T002, T003, T004, and T005 use separate project directories and can run concurrently after T001.
- Foundation: T007 (Blazor template cleanup) and T008 (API/UI origin configuration) can run concurrently.
- User Story 1 tests: T010 (API integration tests) and T011 (bUnit tests) touch separate test projects.
- User Story 1 implementation: T012 (API store) and T014 (UI API client) can start in parallel. After those finish, T013 (API endpoints) and T015 (UI page) can proceed independently.

## Parallel Example: User Story 1

```text
After Phase 2, author these tests concurrently:
Task T010: API contract and integration coverage in backend/RSSFeedReader.Api.Tests/SubscriptionEndpointsTests.cs
Task T011: UI interaction and rendering coverage in frontend/RSSFeedReader.UI.Tests/SubscriptionsComponentTests.cs

After tests are in place, implement these independent files concurrently:
Task T012: Ordered in-memory store in backend/RSSFeedReader.Api/Subscriptions/InMemorySubscriptionStore.cs
Task T014: API client in frontend/RSSFeedReader.UI/Services/SubscriptionsApiClient.cs

Then implement the dependents concurrently:
Task T013: API endpoints in backend/RSSFeedReader.Api/Program.cs
Task T015: Root subscription page in frontend/RSSFeedReader.UI/Pages/Subscriptions.razor
```

## Implementation Strategy

### MVP First (User Story 1 Only)

1. Complete Setup and Foundational phases.
2. Write the API and UI tests for User Story 1.
3. Implement the process-local API store, endpoints, UI API client, and root page.
4. Run `dotnet test RSSFeedReader.sln` and the quickstart scenarios.
5. Stop at the approved MVP boundary; do not implement deferred feed or persistence features.

### Incremental Delivery

1. Complete Setup and Foundational phases, then verify the API/UI origin configuration.
2. Complete User Story 1 and verify it independently through tests and the quickstart.
3. Deliver the add/list MVP; schedule any deferred capability as a separately approved feature.

## Notes

- Every task uses the required checkbox, sequential task ID, optional parallel marker, user-story label when applicable, and concrete file path.
- User Story 1 test coverage is mandatory under the constitution; no live RSS/Atom feed is needed for tests.
- Keep submitted values as text. Do not add URL validation, outbound feed requests, feed parsing, persistence, removal, or background work.
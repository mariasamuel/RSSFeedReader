# Implementation Plan: RSS Subscription List

**Branch**: `001-rss-subscription-list` | **Date**: 2026-10-26 | **Spec**: [spec.md](spec.md)
**Input**: [spec.md](spec.md), `StakeholderDocuments/ProjectGoals.md`, and `StakeholderDocuments/TechStack.md`

**Note**: This template is filled in by the `/speckit.plan` command. See `.specify/templates/plan-template.md` for the execution workflow.

## Summary

Deliver the local single-user MVP for adding RSS/Atom subscription URLs and
displaying them in insertion order. The ASP.NET Core API owns the in-memory list;
the Blazor WebAssembly UI retrieves and adds entries through the API. Empty or
whitespace-only values are rejected, while other values are stored as entered.
No request is made to a submitted feed URL. See [research.md](research.md) for
runtime, API, and test-framework decisions.

## Technical Context

<!--
  ACTION REQUIRED: Replace the content in this section with the technical details
  for the project. The structure here is presented in advisory capacity to guide
  the iteration process.
-->

**Language/Version**: C# 14 / .NET 10 LTS  
**Primary Dependencies**: ASP.NET Core Web API, Blazor WebAssembly, xUnit, `Microsoft.AspNetCore.Mvc.Testing`, and bUnit  
**Storage**: Process-local in-memory ordered subscription list; no persistence  
**Testing**: `dotnet test`; xUnit API integration tests with `WebApplicationFactory` and bUnit UI component tests  
**Target Platform**: Local development on Windows, macOS, and Linux; browser UI served by Blazor WebAssembly  
**Project Type**: Web application with separate API and UI projects  
**Performance Goals**: A successful local add appears in the UI within 2 seconds, per SC-001  
**Constraints**: Single local user; no feed URL requests, feed parsing, persistence, removal, or background refresh; API and UI use explicitly configured local origins  
**Scale/Scope**: One MVP subscription-management flow; small local in-memory list, with no production scale target

## Constitution Check

*GATE: Passed before Phase 0 research; re-checked after Phase 1 design.*

- **Scope: PASS.** The plan implements only adding and displaying subscriptions.
  Fetching, parsing, item display, persistence, removal, and background refresh
  remain deferred.
- **Security: PASS.** Submitted values are untrusted text, displayed as text, and
  never dereferenced. The API is for local use; no third-party feed content enters
  this phase.
- **Boundaries: PASS.** The API owns list state and exposes a small JSON contract;
  the UI handles input and presentation. No database, parsing library, or extra
  architecture layer is introduced.
- **Verification: PASS.** API integration tests cover an empty initial list,
  add/list behavior, acceptance of non-URL text, blank-input rejection without
  mutation, duplicate/order preservation, response shape, and a fresh host
  starting empty. bUnit tests cover UI rendering (including markup-like input as
  text), blank input, duplicate entries, and API-client interaction. Tests use no
  live feed URLs or external network access.
- **Cross-platform configuration: PASS.** Both projects target .NET 10 and use
  standard .NET commands on Windows, macOS, and Linux. The UI API base URL and
  API CORS origins are explicit configuration values; local defaults are recorded
  in [quickstart.md](quickstart.md).

## Project Structure

### Documentation (this feature)

```text
specs/[###-feature]/
├── plan.md              # This file (/speckit.plan command output)
├── research.md          # Phase 0 output (/speckit.plan command)
├── data-model.md        # Phase 1 output (/speckit.plan command)
├── quickstart.md        # Phase 1 output (/speckit.plan command)
├── contracts/           # Phase 1 output (/speckit.plan command)
└── tasks.md             # Phase 2 output (/speckit.tasks command - NOT created by /speckit.plan)
```

### Source Code (repository root)
<!--
  ACTION REQUIRED: Replace the placeholder tree below with the concrete layout
  for this feature. Delete unused options and expand the chosen structure with
  real paths (e.g., apps/admin, packages/something). The delivered plan must
  not include Option labels.
-->

```text
backend/
├── RSSFeedReader.Api/
│   ├── Program.cs
│   ├── Subscriptions/
│   │   └── InMemorySubscriptionStore.cs
│   └── RSSFeedReader.Api.csproj
└── RSSFeedReader.Api.Tests/
  ├── SubscriptionEndpointsTests.cs
  └── RSSFeedReader.Api.Tests.csproj

frontend/
├── RSSFeedReader.UI/
│   ├── Pages/
│   │   └── Subscriptions.razor
│   ├── Services/
│   │   └── SubscriptionsApiClient.cs
│   ├── wwwroot/
│   │   └── appsettings.json
│   └── RSSFeedReader.UI.csproj
└── RSSFeedReader.UI.Tests/
  ├── SubscriptionsComponentTests.cs
  └── RSSFeedReader.UI.Tests.csproj

RSSFeedReader.sln
```

**Structure Decision**: Use two runtime projects matching the stakeholder
backend/UI split and one test project for each boundary. Keep the in-memory store
in the API project and the HTTP client in the UI project. No source directories
exist yet; this is the intended structure for implementation.

## Complexity Tracking

> No constitution violations or exceptions are required.

| Violation | Why Needed | Simpler Alternative Rejected Because |
|-----------|------------|-------------------------------------|
| None | N/A | N/A |

# Research: RSS Subscription List

## Runtime and Framework

**Decision**: Target .NET 10 LTS for both the ASP.NET Core API and Blazor WebAssembly application.

**Rationale**: The stakeholder documents already select ASP.NET Core Web API and Blazor WebAssembly but do not specify a target framework. Microsoft's support policy lists .NET 10 as the active LTS release on 2026-10-26, supported through November 14, 2028. The MVP needs no preview-only capabilities.

**Alternatives considered**:
- .NET 8 LTS: still supported on the project date, but in maintenance support and scheduled to reach end of support on November 10, 2026.
- .NET 11: a release candidate on the project date, so it is not appropriate for this stable MVP target.

**Source**: [Microsoft .NET Support Policy](https://dotnet.microsoft.com/en-us/platform/support/policy/dotnet-core) (updated October 25, 2026).

## API and UI Integration

**Decision**: Keep subscription entry and list state behind the ASP.NET Core API. The Blazor UI uses the API for list retrieval and submission. Use `GET /api/subscriptions` and `POST /api/subscriptions`; the API owns the single in-memory list for the current process.

**Rationale**: This follows the selected backend/frontend responsibilities while keeping the feature small. The API can be tested independently and the UI does not own a competing copy of the source-of-truth state. No feed URL is contacted; the API only stores and returns submitted text.

**Alternatives considered**:
- Store the list only in the browser: rejected because the stakeholder tech stack assigns subscription management to the backend API.
- Add a database or browser storage: rejected because persistence is explicitly deferred.
- Fetch/parse feed content: rejected because it is outside the MVP and adds an unnecessary network trust boundary.

## Automated Testing

**Decision**: Use xUnit and the standard `dotnet test` workflow. Cover API behavior with ASP.NET Core `WebApplicationFactory` integration tests, and cover list rendering, input handling, and the UI's API interaction with bUnit component tests.

**Rationale**: These checks cover both observable sides of the feature without requiring a live feed or a browser automation stack. ASP.NET Core's official guidance uses `WebApplicationFactory` and `Microsoft.AspNetCore.Mvc.Testing` for HTTP-level integration tests. bUnit supports xUnit and can render Blazor components and assert their markup and interactions.

**Alternatives considered**:
- Manual-only verification: rejected because the constitution requires automated tests for observable behavior.
- Playwright browser tests: useful for full end-to-end browser coverage, but disproportionate for this single form/list MVP; retain local manual smoke checks in the quickstart.
- MSTest or NUnit: viable, but xUnit is used in Microsoft's ASP.NET Core integration-test examples and is sufficient for both test projects.

**Sources**: [Microsoft Learn: Integration tests in ASP.NET Core](https://learn.microsoft.com/en-us/aspnet/core/test/integration-tests?view=aspnetcore-10.0); [bUnit: Writing tests for Blazor components](https://bunit.dev/docs/getting-started/writing-tests.html); [Microsoft Learn: Testing with `dotnet test`](https://learn.microsoft.com/en-us/dotnet/core/testing/unit-testing-with-dotnet-test).

## Storage and Scope

**Decision**: Keep ordered subscription values in a process-local in-memory collection. Reject only null, empty, or whitespace-only input; accept all other values as entered, including duplicates and strings that are not syntactically valid URLs. Render values as text. Make no requests to subscription addresses.

**Rationale**: This is the smallest behavior that satisfies the approved MVP while preserving the security boundary: user text is stored and displayed, never dereferenced or interpreted as markup. Restarting the API clears the list, as persistence is deferred.

**Alternatives considered**:
- Validate or normalize URL syntax: rejected by the stakeholder requirements, which explicitly defer URL validation.
- Deduplicate subscriptions: rejected because the requirements permit duplicates and provide no removal workflow.
- Persist subscriptions: deferred to a later phase.
<!--
Sync Impact Report
Version change: uninitialized template -> 1.0.0 (initial constitution)
Modified principles: five template placeholders -> MVP Scope Discipline, Secure
Input Handling, Maintainable Boundaries, Risk-Based Verification, Cross-Platform
Configuration
Added sections: Project Constraints, Development Workflow
Removed sections: none
Templates: ✅ .specify/templates/plan-template.md; ✅ .specify/templates/spec-template.md;
✅ .specify/templates/tasks-template.md; ✅ .github/prompts/*.prompt.md reviewed (no updates needed)
Follow-up TODO: Confirm the original ratification date.
-->
# RSS Feed Reader Constitution

## Core Principles

### I. MVP Scope Discipline
Deliver only the capabilities approved for the current phase. The MVP accepts a
feed URL, stores the subscription in memory, and displays the subscription list;
it MUST NOT fetch or parse feeds, persist data, remove subscriptions, or add
background polling unless a later phase explicitly authorizes those capabilities.
Specs and plans MUST identify deferred work rather than silently expanding scope.

### II. Secure Input and Content Handling
Treat user-provided URLs and all future network responses as untrusted. The MVP
MUST NOT make network requests for subscriptions. Any phase that adds fetching
MUST constrain outbound requests to prevent access to loopback, private, link-local,
and otherwise disallowed destinations; handle redirects under the same policy;
bound response size and duration; and parse supported RSS/Atom formats safely.
Feed-provided HTML MUST NOT be rendered as trusted markup; sanitize it before any
rendering. Do not expose secrets or sensitive response details in logs or errors.

### III. Maintainable Boundaries
Keep the ASP.NET Core API responsible for subscription and feed operations and the
Blazor WebAssembly UI responsible for user interaction. Keep contracts explicit,
use the existing .NET stack, and add dependencies or architectural layers only when
an approved requirement needs them. Changes MUST preserve independently testable
behavior at the API and UI boundaries.

### IV. Risk-Based Verification
Every change to observable behavior MUST have automated tests at the narrowest
useful layer. Verify subscription addition and list updates; cover API contracts
when they change, and add integration tests for cross-component or network
behavior. Security controls MUST have tests for allowed and rejected cases.
Run relevant builds and tests before declaring a change complete; document any
unavailable verification.

### V. Cross-Platform Configuration
The application MUST remain runnable and testable on Windows, macOS, and Linux.
Configure API addresses and allowed frontend origins through environment-appropriate
configuration, not hardcoded assumptions in application logic. Plans MUST account
for the separate API and UI origins and verify their configuration when changed.

## Project Constraints

The product is a local, single-user proof of concept. The current MVP consists of
adding a subscription by URL and displaying the in-memory list. Do not introduce
feed fetching, parsing, persistence, authentication, background work, or other
post-MVP capabilities without an explicit phase change. Use ASP.NET Core Web API
and Blazor WebAssembly; keep API base URL and CORS origins consistent with local
launch configuration. Features that accept or process network content MUST include
their trust boundaries and mitigations in the design.

## Development Workflow

Before implementation, plans MUST pass the Constitution Check and document the
phase scope, API/UI responsibilities, security impact, and verification approach.
For Blazor template initialization, remove conflicting demonstration pages and
verify routing before implementing feature UI. Before completion, run the
appropriate automated tests and builds, verify configuration affected by the
change, and review the diff for scope and security compliance.

## Governance

This constitution governs project decisions and takes precedence over conflicting
feature plans or implementation guidance. Amendments MUST state their rationale,
impact on existing plans and templates, and any required migration or follow-up.
Use semantic versioning: MAJOR for incompatible principle changes or removals,
MINOR for added principles or materially expanded requirements, and PATCH for
clarifications that do not change obligations. Reviewers MUST verify scope,
security, maintainability, and verification compliance for affected changes.
Plans and generated tasks MUST identify and justify any proposed exception before
implementation; unresolved exceptions block completion.

**Version**: 1.0.0 | **Ratified**: TODO(RATIFICATION_DATE): original adoption date unknown | **Last Amended**: 2026-10-26

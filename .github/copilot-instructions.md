# RSSFeedReader Development Guidelines

Auto-generated from all feature plans. Last updated: 2026-10-26

## Active Technologies

- C# 14 / .NET 10 LTS + ASP.NET Core Web API, Blazor WebAssembly, xUnit, `Microsoft.AspNetCore.Mvc.Testing`, and bUnit (001-rss-subscription-list)

## Project Structure

```text
backend/
├── RSSFeedReader.Api/
│   ├── Program.cs
│   └── Subscriptions/
│       └── InMemorySubscriptionStore.cs
└── RSSFeedReader.Api.Tests/

frontend/
├── RSSFeedReader.UI/
│   ├── Pages/Subscriptions.razor
│   └── Services/SubscriptionsApiClient.cs
└── RSSFeedReader.UI.Tests/

specs/001-rss-subscription-list/
└── contracts/openapi.yaml
```

## Commands

```powershell
dotnet restore RSSFeedReader.sln
dotnet test RSSFeedReader.sln
dotnet run --project backend/RSSFeedReader.Api
dotnet run --project frontend/RSSFeedReader.UI
```

## Code Style

C# 14 / .NET 10 LTS: Follow standard conventions

## Recent Changes

- 001-rss-subscription-list: Added C# 14 / .NET 10 LTS + ASP.NET Core Web API, Blazor WebAssembly, xUnit, `Microsoft.AspNetCore.Mvc.Testing`, and bUnit

<!-- MANUAL ADDITIONS START -->
<!-- MANUAL ADDITIONS END -->

# Quickstart: RSS Subscription List

## Prerequisites

- .NET 10 SDK with the latest available servicing update.
- A supported browser.
- No database, feed service, or external feed access is required.

## Restore and Test

From the repository root:

```powershell
dotnet restore RSSFeedReader.sln
dotnet test RSSFeedReader.sln
```

The API test project uses xUnit and `WebApplicationFactory`; the UI test project
uses xUnit and bUnit. All tests run without contacting feed URLs.

## Run Locally

Start the API in one terminal:

```powershell
dotnet run --project backend/RSSFeedReader.Api --urls http://localhost:5151
```

Start the UI in a second terminal:

```powershell
dotnet run --project frontend/RSSFeedReader.UI --urls http://localhost:5213
```

Open `http://localhost:5213`. The UI's `ApiBaseUrl` configuration must point to
`http://localhost:5151/api/`. The API CORS policy must allow the exact UI origin
`http://localhost:5213`; do not use a wildcard origin. If HTTPS launch profiles
are used instead, configure the matching HTTPS UI origin explicitly.

## Verify the MVP

1. Confirm the subscription view starts with an empty list.
2. Add a normal feed URL and confirm it appears immediately.
3. Add a second value and confirm insertion order is preserved.
4. Add the same value twice and confirm both entries appear.
5. Submit empty and whitespace-only values and confirm the list is unchanged.
6. Add a non-empty value that is not a valid URL and confirm it is listed without
   a request to that address.
7. Confirm the browser makes requests only to the local API, never to submitted
   feed URLs.
8. Restart the API and confirm the list is empty after it starts again.

The API defaults to `http://localhost:5151`; the UI defaults to
`http://localhost:5213`. Keep the API base URL and allowed CORS origin aligned
with any local port changes. These defaults are for local single-user development,
not a public deployment configuration.
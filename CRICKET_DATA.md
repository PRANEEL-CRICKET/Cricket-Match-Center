# Licensed cricket data integration

The browser-facing adapter is `src/services/cricketData.ts`. It expects a
same-origin backend proxy under `/api/cricket`; licensed API credentials must
remain on that backend and must not use a `VITE_` environment variable.

## Required backend configuration

Configure these as deployment secrets once a provider is selected:

- `CRICKET_API_BASE_URL`
- `CRICKET_API_KEY`

Do not commit either value.

## Normalized endpoints

The proxy should expose:

- `GET /api/cricket/matches?season=2026&competition={id}&team={id}`
- `GET /api/cricket/matches/{matchId}`
- `GET /api/cricket/competitions/{id}/standings?season=2026`

Supported competition IDs are `ipl`, `bbl`, `cpl`, `sa20`, `hundred`,
`smash`, and `icc`.

Provider responses should be normalized to the TypeScript contracts exported
by `src/services/cricketData.ts`. Match-detail responses include innings
scorecards and ball-by-ball deliveries.

## Provider mapping checklist

1. Map each licensed tournament/series ID to the local competition ID.
2. Request only published 2026 fixtures and completed/live results.
3. Preserve provider match and team IDs; do not derive IDs from display names.
4. Convert provider timestamps to ISO 8601 and format them only in the UI.
5. Distinguish scheduled, live, complete, and abandoned matches.
6. Cache historical results and respect provider rate limits.
7. Return attribution and licensing fields if the provider requires them.
8. Never substitute generated commentary or standings when a request fails.

The existing preview data should remain visible until the proxy is configured.
Once credentials and provider documentation are available, map the provider
payload on the server and replace the preview selectors with the adapter calls.

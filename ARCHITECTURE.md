# Architecture and maintenance notes

## Repository layout

| App | Entry point | Main dependencies | Persistence |
| --- | --- | --- | --- |
| Restaurant Chooser | `restaurant-chooser/index.html` | Supabase REST API, Google Fonts | Supabase `restaurants` table |
| Weight Race | `weight-race/index.html` | Supabase JS v2, Chart.js | Supabase `settings` and `entries` tables; browser identity in `localStorage` |
| Travel Map | `travel-map/index.html` | Supabase JS v2, Leaflet | Supabase `travel_cards`, Realtime, Storage, and a browser cache |

All three are single-file static applications. There is no build step and no server-side code in this repository.

## Data-access boundaries

### Restaurant Chooser

- Reads and writes `restaurants` through the Supabase REST endpoint.
- Uses a public/publishable browser key.
- The current frontend does not establish a Supabase user session before write requests. Therefore, safe edit restriction depends entirely on the database policies actually deployed. Review this before relying on “Evelyn and husband only” access.

### Weight Race

- Reads `settings` and `entries` with `supabase-js`.
- Upserts and deletes settings and upserts daily entries.
- Stores the selected participant name in `localStorage`; this is a UI preference, not authentication or authorization.
- Uses a separate Supabase project from the other two apps.

### Travel Map

- Reads, upserts, deletes, and reorders `travel_cards`.
- Subscribes to Realtime changes on `travel_cards`.
- Uploads photos to the `travel-photos` Storage bucket and uses public URLs.
- Uses passwordless email authentication and checks an allowlist in the browser. The allowlist improves the user experience, but database and Storage RLS policies must independently enforce access.
- Caches card data in `localStorage` for resilience.

## Security rules to preserve

1. Never place a Supabase `service_role` or secret key in these static files.
2. Enable RLS on every table exposed through the Data API.
3. Authorize writes in RLS using authenticated user IDs or trusted app metadata; a frontend email allowlist alone is not security.
4. Apply Storage policies to uploads, updates, reads, and deletes as appropriate.
5. Keep production database changes as versioned SQL migrations in a future `supabase/` directory.

## Deployment

The Pages workflow uploads this repository as a static artifact and deploys it. Each app uses relative placement under its folder, so the same source works under the `/personalapps/<app>/` paths.

## Recommended next improvements

1. Audit and document the live RLS and Storage policies for both Supabase projects.
2. Add authentication to Restaurant Chooser and enforce its two-person editor rule in RLS.
3. Add authentication/authorization to Weight Race; participant selection currently does not verify identity.
4. Move repeated configuration and data helpers out of the large HTML files into modules.
5. Add automated smoke tests for app loading, database reads, and authorized/unauthorized writes.

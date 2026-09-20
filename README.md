# Evelyn's Apps

This repository contains three personal web apps deployed together with GitHub Pages:

- `restaurant-chooser/` — restaurant list, filters, random choice, and Supabase-backed editing
- `weight-race/` — Evelyn and Damian's weight challenge tracker
- `travel-map/` — collaborative travel-card map with photo uploads

## Deployment

GitHub Pages deploys the repository root through `.github/workflows/pages.yml`.

Expected URLs:

- `https://evelynphang.github.io/evelyn-apps/`
- `https://evelynphang.github.io/evelyn-apps/restaurant-chooser/`
- `https://evelynphang.github.io/evelyn-apps/weight-race/`
- `https://evelynphang.github.io/evelyn-apps/travel-map/`

## Data services

The apps remain connected to their existing Supabase projects. Browser-safe publishable keys are present in the static frontend. Authorization must be enforced by Supabase Row Level Security (RLS), not by keeping those keys secret.

See [ARCHITECTURE.md](ARCHITECTURE.md) for the code and data-access map.

# investments-backend

Base Express + TypeScript backend template: cookie-based JWT auth with `admin` / `editor` / `viewer` roles, and a single protected `GET /diagnostic` endpoint used as a reference for adding new endpoints later.

## Setup

1. Make sure `DATABASE_PUBLIC_URL` in `.env` points at the Railway Postgres service for this app.
2. `npm install`
3. `npm run seed` — creates the tables (if they don't exist yet) and the first admin user, using `SEED_ADMIN_EMAIL`/`SEED_ADMIN_PASSWORD` from `.env`. This account is the only one that can create further users (`POST /users-admin/create-user`), so treat its password as a real credential, not a throwaway. `npm run unseed` removes it by email if you ever need to reset and reseed.
4. `npm run dev`

Table sync isn't automatic on every server start — `npm run db:sync` runs it on its own, e.g. after you add or change a model.

## Try it

    curl -c cookie.txt -X POST http://localhost:3500/users/login \
      -H "Content-Type: application/json" \
      -d '{"email":"admin@investments-backend.com","password":"<SEED_ADMIN_PASSWORD>"}'

    curl -b cookie.txt http://localhost:3500/diagnostic

## Adding new endpoints

Follow the `diagnostic` pattern: model (if needed) → controller → route → mount in `app.ts` → gate with `verifyRoles([...])`. If you add a model, add its import to `services/synch.ts` and run `npm run db:sync`.

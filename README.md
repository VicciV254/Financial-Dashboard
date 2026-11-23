# Financial Dashboard

An invoice and customer dashboard for Acme, a fictional company: the Next.js app from the public [Next.js Learn App Router course](https://nextjs.org/learn/dashboard-app), for developers who want to run or study it. Status: a learning project whose code stays close to the course's finished example.

## Overview

After you sign in, the dashboard shows collected and pending invoice totals, invoice and customer counts, a 12-month revenue chart and the five latest invoices. You can search invoices, page through them six at a time, and create, edit or delete them. The customers page lists each customer's invoice count and pending and paid totals, with search. Data lives in PostgreSQL, and sign-in uses email and password through NextAuth.js.

## Getting started

### Prerequisites

| Tool | Version |
| --- | --- |
| Node.js | `^20.19.0`, `^22.13.0` or `>=24`: the strictest engine range in `pnpm-lock.yaml`, from ESLint 10.7.0 |
| pnpm | Not pinned; `pnpm-lock.yaml` is lockfile format 9.0 |
| PostgreSQL | Not pinned; it must accept SSL connections, because the app always connects with `ssl: 'require'` |

### Install and configure

Run every command from `nextjs-dashboard/`:

```bash
cd nextjs-dashboard
pnpm install
```

No `.env.example` is tracked, so create `nextjs-dashboard/.env.local` (Git ignores it) with the two variables the app needs:

```dotenv
POSTGRES_URL=postgres://YOUR_USER:YOUR_PASSWORD@YOUR_HOST/YOUR_DATABASE
AUTH_SECRET=YOUR_RANDOM_SECRET
```

Generate the `AUTH_SECRET` value with `openssl rand -base64 33`, as the [deployment guide for NextAuth.js](https://authjs.dev/getting-started/deployment#auth_secret) suggests.

### Run and seed

```bash
pnpm dev
```

1. Open <http://localhost:3000/seed> once. It creates the `uuid-ossp` extension and the `users`, `customers`, `invoices` and `revenue` tables, loads sample data and returns `{"message":"Database seeded successfully"}`; each later visit inserts the sample invoices again. If it returns HTTP 500 with an `error` object, check `POSTGRES_URL` and that your database user can create the extension.
2. Open <http://localhost:3000/login> and sign in with the sample user in `app/lib/placeholder-data.ts`. You land on `/dashboard`; a wrong email or password shows "Invalid credentials."

The revenue chart appears about three seconds after the rest of the dashboard, because `fetchRevenue` in `app/lib/data.ts` waits on purpose.

## Verification

```bash
pnpm lint    # ESLint with eslint-config-next's core-web-vitals rules
pnpm build   # optimized build of the app (next build)
```

No test suite is defined: `package.json` has no `test` script.

## Tech stack

Versions are the ones locked in `nextjs-dashboard/pnpm-lock.yaml`.

- Next.js 16.2.10 (App Router), React 19.2.7, TypeScript 5.7.3
- NextAuth.js 5.0.0-beta.31 (credentials sign-in) with bcrypt 5.1.1
- PostgreSQL through the `postgres` client 3.4.7
- Tailwind CSS 3.4.17 with `@tailwindcss/forms`, and Heroicons
- Zod 3.25.76 for form validation
- ESLint 10.7.0 with `eslint-config-next` 16.2.10

## Project structure

```text
nextjs-dashboard/          # the Next.js app
├── app/
│   ├── dashboard/         # signed-in pages: overview, invoices, customers
│   ├── lib/               # SQL queries, server actions, types, sample data
│   ├── login/             # sign-in page
│   ├── query/route.ts     # GET /query: the course's example SQL query
│   ├── seed/route.ts      # GET /seed: creates the tables and loads sample data
│   ├── ui/                # React components, fonts and styles
│   └── page.tsx           # landing page
├── public/                # static images: customer avatars, hero images, favicon
├── auth.config.ts         # sign-in page and the rule that /dashboard needs sign-in
├── auth.ts                # credentials sign-in against the users table
└── proxy.ts               # applies the sign-in rule to incoming requests
```

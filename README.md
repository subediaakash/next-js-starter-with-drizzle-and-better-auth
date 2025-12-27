# Next.js Starter (Drizzle + Better Auth)

A Next.js starter using Drizzle ORM (PostgreSQL) and Better Auth.

## Prerequisites

- Node.js 18+ (or Bun if you prefer)
- PostgreSQL running locally or remotely
- A `.env` file at the project root

## Environment Variables

Create `.env` with:

```
DATABASE_URL=postgresql://USER:PASS@HOST:PORT/DB_NAME
BETTER_AUTH_SECRET=your-random-secret-string
BETTER_AUTH_URL=http://localhost:3000
```

Notes:
- BETTER_AUTH_SECRET should be a long random string.
- BETTER_AUTH_URL must match your app URL (http://localhost:3000 in dev).

## Install Dependencies

Linux terminal:

```
npm install
```

If you use Bun:

```
bun install
```

## Drizzle Setup

Check your Drizzle config:

- Config file: `drizzle.config.ts`
- Schema: `./drizzle/src/db/schemas/schema.ts`
- Output: `./drizzle/migrations` (or `./drizzle` if you prefer everything in one folder)

Generate SQL from schema:

```
npx drizzle-kit generate
```

Run migrations:

```
npx drizzle-kit migrate
```

## Better Auth Setup

Your auth is configured in `app/lib/auth.ts` using the Drizzle adapter:

```ts
import { betterAuth } from "better-auth";
import { drizzleAdapter } from "better-auth/adapters/drizzle";
import { db } from "@/drizzle/src";

export const auth = betterAuth({
  database: drizzleAdapter(db, { provider: "pg" }),
  emailAndPassword: { enabled: true },
});
```

If you run on Node/Next.js, ensure your Drizzle client uses a Node driver (not Bun):

```ts
// filepath: /home/aakash/Documents/Geeks/next-js-starter/drizzle/src/index.ts
import { drizzle } from "drizzle-orm/node-postgres";
import { Pool } from "pg";

const pool = new Pool({ connectionString: process.env.DATABASE_URL });
export const db = drizzle(pool);
```

Generate Better Auth artifacts:

```
npx @better-auth/cli generate
```

If you prefer Bun, make sure Bun is in PATH and use:

```
bunx @better-auth/cli generate
```

## Run the App

Start the dev server:

```
npm run dev
```

Open http://localhost:3000.

## Common Issues

- “Cannot find module 'bun'” when generating: you’re loading a Bun driver under Node. Switch Drizzle to `node-postgres` (as above) or run the command with Bun (`bunx`) and ensure Bun is in PATH.
- Missing DATABASE_URL: confirm `.env` is present and loaded. Next.js loads `.env` automatically; drizzle-kit loads via `dotenv/config` in your `package.json` scripts.

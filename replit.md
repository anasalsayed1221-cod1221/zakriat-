# ذكريات عروس

متجر طلبات قص الفينيل كتر — يتيح للزبائن تصفح المنتجات، اختيار القياس واللون، وإدخال نص مخصص لطلبات القص.

## Run & Operate

- `pnpm --filter @workspace/api-server run dev` — run the API server (port 8080)
- `pnpm --filter @workspace/store run dev` — run the store frontend (port 24964)
- `pnpm run typecheck` — full typecheck across all packages
- `pnpm run build` — typecheck + build all packages
- `pnpm --filter @workspace/api-spec run codegen` — regenerate API hooks and Zod schemas from the OpenAPI spec
- `pnpm --filter @workspace/db run push` — push DB schema changes (dev only)
- Required env: `DATABASE_URL` — Postgres connection string
- Required env: `SESSION_SECRET` — Session secret for admin auth
- Required env: `ADMIN_PASSWORD` — Admin panel password (default: admin2024)

## Stack

- pnpm workspaces, Node.js 24, TypeScript 5.9
- Frontend: React + Vite + Tailwind (RTL Arabic)
- API: Express 5
- DB: PostgreSQL + Drizzle ORM
- Validation: Zod (`zod/v4`), `drizzle-zod`
- API codegen: Orval (from OpenAPI spec)
- Build: esbuild (CJS bundle)
- File uploads: multer → artifacts/api-server/uploads/

## Where things live

- `lib/api-spec/openapi.yaml` — API contract source of truth
- `lib/db/src/schema/` — DB tables: collections, products, orders
- `artifacts/api-server/src/routes/` — Express route handlers
- `artifacts/api-server/uploads/` — Uploaded images (served at /api/uploads/)
- `artifacts/store/src/` — React frontend (Arabic RTL)

## Architecture decisions

- Currency: ل.س (Syrian Pound), no tax display anywhere
- Admin auth: session-based with express-session + ADMIN_PASSWORD env var
- Image upload: multer multipart → /api/upload → returns { url } served from /api/uploads/
- Product colors stored as JSONB array of { name, imageUrl }
- Product images stored as JSONB array of strings (up to 4)
- Orders stored as JSONB items snapshot (not FK joins) for historical accuracy

## Product

- **Customer side**: Home page with collections, product browse with collection filter, product detail (gallery, size selector, color selector, custom text), checkout, order lookup board
- **Admin side**: Protected by password login, dashboard with order stats, order management (status updates), product CRUD (image upload, sizes, colors), collection CRUD with image

## User preferences

- Language: Arabic (RTL)
- Currency: ل.س
- No tax display
- Admin password: stored in ADMIN_PASSWORD env var (default: admin2024)

## Gotchas

- Always run `pnpm run typecheck:libs` before checking artifact typechecks after changing `lib/*`
- `price` field in Drizzle is `numeric` → stored as string, cast with `Number()` in responses
- JSONB fields (images, sizes, colors) need `as never` cast when inserting
- Admin routes check `req.session.isAdmin` — express-session must be mounted before routes in app.ts

## Pointers

- See the `pnpm-workspace` skill for workspace structure, TypeScript setup, and package details

# Solana Sniper Bot

A secure operations dashboard for a Solana token-monitoring bot, prepared to connect to an independently hosted Go API and WebSocket service.

## Run & Operate

- `pnpm --filter @workspace/solana-sniper-bot run dev` — run the web dashboard
- `pnpm --filter @workspace/solana-sniper-bot run typecheck` — check the dashboard
- `pnpm --filter @workspace/api-server run dev` — run the shared API server (health endpoint and Clerk proxy)
- `pnpm --filter @workspace/api-server run typecheck` — check the shared API server
- `pnpm --filter @workspace/api-spec run codegen` — regenerate API hooks and schemas from OpenAPI
- `pnpm run typecheck` — check workspace libraries and artifacts
- Optional frontend setting: `VITE_API_BASE_URL`; leave unset for same-origin `/api` routes or set it to the Go server origin (the contract adds `/api`).
- Authentication keys are managed through Replit Clerk Auth; never add them to source files or expose server keys in the browser.

## Stack

- pnpm workspaces, Node.js 24, TypeScript 5.9
- Frontend: React, TypeScript, Vite, Tailwind CSS, Wouter, TanStack Query, Recharts, Lucide
- Auth: Replit-managed Clerk
- Shared project API: Express 5, used for health and Clerk Frontend API proxy
- Bot API and event WebSocket: separate Go services, not implemented by this dashboard
- API contract/codegen: OpenAPI and Orval

## Where things live

- `artifacts/solana-sniper-bot/src/` — dashboard routes, services, hooks, and UI components
- `lib/api-spec/openapi.yaml` — HTTP contract for the external Go service
- `lib/api-client-react/src/generated/` — generated React Query hooks and shared types
- `artifacts/api-server/src/middlewares/clerkProxyMiddleware.ts` — Clerk proxy for published sign-in
- `artifacts/solana-sniper-bot/README.md` — local setup and Go API integration details

## Architecture decisions
- The dashboard is a client for a separate Go backend; the shared Express service is not the trading backend.
- Balances, transaction states, and results are backend-owned. Unknown values remain unknown; the browser never synthesizes blockchain results.
- Trading, signing, and private-key custody remain exclusively in the backend.
- Real-mode requests, strategy updates, RPC operations, and manual sales go through backend mutations and reflect returned results.

## Product
- Authenticated dashboard for bot health, token monitoring, positions, trade history, RPC management, strategy settings, public wallet status, and logs.
- OpenAPI-generated HTTP hooks and a reconnecting WebSocket client prepare the UI for real backend updates.

## User preferences
- Keep trade execution/signing, private keys, seed phrases, and credentials out of the browser.
- Do not present demonstration data as live data. Show “Aguardando conexão com o back-end” or “Não implementado” until actual server data is available.

## Gotchas
- Update `lib/api-spec/openapi.yaml` before consuming generated client types, then run `pnpm --filter @workspace/api-spec run codegen`.
- The Go API must enforce authentication and validate all mutations independently; a frontend route guard is not server authorization.
- The Go API must redact RPC credentials in returned URLs and never return wallet secrets.

## Pointers

- See the `pnpm-workspace` skill for workspace structure, TypeScript setup, and package details

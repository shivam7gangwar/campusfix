# CampusFix — Campus Issue Management Platform

A mobile-first campus service desk for reporting, prioritizing, assigning, tracking, and learning from facilities issues. The implementation uses the initialized Manus web-db-user stack: React/Vite, Express, tRPC, Drizzle/MySQL, Manus OAuth, and managed object storage.

## Run and verify

- `pnpm dev` — start the development server (configured port 3000).
- `pnpm check` — run the TypeScript check.
- `pnpm test` — run focused CampusFix business-rule tests.
- `pnpm build` — create the client build and server bundle.

The project requires its configured Manus OAuth, managed MySQL database, and managed storage services. The Preview serves the same app origin; public HTTPS sessions use `SameSite=None; Secure` cookies for its embedded frame.

## First-time campus setup

1. Apply the additive database schema: `pnpm db:push` (or apply committed migrations with `pnpm db:migrate`).
2. Run `pnpm db:seed` to add the PRD's standard issue categories and editable starting SLA targets (Critical 4h, High 24h, Medium 72h, Low 168h). This safe default does **not** create locations or issue records.
3. Sign in with the configured Manus OAuth identity. The configured project owner becomes the initial Admin; there is no self-service role switch.
4. As Admin, add the real campus Block → Floor → Room/Area hierarchy, review category weights and visibility, set institution-approved SLA targets, assign signed-in users to Staff/Authority roles, and map each Authority to their block, floor, or area. Location scope is inherited by descendants.
5. Students can then submit reports. New accounts default to Student. Privileged actions and sensitive reporter data are checked again on the server.

Admins can rename or deactivate/reactivate location records without deleting historical issue references. Deactivate active child locations before their parent; a report can only target an active Room/Area under an active Floor and Block. Reporter resolution/reopen window defaults to 7 days and is configurable from 1–30 days. For privacy, manual merges are restricted to open, public, non-anonymous master issues; anonymous or restricted reports remain separate.

No college email-domain or routing table was supplied, so OAuth remains the configured login and no unsupported campus-domain restriction is claimed. Real email delivery is not configured; in-app notifications are the active channel.

Photos use owner-bound, retryable upload tokens and short-lived signed URLs only after the issue and attachment association are verified. The remote object write happens before the MySQL report transaction; if that transaction fails, its token is still reusable for retry. Runtime storage has no delete API, so abandoned objects may remain; the application does not claim physical deletion or automatic cleanup.

## Optional synthetic demo data

`pnpm db:seed:demo` adds clearly labeled generic example blocks, areas, and sample issues for a **disposable demo database**. It refuses to run if the database already contains any campus block or issue. Do not use synthetic records as real campus reports; remove rows marked `demoRecord=true` before using that database for institutional data.

Campus-specific URLs, locations, Authority assignments, priority weights, visibility defaults, and SLA targets are configurable in Admin settings. Payment, inventory/vendor management, external ticketing, automated issue resolution, image-based duplicate detection, offline/PWA drafts, and messaging bots are outside the MVP.

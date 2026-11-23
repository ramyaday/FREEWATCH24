# FREEWATCH 24 — creator discovery starter
Created by Ram Yadav. Product requirements are based on the supplied project brief.

## Included
- Responsive React + TypeScript dashboard with dark neon/glass UI, owner branding, three channel links, campaign overview and campaign creation modal.
- Express API scaffold with Supabase token verification, campaign listing, creation and status routes.
- PostgreSQL/Supabase migration for core tables, row-level security, event/analytics structures and an advisory-lock-backed three-active-campaign creation function.
- Motion preference CSS, animation preference control, low-data setting placeholder and clear demo labels.

## Important implementation status
This is a **starter build, not a production-ready or deployed platform**. The dashboard currently uses demo campaign data in browser memory. Registration/login, Google OAuth, email verification, password reset, persistent frontend API wiring, moderation/admin UI, real AI provider calls, background scheduler, external platform metadata/API integrations, referral attribution, verified analytics, notification delivery, PWA manifest/service worker, automated tests, backups and deployment are not yet implemented. The API and SQL are foundations, not proof that those features are live. The interface explicitly avoids autoplay and fake engagement.

## Run locally
Requirements: Node.js 20+ and npm.

1. Copy `.env.example` to `server/.env` and set a Supabase project URL and **server-only** service role key. Never expose the service role key to the browser.
2. Run `db/migrations/001_initial.sql` in the Supabase SQL editor or via your migration workflow.
3. Install dependencies:
   ```bash
   npm install
   npm --prefix client install
   npm --prefix server install
   ```
4. Start both apps:
   ```bash
   npm run dev
   ```
   Frontend: http://localhost:5173 — API health: http://localhost:4000/health

## Auth and database setup still required
Enable Supabase Auth email/password and configure email templates/redirect URLs. Add Google OAuth credentials in Supabase if desired. Create the `profiles` row on signup (e.g. an auth.users trigger) and implement client auth/session handling. Set `SUPABASE_URL`, `SUPABASE_SERVICE_ROLE_KEY`, and `CLIENT_ORIGIN` for the API. The API expects the Supabase access token in `Authorization: Bearer <token>`.

## Campaign integrity
The SQL function `create_campaign_limited` serializes campaign creation per user with a transaction-scoped advisory lock and rejects a fourth active campaign. Campaign goals are aspirations, not guaranteed results. The API validates basic fields; add platform-specific URL validation, moderation/eligibility checks and abuse controls before public launch. Status activation endpoint currently performs a basic count; production should move resume into a similarly locked SQL function to eliminate a resume race.

## Deployment checklist
- Deploy client to Vercel/Netlify and API to a Node host; configure CORS origins.
- Apply migrations to production Supabase; verify RLS with non-admin test accounts.
- Store secrets only in server environment variables/secret manager.
- Configure auth redirect URLs, SMTP/email provider, domain and HTTPS.
- Add queue/scheduler for expiration and moderation jobs, backups, monitoring, rate limiting and audit review.
- Obtain official platform API access/approval where required. External platforms may restrict embeds, metadata, metrics and API access. Never claim clicks are views; mark platform-sourced verified metrics separately.
- Add privacy policy, terms, content/reporting process, data retention and deletion workflows.
- Run security review, load tests, accessibility checks and end-to-end tests before launch.

## External credentials / approvals
Supabase project and email configuration are required for accounts and persistence. Google OAuth requires Google Cloud OAuth setup. AI features require an authorized AI provider key. YouTube/Meta/TikTok/Vimeo data and analytics require their respective API access, scopes, app review and policy compliance; availability varies. Cloud hosting, database, SMTP and monitoring may incur charges, so a permanently free production service cannot be guaranteed.


## Authentication and cloud campaigns (new)

The client now includes Supabase email/password sign-up, sign-in, password-reset email, session persistence, sign-out, and account-backed campaign listing/creation. Campaign creation and reactivation use database RPCs that enforce the maximum of three active campaigns atomically. Apply both SQL migrations in order in the Supabase SQL editor.

Create `client/.env.local` with `VITE_SUPABASE_URL` and `VITE_SUPABASE_ANON_KEY` (the public anon/publishable key only). Configure email confirmation and your allowed redirect URLs in Supabase Auth. Keep `SUPABASE_SERVICE_ROLE_KEY` server-only.

Run `npm install` at the project root, then `npm run dev`. Without Supabase environment values, the client intentionally displays a setup notice rather than pretending account features work.

## Latest UI work
- Added Lucide SVG icons throughout the workspace navigation, action controls, creator tools and official YouTube footer links.
- Added a campaign discovery list that opens the original video in a new tab and never autoplays it.
- Added public creator discovery cards backed by the `profiles` table (requires profiles to exist and public-read RLS to be applied).
- Added a local content-assistant in AI Studio for drafting a title, description and hashtags from a topic. This is a deterministic offline helper, **not** a connected generative-AI model; connect an authorized provider and server-side secret to enable true AI generation.

## Still not complete
Real platform-verified view analytics, background campaign scheduler, production moderation/admin workflows, referral validation, notification delivery, Google OAuth, real AI API integration, deployment, security/load tests and platform API approvals remain outstanding. Do not present the sample helper or UI shells as these production features.

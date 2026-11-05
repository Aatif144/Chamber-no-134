# CHAMBER NO. 134 — Advocate Chamber Management

A responsive React + Supabase starter application for managing chamber cases, clients, hearing history and fee payments. It runs in a browser on laptop/desktop and mobile. It can be installed as a PWA in a later enhancement; this starter is a responsive web app, not a packaged Play Store APK.

## Included now

- Email/password authentication through Supabase Auth
- Chamber creation and multi-user membership foundation
- Dashboard: active cases, today's dates, clients and estimated pending fees
- Case registration, editing, search and case detail view
- Client directory with call and WhatsApp links
- Hearing diary with order/proceedings summary and case hearing history
- Payment register and case-wise payment history
- Legal research links to India Code, Supreme Court, eCourts and Google search
- Row Level Security (RLS) policies to isolate chamber data

## Requirements

- Node.js 20 or newer
- A Supabase account and a new Supabase project
- Internet connection

## 1. Create the Supabase database

1. Create a project at https://supabase.com/.
2. Open **SQL Editor → New query**.
3. Paste and run all contents of `supabase/schema.sql`.
4. In **Project Settings → API**, copy the Project URL and `anon` / publishable key.
5. In Supabase **Authentication → URL Configuration**, set your local Site URL to `http://localhost:5173` and add your production URL later.
6. For a production deployment, enable email confirmation and configure SMTP.

## 2. Run the app

```bash
npm install
cp .env.example .env
```

Edit `.env`:

```env
VITE_SUPABASE_URL=https://YOUR_PROJECT.supabase.co
VITE_SUPABASE_ANON_KEY=YOUR_SUPABASE_ANON_KEY
```

Then:

```bash
npm run dev
```

Open the local URL shown by Vite (normally http://localhost:5173). Create an account, sign in, then create **CHAMBER NO. 134**.

Build for deployment:

```bash
npm run build
```

The output is in `dist/`. Deploy it to a static host such as Vercel or Netlify. Add the same two environment variables to the hosting provider and add the deployed URL to Supabase Auth redirect/site URL settings.

## 3. Add multiple chamber users

The first account that creates the chamber becomes `owner`. To add colleagues:

1. In Supabase Dashboard → Authentication → Users, invite the colleague or create their account.
2. Copy that user's UUID.
3. Find the chamber UUID in Table Editor → `chambers`.
4. Run this SQL (replace both UUIDs):

```sql
insert into public.chamber_members(chamber_id,user_id,role)
values ('CHAMBER_UUID','AUTH_USER_UUID','advocate');
```

Supported roles: `owner`, `admin`, `advocate`, `clerk`, `read_only`. The current starter applies RLS policies to isolate chamber data, but the UI is not yet a full role-administration console. Assign roles carefully. `read_only` users can read records but cannot add or edit them.

## 4. Important legal and operational limitations

- **Court data is not automatically synced.** The app records the next date and hearing history entered by your chamber. Automated eCourts sync needs an authorised, supported API/integration and must not rely on scraping or bypassing access controls.
- **Bare Acts and judgments:** the Legal Research page opens official/public research sources. It does not copy or silently download every Act from Google. Verify the current amended text and judgment before relying on it.
- **Reminders:** browser push/SMS/WhatsApp reminders are not wired in this first version. They require a notification service, permission and delivery configuration.
- **Documents:** the schema includes a private-document metadata table, but upload/download screens and Storage bucket policies are not yet enabled. Do not upload confidential case files until private storage and signed-access policies are configured.
- **Language:** this starter UI is English. Hindi labels and a language switch are a planned next step; browser translation may be used temporarily.
- **PWA / Android:** mobile browser responsive layout is included. A PWA manifest/service worker or Android wrapper is needed for installable app packaging.
- **Fees:** pending balance is a simple estimate: case total fee minus recorded payments. It is not accounting software, tax advice or a client ledger substitute.
- **No legal advice:** this is an administrative recordkeeping tool, not a legal research or legal advice engine.

## 5. Security checklist before real client use

- Use a dedicated Supabase project and a strong owner password.
- Never put the Supabase `service_role` secret in `.env` variables prefixed with `VITE_` or in frontend code. Only the anon/publishable key belongs in the browser.
- Enable email confirmation, MFA where available, and secure account recovery.
- Review RLS policies and role permissions with a developer before adding real users.
- Configure backups/PITR, retention and an incident-response process.
- Add private Storage policies before storing documents; avoid public buckets.
- Collect only necessary client information; get appropriate consent and restrict staff access.
- Test with dummy data before entering actual matters.

## Project structure

```text
chamber-no-134/
  index.html
  package.json
  .env.example
  README.md
  src/
    main.jsx
    App.jsx
    supabase.js
    style.css
  supabase/
    schema.sql
```

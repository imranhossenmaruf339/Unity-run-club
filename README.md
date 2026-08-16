# Unity Run Club — Setup Guide

## 1. Create your Supabase project
1. Go to supabase.com → New Project.
2. Once it's up, open **SQL Editor** and run the entire contents of `supabase-schema.sql` (creates tables, RLS policies, and the `event-images` storage bucket).
3. Go to **Project Settings → API** and copy the **Project URL** and **anon public key**.

## 2. Connect the site to Supabase
Open `assets/config.js` and replace the two placeholder values:
```js
window.UNITY_CONFIG = {
  SUPABASE_URL: "https://xxxx.supabase.co",
  SUPABASE_ANON_KEY: "eyJ...",
};
```
The anon key is safe to ship in frontend code — real access control comes from the Row Level Security policies already in `supabase-schema.sql`, not from hiding this key.

## 3. Create your first admin account
1. Open `/admin/login.html` → "Create Account" tab → sign up with your email/password.
2. In Supabase SQL Editor, run:
   ```sql
   update profiles set role = 'admin' where email = 'you@example.com';
   ```
3. Sign in at `/admin/login.html` — you'll land on the dashboard.

## 4. Add an event
Admin → **Manage Events** → **+ Add Event** → fill in the fields → **Save Draft** (keeps it private) or **Publish Event** (goes live immediately on `/events`). Toggle **Featured** to have it appear in the homepage's big "The Big Run" section with a countdown.

## 5. Approve an event request
A visitor submits `/suggest-event.html` → it lands in Admin → **Event Requests** as *Pending*. Click **Review** to see full details, then **Approve** (creates a draft event you can edit/publish), **Reject**, or **Request Changes** (both let you attach a note).

## 6. Deploy / update the site
This is a static site (plain HTML/CSS/JS + the Supabase JS client loaded from CDN) — no build step. I can deploy it straight to Netlify for you via the connected Netlify tool, or you can drag-and-drop the `site/` folder into Netlify's dashboard, or connect a GitHub repo for auto-deploys on every push.

## What's included vs. what's next
**Included:** homepage (hero, upcoming events, featured event + countdown, mission, why-us, community stats, gallery, team, contact), full events listing with filters + past events, dynamic event detail pages, public "suggest an event" form with image upload, admin login/signup, admin dashboard with live stats, full event CRUD (draft/publish/archive/delete with confirmation), event request review (approve/reject/request changes) — all reading and writing real Supabase data, protected by Row Level Security so only admins can write.

**Intentionally left as real placeholders, not invented data:** there are currently no events, team members, or community stats in the database, so those sections show honest empty states until you add real information through the admin panel — per your brief, nothing fake is hardcoded in.

**Not yet built (flagged in the brief as optional/conditional):** `/my-requests` for visitors to track their own submissions — this needs a public-facing sign-in flow (separate from admin login) since requests aren't tied to a visitor account yet. Happy to add it if you want visitors to create accounts.

## Environment variables / secrets
There are none to keep secret on the frontend beyond the anon key (which is meant to be public). Do not put your Supabase **service_role** key anywhere in this site — it's never needed here.

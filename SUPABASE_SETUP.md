# Supabase setup

Run these files in the Supabase SQL editor, **in this exact order**:

1. `supabase/schema.sql` — creates every table (`users`, `services`, `locations`,
   `service_locations`, `slots`, `bookings`, `provider_assignments`), enables
   row-level security, and creates the `current_role()` helper.
2. `supabase/rpc.sql` — customer-facing booking RPCs (`create_booking`,
   `cancel_booking`), with row locking so a slot can never be over-booked.
3. `supabase/rpc_admin.sql` — every admin RPC (services, locations, slots,
   provider assignments, users/roles, bookings overview).
4. `supabase/rpc_provider.sql` — every provider RPC (their bookings, confirm,
   cancel with a required reason, assigned locations).

Then, in Project Settings → API, copy the **Project URL** and the
**publishable key** — these are what the app's `SUPABASE_URL` and
`SUPABASE_PUBLISHABLE_KEY` GitHub secrets/variables should hold; the CI
workflow passes them to `flutter build` via `--dart-define`.

## Making the first admin (mohanedsamehelghazouly22@gmail.com)

There is no admin yet inside a brand-new database, so nobody can open the
admin console to promote themselves. Bootstrap it once, by hand:

1. Install/run the app and sign in once as
   `mohanedsamehelghazouly22@gmail.com` using **"Continue with Google"**
   (email must be populated, so phone-OTP sign-in won't work for this step).
   This creates that user's row in `public.users` with role `customer`.
2. Run `supabase/seed_admin.sql` in the SQL editor. It promotes that account
   to `role = 'admin'`.
3. Sign out and back in (or just reopen the app) — the reactive session
   gate in `main.dart` will now route that account straight to the admin
   console.

To make someone a provider afterwards, you don't need SQL: sign in as
admin → **Users & roles** → tap the account → choose `provider`. Then
assign them to a service + location from **Provider assignments**.

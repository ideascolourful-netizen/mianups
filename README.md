# MIAN UPS & ELECTRONICS – Netlify + Supabase Edition

This package is adapted for Netlify hosting. It uses:
- Netlify for the public website and serverless API functions
- Supabase Postgres for products and orders
- Supabase Storage for product photos
- Supabase Auth for the admin login

## Before starting
You need a GitHub account, a Netlify account and a Supabase account. You can do the setup on a computer or a mobile browser, but a computer is easier.

## Step 1 — Create the Supabase project
1. Visit https://supabase.com/ and create a project.
2. Choose a strong database password and save it safely.
3. Open SQL Editor in your Supabase project.
4. Open `supabase/schema.sql` from this ZIP, copy all its contents and run it in SQL Editor.
5. Go to Project Settings → API. Copy the Project URL and the anon/publishable key.
6. In Authentication → Providers / Settings, disable public email sign-ups.
7. In Authentication → Users, create your own admin user with your email and a strong password.
   - Do not share this password.
   - The admin email must match the `ADMIN_EMAIL` environment variable you set on Netlify.

## Step 2 — Configure the website
1. Open `public/config.js`.
2. Replace `PASTE_YOUR_SUPABASE_PROJECT_URL` with your Supabase Project URL.
3. Replace `PASTE_YOUR_SUPABASE_ANON_OR_PUBLISHABLE_KEY` with the anon/publishable key.
4. Replace `923XXXXXXXXX` with your shop WhatsApp number in international digits, e.g. `923001234567`.
5. Save the file.

The anon/publishable key is designed to be public. Never put the Supabase `service_role` key in `public/config.js`, HTML or any browser file.

## Step 3 — Upload to GitHub
1. Extract this ZIP.
2. Create a new GitHub repository, for example `mian-ups-electronics`.
3. Upload all extracted files and folders, including `netlify.toml`, `netlify/`, `public/`, and `supabase/`.

## Step 4 — Deploy on Netlify
1. Visit https://app.netlify.com/ and sign in with GitHub.
2. Choose Add new site → Import an existing project.
3. Select your repository.
4. Build settings should be:
   - Build command: leave blank (or `echo "No build step"`)
   - Publish directory: `public`
   - Functions directory: `netlify/functions`
5. Deploy the site.

## Step 5 — Add Netlify environment variables
In Netlify: Site configuration → Environment variables. Add:
- `SUPABASE_URL` = your Supabase Project URL
- `SUPABASE_SECRET_KEY` = your Supabase secret key (`sb_secret_...`, server-side ONLY). The code also accepts the legacy `SUPABASE_SERVICE_ROLE_KEY` variable.
- `ADMIN_EMAIL` = exact email address of your admin user

Find the secret key under Supabase Project Settings → API Keys → Secret keys. Treat it like a password. Do not paste it into website files, GitHub, chat or public browser config. A secret key bypasses Row Level Security and must stay only in Netlify environment variables.

After adding environment variables, trigger a new deploy.

## Step 6 — Test before launch
1. Open the Netlify URL.
2. Click Admin and sign in with the Supabase admin user.
3. Add a test product with a test image.
4. Confirm it appears on the storefront.
5. Place a test order.
6. Check the order in Admin → Orders.
7. Test status changes and stock levels.
8. Confirm WhatsApp opens the correct shop number.

## Step 7 — Connect your domain
In Netlify: Domain management → Add a domain. Follow the DNS instructions at your domain registrar. Wait for DNS and HTTPS provisioning to complete.

## Important current-version limitations
- Product management, product images, storefront and customer orders are included.
- Cash on Delivery is the only checkout method in this starter. JazzCash, Easypaisa, bank/card gateway integration is not included.
- Admin login uses Supabase Auth. Keep public sign-ups disabled and create only trusted admin users.
- Orders are stored securely through Netlify Functions using the server-side service role key. The service role key must remain only in Netlify environment variables.
- This starter validates stock when the order is placed, but does not reserve/deduct stock until the order is confirmed. The current order status function changes status but does not yet implement transactional stock deduction. Confirm stock manually before dispatch.
- No customer account, order tracking portal, email/SMS notifications, courier integration, or automatic stock reservation is included yet.
- Before advertising, test thoroughly and consider adding rate limiting, spam protection/CAPTCHA, transactional stock handling and privacy/returns pages.

## Troubleshooting
- "Store setup is not finished": check both values in `public/config.js`, commit, and redeploy.
- Admin login rejected: verify the Supabase user exists, email is exact in Netlify `ADMIN_EMAIL`, and password is correct.
- API returns server configuration error: verify Netlify `SUPABASE_URL` and `SUPABASE_SECRET_KEY` (or legacy `SUPABASE_SERVICE_ROLE_KEY`).
- Product image upload fails: confirm `supabase/schema.sql` ran successfully and bucket `product-images` exists.
- Products not loading: check Netlify Function logs and Supabase project status.

## Project structure
- `public/` — storefront and browser code
- `public/config.js` — public Supabase URL/key and WhatsApp configuration
- `netlify/functions/` — server-side API functions
- `supabase/schema.sql` — database and storage setup
- `netlify.toml` — Netlify build and routing configuration

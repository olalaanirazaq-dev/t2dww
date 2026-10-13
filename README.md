# T2DW — Clothing / Lifestyle / You

A responsive streetwear storefront built for T2DW, using React, Vite, Express and a persistent SQLite database. The project starts with 19 replaceable sample listings based on the supplied T2DW garment boards, plus the T2DW logo artwork. Sample 16 was not present among the provided files.

## Run locally

Requirements: Node.js 20 or newer and npm.

1. Install dependencies:

   ```powershell
   npm install
   ```

2. Create local settings:

   ```powershell
   Copy-Item .env.example .env
   ```

3. Edit `.env` before starting:
   - Set `T2DW_ADMIN_EMAIL` to the store owner's email.
   - Replace `T2DW_ADMIN_PASSWORD` with a unique password of at least 14 characters.
   - Set `VITE_WHATSAPP_NUMBER` to the business WhatsApp number in international format, digits only (Nigeria: `234...`).
   - Change `VITE_CONTACT_EMAIL` to the customer-support mailbox.
   - Keep `DATABASE_PATH` and `UPLOAD_DIRECTORY` on persistent storage for production.

4. Start the client and API:

   ```powershell
   npm run dev
   ```

   Open the Vite URL printed in the terminal (normally `http://localhost:5173`). The API listens on port 3001. The SQLite database and uploaded product photos are stored under `data/`.

The store creates an admin account from `T2DW_ADMIN_EMAIL` and `T2DW_ADMIN_PASSWORD` on its first run. If the admin email already exists, the startup process grants that account the admin role but does not change its password. Keep `.env` and `data/` private.

## Production

Build the static storefront and run the API from the same project directory:

```powershell
npm run build
$env:NODE_ENV = "production"
npm start
```

The production process serves both the API and `dist/`. Put it behind HTTPS, configure a persistent volume for `data/`, set the environment variables in the hosting platform's secret/configuration store, and back up the database and uploads. SQLite is a real persistent database and is appropriate for a small, single-instance launch; use a managed database and object storage before horizontally scaling or running multiple app instances. The SQL.js adapter loads the local database in memory and writes it to disk on mutations, so it is not intended for high write concurrency.

`VITE_*` values are compiled into the public storefront at build time. They are only for public contact/WhatsApp details. Never put passwords, API keys, or other secrets in `VITE_*` variables.

## Store capabilities

- Editorial storefront, responsive navigation, collection/category browsing, search, stock/size/color/price filters, sale sorting, quick view and product detail pages.
- Product variant and stock validation, a persistent local cart and wishlist, price calculations, a first-order `FIRSTLOOK` discount and admin-managed promo codes.
- Guest checkout records an unpaid `awaiting_dm` order, reserves stock, and offers a WhatsApp handoff when configured. Standard delivery is ₦5,000 and complimentary from ₦100,000; express delivery is ₦8,000. These are starter settings and should be changed to match actual fulfilment.
- Customer registration and login use bcrypt password hashing and an HTTP-only, SameSite session cookie. Signed-in customers can view order statuses and save/delete delivery addresses.
- The `/admin` storefront route has server-enforced admin checks for inventory, product image uploads, promotions, customer orders and order status changes. Cancelling an order releases its reserved inventory.
- Product structured data, accessible labels/forms, loading/error/empty states and responsive layouts.

## Checkout and integrations

There is deliberately **no payment provider**. Orders are not marked paid: the customer and store arrange payment via direct message. Configure `VITE_WHATSAPP_NUMBER` to enable the WhatsApp order handoff. The customer still receives an order number and sees the unpaid order in their account if signed in.

Email notifications and password-reset email delivery are not configured. The password reset endpoint returns an explicit unavailable message rather than pretending to send an email. Add an email service and token-expiry workflow before enabling password recovery or transactional mail. Contact, returns, delivery and size-guide links use the configured customer-support email address; replace the sample editorial review with real customer feedback before launch.

The 19 seed products and their stock/prices are examples, not confirmed inventory or final pricing. Product gallery assets are copies of the supplied files in `public/products/`; the original uploads remain untouched. Edit/remove the seeded products in the admin dashboard and replace the copy, prices, delivery terms and contact details with real store information. Remote editorial photos are served by Unsplash; replace them with licensed, optimized brand photography for production.

## Checks

```powershell
npm test
npm run build
```

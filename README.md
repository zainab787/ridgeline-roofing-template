# Ridgeline Roofing: a Whop Websites template for roofers

A conversion-focused roofing site that runs the whole deposit → job → final invoice flow on Whop.
Built with TanStack Start + Tailwind on Whop Websites. No Stripe, no PayPal, no custom card forms.

## Re-skin it in one file

Edit `src/site.config.ts`: company name, phone, services, service area, reviews, FAQ, prices and the
Whop IDs (`accountId`, `productId`, `plans.inspection`, `plans.deposit`, `plans.service`).
Colors and fonts live in `src/styles.css` as Tailwind theme tokens. No component changes needed.

## What is wired to Whop

| Feature | Where |
|---|---|
| Inspection fee, deposit and flat-rate plans | real Whop plans, IDs in `site.config.ts` |
| Checkout | `CheckoutElement` + `ExpressCheckoutElement` (`components/PlanCheckout.tsx`) |
| Deposit with card on file | `Payments` in setup mode: `EmailElement`, `AddressElement`, `PaymentElement`, required `BrandingElement` → `POST /setup_intents` → `POST /payments` against the saved method (`components/DepositCheckout.tsx`, `lib/actions.server.ts`) |
| Request an Estimate | form → `/api/estimate` → `POST /leads` for the signed-in Whop user (Whop sign-in via OAuth + PKCE, `routes/api/oauth/*`) |
| Final balance | `/balance` → `POST /invoices` with an inline variant, then `/issue` (auto-charge the card on file) or `/send` (email a pay link) |
| Pixel | page views and checkout events are automatic. Custom: `estimate_requested`, `service_viewed`, `deposit_started` via `window.whop.track` (`lib/track.ts`) |
| Financing | Klarna, Afterpay, Splitit etc. appear inside the Whop checkout for approved merchants; nothing to build |

Server calls pin `Api-Version-Date: 2026-10-27-3` (`lib/whop-api.ts`). The API key is injected by the host and never appears in code.

## Run it

```bash
bun install
whop apps dev      # local, with injected credentials
whop apps deploy   # build, typecheck and ship
```

Register `https://<your-site>/api/oauth/callback` as an OAuth redirect URI on the app (once per domain).

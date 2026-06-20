# CLAUDE.md — Instacare

## What this project is
Instacare is a digital booking and lead-generation platform for emergency and short-term regulated homecare in Scotland. It offers a fast, clean online booking experience for respite, domiciliary, and live-in care.

Instacare is a marketing and booking layer — NOT a registered care provider. All regulated care (assessment, compliance, delivery) is performed by **Surecare**, an existing Care Inspectorate–registered homecare agency (a family business in Glasgow). Instacare passes booking requests to Surecare and earns a premium above Surecare's standard client rate.

## MVP scope and bar
This is a proof-of-concept MVP. Success = safely handling up to ~50 hours of care bookings per month. After that, a professional developer rebuilds it as a proper app. **Favour simplicity over completeness** — do not over-engineer; this code will be replaced later.

Out of scope for the MVP (do not build unless explicitly asked):
- Customer dashboard
- Admin portal (Surecare is notified by email; handoff is manual)
- Native mobile app

## Hard rules (do not violate)
1. **A booking is a REQUEST pending assessment, not a confirmed commitment.** State machine: `requested → assessment_call → confirmed | declined → delivered`. The UI must never promise instant confirmation or guaranteed availability — a qualified person must assess needs before care is confirmed. This is a legal/regulatory requirement.
2. **Stripe hosted checkout ONLY.** Use Stripe Checkout / Payment Links. NEVER build a custom card-entry form. Card data must never touch this app or its database.
3. **Minimal data footprint.** Instacare's database stores only light booking data: contact details, broad care type, dates, booking status. Detailed/sensitive health information is captured by Surecare during the assessment call into Surecare's own compliant systems — NOT stored here. When in doubt, store less.
4. **Secrets never go in the repo.** All keys (Supabase, Stripe) live in environment variables. `.env*` files must be gitignored. Never hardcode or commit a key.
5. **Scotland only.** Geographic scope is Scotland (initially the Glasgow area).

## The questionnaire
The intake questionnaire is *intake data*, not the care assessment itself. A qualified Surecare person performs the real needs/risk assessment after the request. Structure: a shared core set of questions plus conditional modules triggered by the care type(s) selected. Capture enough to triage and give a price estimate — not full medical detail.

## Pricing
Show the customer an **estimated price range** during the questionnaire. The exact price is confirmed by Surecare after the assessment call. The in-app calculator is an estimator, not a binding quote.

## Tech stack
- **Next.js** — TypeScript, Tailwind CSS, App Router, `src/` directory
- **Supabase** — database + auth; row-level security must be enabled on every table holding user data
- **Stripe** — hosted checkout only
- **Vercel** — hosting; **GitHub** — repo `R28-code/instacare`

## Working notes
- Built by a non-technical founder using AI tools. Keep code clear, conventional, and commented. Avoid clever or unusual patterns.
- Polished, trustworthy UI is a hard requirement — this is the care sector and credibility matters.
- Security-critical work (Supabase RLS, secret management, the Stripe flow) will be reviewed by an experienced developer before launch.

## Open decisions (ask, don't assume)
- **Payment timing** — likely a pre-authorisation captured only when Surecare confirms (not an upfront charge). Confirm before implementing payments.
- Final questionnaire fields and pricing rules are still being designed — check before hardcoding them.

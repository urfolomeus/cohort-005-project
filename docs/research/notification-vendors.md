# Notification-infrastructure vendors for Cadence

Researched 2026-11-18 from vendor docs, pricing pages, DPAs, and npm registry metadata. This is a shortlist, not a choice of vendor.

## TL;DR

**No vendor offers UK hosting at a self-serve price.** The only UK region found is from **Novu**, and it is Enterprise-only with custom pricing [N1][N2]. Other routes to UK residency:

- Self-host Novu Community Edition in a UK region. This drops email activity tracking (clicks) and leaves you without a vendor DPA [N8].
- Use EU hosting with a DPA that includes the UK Addendum.

**EU hosting at small-scale prices:**

| Vendor | EU region | Cost |
|---|---|---|
| Novu | Frankfurt | Included on the **Free** plan [N1][N2] |
| Courier | Ireland | Business plan, pay-as-you-go at **$0.005/send**, with no free sends [C1][C2][C3] |

Knock is **US-only**. Its own docs say EU residency is "expected by the end of 2026", and I found no launch notice [K2][K10].

**Shortlist (each meets the musts, with the caveat noted):**

| Vendor | Strength | Gap |
|---|---|---|
| **Knock** | Best event coverage. One webhook stream covers delivered, link-clicked (Knock's own link rewriting), seen and read. React 19 is supported. | US-only hosting. DPA has the UK Addendum [K1][K3][K4]. |
| **Novu** | EU on the Free plan. In-app seen/read webhooks. React 19 is supported. Open source. | Email clicks are tracked through the provider, but they are **not forwarded as a webhook event** [N3][N4]. |
| **Courier** | EU on the cheapest paid plan. Courier tracks email clicks itself. | **No in-app read webhook event**. Inbox reports only OPENED and CLICKED statuses [C5][C6]. |

Reserve: **SuprSend**. It has an EU option and delivered/seen/clicked statuses, but the residency plan gate is not stated and preferences need the $250/mo Business plan [S1][S2].

Excluded:

- **MagicBell**: no outbound webhooks or residency documented.
- **Engagespot**: EU region exists, but outbound events and pricing are unverified.
- **OneSignal**: no web inbox found.
- **Customer.io**: no React inbox component; it is a marketing-automation product.

**Inference that could change the decision:** Cadence controls the Alert link target. It can record "Opened" itself when the student lands on the link (for example `/notifications/:id?via=alert&aid=…`). It can record "Read" itself from the inbox SDK's read callbacks or its own handlers. Doing this removes the dependency on vendor click and read webhooks. Novu (EU, free) and Courier (EU, cheap) would then meet every must. Only "Delivered" would still need the vendor or email provider.

## Comparison

| Vendor | In-app React SDK | Email / BYO | SMS | Preferences / unsubscribe | Delivered + click + read events | Residency | Free tier | First paid tier | Analytics |
|---|---|---|---|---|---|---|---|---|---|
| **Knock** | `@knocklabs/react` 0.15.1. Peer deps `react ^17\|\|^18\|\|^19` [K4]. Styled components plus headless hooks [K5]. | Yes: SES, Postmark, Resend, SMTP, others [K6] | Yes: Twilio, Vonage, SNS, others [K7] | Preference sets, hosted or custom preference center, 1-click commercial unsubscribe headers [K6][K8] | **All via webhook**: `message.delivered`, `message.link_clicked`, `message.seen`, `message.read`, `message.interacted` [K3] | **US only** (all subprocessors are in the USA). DPA has the UK Addendum. EU "by end of 2026" [K1][K2][K10] | 10k messages/mo [K9] | Starter **$250/mo**, 50k messages, then $0.005 each [K9] | Engagement tracking on all plans. "Top links clicked", unique vs total [K9][K11] |
| **Novu** | `@novu/react` 3.20.0. Peer deps `react ^18\|\|^19` [N5]. `<Inbox>` plus headless hooks [N6]. | Yes: SES, Postmark, Resend, SMTP, others [N7] | Yes, all channels on Free [N2] | Global and per-workflow preferences, `<Preferences />` component. **No built-in unsubscribe link or header**; you build it [N7] | Webhooks: `message.delivered`, `message.seen`, `message.read`, plus others. **Email click is not forwarded**; it sets read/seen on the message [N3][N4] | **EU (Frankfurt) on Free**, US. UK, SG, AU, JP, KR are Enterprise-only. Self-host option [N1][N2] | 10k workflow runs/mo, 24h activity retention [N2] | Pro **from $30/mo**, 30k runs, $1.20 per 1k extra [N2] | Usage page with in-app interactions. Email open/click shown per message in the activity feed only [N9] |
| **Courier** | `@trycourier/courier-react` 9.3.5. Peer deps `react >=18` [C7]. `CourierInbox` plus custom views, `CourierInboxTheme` [C8]. | Yes: SES, Postmark, Resend, SMTP, others [C9] | Yes: Twilio, Vonage, MessageBird, others [C10] | Topics, hosted preference page, `{{$.urls.unsubscribe}}`, per-topic List-Unsubscribe and One-Click headers [C11] | `message:updated` with DELIVERED (from provider), OPENED, CLICKED (Courier redirect). **No inbox read/archived event** [C5][C6] | **EU (Ireland) on Business and Enterprise**, self-serve. DPA has the UK Addendum [C2][C4] | Developer: 10k sends/mo, **no EU** [C1] | Business: **$0.005/send**, pay-as-you-go, no free sends, EU included [C1][C3] | Per-template sent/delivered/open/click rates, plus a Template Metrics API on all plans [C12] |
| **SuprSend** | `@suprsend/react` 1.6.0. Peer deps `react >=16.8` [S3]. `@suprsend/react-core` headless hooks, `ITheme`, component slots [S4]. | Yes: SES, Mailjet, Mailgun, others, via vendor callbacks [S5] | Yes, BYO provider [S1] | **Preferences only on Business ($250/mo) and above** [S1] | Delivered/seen/clicked for email (from vendor callbacks) and inbox. Outbound webhook per channel; **channel list unclear** [S5][S6] | "US or EU data residency" (Enterprise page). **Plan gate not stated** [S2][S7] | 10k notifications/mo, SuprSend branding on inbox [S1] | Essentials **$100/mo**, 50k [S1] | Template analytics and engagement tracking on all plans [S1] |
| **MagicBell** | `@magicbell/react` 1.14.0, no peer deps declared. Legacy `@magicbell/magicbell-react` needs `react >=18.3.1` [M3] | SES, SendGrid, Mailgun, SMTP listed [M2] | Yes (product listed) [M1] | Not verified | **No outbound webhooks documented**. `read_at`/`seen_at` fields and a read-only Events API [M2] | **Not stated**. GDPR claim only [M4] | 1,000 deliveries/mo [M1] | Startup **$249/mo**, 50k [M1] | Not verified |
| **Engagespot** | `@engagespot/react-component` 3.0.1, peer deps `react >=16.8.3` [E3] | Yes (Mailgun, MailerSend, others) [E2] | Not verified | Not verified | Inbound provider tracking (delivered/seen/interacted). **Outbound events not found** [E2] | **EU region selectable at signup** (`api-eu.engagespot.co`). Hosting location not stated [E1] | Pricing page 404 | Pricing page 404 | Not verified |

## Knock

- **Residency**:
  - All 19 subprocessors are listed in the USA, AWS included [K1].
  - The DPA §9.3 applies the UK Addendum to UK-origin transfers [K12].
  - The plans FAQ says: "We expect to support EU data residency by the end of 2026" [K2].
  - **I found no changelog entry announcing an EU launch up to 2026-10-14** [K10].
- **Events**: the webhook catalog is the most complete for Cadence's funnel [K3]:
  - `message.sent`, `message.delivered`, `message.undelivered`, `message.bounced`
  - `message.seen`, `message.read`, `message.interacted` (an in-app feed click)
  - `message.link_clicked`, which needs Knock link tracking
  - `message.link_clicked` is not on the pricing page. The docs list no plan gate for webhooks [K2][K9].
- **Link clicks**:
  - **Vendor-side**. Knock link tracking is "provider agnostic", and it can be switched on separately from the provider's own tracking [K13].
  - Clicks on the in-app action URL count as `interacted`, not as link clicks [K13].
- **Inbox**:
  - Prebuilt components: `NotificationFeedPopover`, `NotificationFeed`, `NotificationIconButton`.
  - Full headless hooks.
  - `--rnf-*` CSS variables, or replace the CSS entirely. `colorMode` light/dark [K5][K14].
  - React 19 is in the peer deps. `next` and `@tanstack/react-router` are optional peers [K4].
- **Pricing**:
  - Free: 10k messages/mo.
  - Starter: $250/mo, 50k messages.
  - Enterprise-only: streaming analytics to a warehouse, HIPAA, SAML.
  - Log retention: 30 days on Free and Starter [K9].

## Novu

- **Residency**:
  - EU (Frankfurt) and US are chosen at account creation. The pricing page says "US + EU data residency" is on Free.
  - **UK is listed as an Enterprise region**.
  - API keys are tied to a region, and a region cannot be migrated by self-serve [N1][N2].
  - The Inbox needs `backendUrl`/`socketUrl` set to the EU endpoints (`eu.api.novu.co`, `eu.socket.novu.co`) [N10].
  - The DPA and SCC template are at novu.co/dpa [N1]. **UK Addendum not verified.**
- **Self-host** (residency route):
  - Community Edition is free and covers all channels and the Inbox/Preferences components.
  - It **excludes email activity tracking, DPAs, SOC 2, RBAC and SSO**, and allows 1 team member [N8].
  - Enterprise self-host has Cloud parity at custom pricing [N8].
  - Migrations are manual, and the Docker setup co-locates the database [N8].
  - **Whether Community includes outbound webhooks was not verified.**
- **Events**:
  - Webhooks are signed Svix-style, with retries over about 1 day.
  - Message events: `message.sent`, `delivered`, `failed`, `seen`, `read`, `unread`, `archived`, `unarchived`, `snoozed`, `unsnoozed`, `deleted` [N3].
  - **No plan gate is stated** for webhooks [N2][N3].
- **Link clicks**:
  - Novu takes clicks from the **provider's** delivery webhooks. Supported providers are SendGrid, Mailgun, Resend and SES; **Postmark is not listed** [N4].
  - The docs say: "`clicked` is not sent as a webhook, but it sets `read` and `seen` on the message" [N4].
  - **Unverified**: whether that then fires `message.read` for the email message. If it does, the email click would arrive indirectly.
  - Clicks are visible per message in the activity feed. Retention is 24h on Free and 7d on Pro [N2][N9].
- **Inbox**:
  - Composable `<Inbox>`, `Bell`, `Notifications` and `Preferences`.
  - "Override styles, replace every UI element".
  - Headless hooks such as `useNotifications` [N6].
  - Removing Novu branding needs Pro [N2].
- **Unsubscribe**:
  - Not automatic. You build a tokenised link and call the preferences API [N7].
  - **Built-in List-Unsubscribe headers not found.**
- **Pricing**:
  - Free: 10k runs/mo, 5 workflows, 3 members.
  - Pro: from $30/mo, 30k runs.
  - Team: from $250/mo [N2].
  - The pricing comparison table is hard to map by plan, so features per tier beyond residency are partly unverified [N2].

## Courier

- **Residency**:
  - The EU datacenter is in Ireland. "The EU datacenter is available on every paid plan: Business and Enterprise" [C2].
  - Setup is self-serve via `app.eu.courier.com`. The API is `api.eu.courier.com` [C2][C3].
  - The Inbox SDK needs `getCourierApiUrlsForRegion("eu")`, and the JWT must be minted from the EU API [C13].
  - **Conflict**: one search snippet and a Courier marketing blog describe EU as Enterprise or "by arrangement". The docs and billing page say Business and self-serve [C2][C3][C14].
  - The DPA §3.4 applies the UK Addendum. Its default infrastructure statement is "located in the United States" [C4].
- **Events**:
  - Outbound webhook types include `message:updated` and `preferences:user:updated` [C6].
  - The status enum includes DELIVERED, OPENED and CLICKED. "Every destination receives every event type". Retries continue for about 1 day [C5][C15].
  - **No read, unread or archived event or status** [C6].
  - The tracking docs say the SDK reports Inbox opened, read and clicked, but only OPENED and CLICKED appear in webhooks [C5].
  - **Gap**: in-app "Read" would need a client-side hook or a Courier API query (unverified).
- **Link clicks**:
  - **Vendor-side**. Courier "rewrites every link in an email to pass through Courier's redirect" [C5].
  - Only the first click is timestamped [C5].
  - A custom tracking domain needs Business [C5].
- **Inbox**:
  - `CourierInbox` component, custom views, `CourierInboxTheme`, `useCourier()`.
  - React ≥18; a separate package exists for React 17 [C7][C8].
- **Preferences**:
  - Hosted page, `{{$.urls.unsubscribe}}`, per-topic One-Click headers [C11].
  - One Courier doc says to enable the header at the provider instead. Test this with a real send [C11].
- **Pricing**:
  - Developer: free, 10k sends, no EU.
  - Business: $0.005/send with no free allowance [C1][C3].
  - Example (inference): 3k Alerts plus 3k in-app messages a month comes to roughly $30. **Unverified**: whether an Inbox message counts as a separate "send".
- **Analytics**: per-template sent, delivered, open, click and error rates, plus a Template Metrics API on all plans [C12].

## SuprSend

- **Residency**:
  - The security docs say "We support EU-data residency options" [S7].
  - The Enterprise page says "US or EU data residency" [S2].
  - The pricing page lists only "Custom Data Center Region" under Enterprise [S1].
  - **No EU hub hostname is documented. Which plans get EU is not stated.**
  - VPC, on-prem and BYOC are Enterprise-only [S2].
- **Events**:
  - Statuses: Triggered, Delivered, Seen, Clicked, Failed. Email and Inbox support delivered, seen and clicked [S6].
  - Email statuses depend on **vendor callbacks** (the email provider's click tracking) [S6].
  - The outbound webhook is set up per channel with selectable event types. Only email and WhatsApp examples are documented [S5].
  - "Seen" also counts clicks, so it is not a clean Read signal [S6].
- **Inbox**: `@suprsend/react` (drop-in) and `@suprsend/react-core` (hooks: `useFeedData`, `useFeedClient`). `ITheme` and component slot props [S4].
- **Pricing**:
  - Free: 10k.
  - Essentials: $100/mo.
  - Business: $250/mo, which **gates preferences**.
  - Outbound webhooks on all plans [S1].

## Excluded or partial

- **MagicBell**:
  - The API reference has no outbound webhook subscriptions. It has a read-only Events API with undocumented types [M2].
  - No hosting region, DPA or subprocessor list is published. The security page claims only GDPR compliance [M4].
  - Pricing: Builder free with 1k deliveries; Startup $249/mo [M1].
- **Engagespot**:
  - EU region (`api-eu.engagespot.co`) is selectable per workspace; hosting location is not stated [E1].
  - Only inbound provider tracking webhooks are documented [E2].
  - Pricing page returned 404.
- **OneSignal**: hosted in the EU (Google Cloud, Netherlands, per its DPA) [O1]. **No web notification inbox or React inbox component found.**
- **Customer.io**:
  - Added a web notification inbox in July 2026. It is a JS SDK snippet or headless `analytics.inbox()`, not a React component.
  - Pricing is profile-based, from the Essentials plan [CIO1].
  - It is a marketing-automation platform and a poor fit. Not researched further.

## Unverified / open questions

- **Knock EU launch**: "by end of 2026" is stated, but no announcement was found as of 2026-11-18 [K2][K10]. Ask sales.
- **Novu**:
  - Does a provider-reported email click fire a `message.read` webhook for the email message?
  - Does Community self-host include outbound webhooks?
  - Does the Novu DPA include the UK Addendum?
  - Exact per-plan feature mapping; the pricing table is ambiguous.
- **Courier**:
  - Can in-app read state be fetched via the API (for example, a message or inbox query) for backfill?
  - Does an Inbox delivery count as a billable send?
  - Business vs Enterprise EU: the docs and blog conflict.
- **SuprSend**: which plan unlocks the EU region, and does the inbox channel emit outbound webhooks?
- **MagicBell and Engagespot**: residency details, outbound events, and pricing (Engagespot) not verifiable from public pages.
- **Legal (inference, not from vendor sources)**: whether EU hosting plus the UK Addendum satisfies Cadence's UK obligations is a legal question. Confirm with whoever owns compliance.
- **React 19**: Knock and Novu list `^19` explicitly. Courier (`>=18`), SuprSend and Engagespot use open-ended ranges and do not explicitly claim React 19.

## Sources

Knock
- [K1] https://knock.app/legal/subprocessors
- [K2] https://docs.knock.app/manage-your-account/knock-plans
- [K3] https://docs.knock.app/developer-tools/outbound-webhooks/event-types
- [K4] https://registry.npmjs.org/@knocklabs/react/latest ; https://github.com/knocklabs/javascript/blob/main/packages/react/package.json
- [K5] https://docs.knock.app/in-app-ui/react/overview
- [K6] https://docs.knock.app/integrations/email/overview ; https://docs.knock.app/preferences/commercial-unsubscribe
- [K7] https://docs.knock.app/integrations/sms/overview ; https://docs.knock.app/integrations/sms/twilio
- [K8] https://docs.knock.app/preferences/overview
- [K9] https://knock.app/pricing
- [K10] https://knock.app/changelog (pages 2 and 5 checked; latest entry seen 2026-10-14 "Goals")
- [K11] https://docs.knock.app/send-notifications/analytics
- [K12] https://knock.app/legal/data-processing-addendum
- [K13] https://docs.knock.app/send-notifications/message-statuses
- [K14] https://docs.knock.app/in-app-ui/react/feed

Novu
- [N1] https://docs.novu.co/platform/additional-resources/security
- [N2] https://novu.co/pricing
- [N3] https://docs.novu.co/platform/developer/webhooks/webhooks
- [N4] https://docs.novu.co/platform/integrations/email/activity-tracking
- [N5] https://registry.npmjs.org/@novu/react/latest ; https://github.com/novuhq/novu/blob/next/packages/react/package.json
- [N6] https://docs.novu.co/platform/inbox/react/components/inbox
- [N7] https://docs.novu.co/platform/integrations/email
- [N8] https://docs.novu.co/community/self-hosted-and-novu-cloud ; https://docs.novu.co/community/self-hosting-novu/overview
- [N9] https://docs.novu.co/platform/workflow/usage ; https://docs.novu.co/platform/developer/limits
- [N10] https://docs.novu.co/platform/inbox/setup-inbox

Courier
- [C1] https://www.courier.com/pricing
- [C2] https://courier.com/docs/platform/workspaces/eu-datacenter
- [C3] https://www.courier.com/docs/workspaces/billing
- [C4] https://www.courier.com/data-processing-addendum
- [C5] https://www.courier.com/docs/monitor/tracking
- [C6] https://www.courier.com/docs/monitor/webhooks/events
- [C7] https://registry.npmjs.org/@trycourier/courier-react/latest ; https://github.com/trycourier/courier-web
- [C8] https://www.courier.com/docs/platform/inbox/inbox-overview
- [C9] https://www.courier.com/integrations ; https://www.courier.com/docs/platform/channels/email/aws-ses/ ; https://www.courier.com/docs/platform/channels/email/postmark/
- [C10] https://www.courier.com/docs/external-integrations/sms/twilio
- [C11] https://www.courier.com/docs/guides/build-a-preference-center ; https://courier.com/docs/platform/preferences/preferences-editor
- [C12] https://www.courier.com/docs/monitor/analytics ; https://www.courier.com/docs/platform/analytics/template-metrics-api
- [C13] https://www.courier.com/docs/in-app/authenticate-users
- [C14] https://www.courier.com/blog/eu-data-residency-notifications
- [C15] https://www.courier.com/docs/guides/getting-started/outbound-webhooks/

SuprSend
- [S1] https://www.suprsend.com/pricing
- [S2] https://www.suprsend.com/enterprise
- [S3] https://registry.npmjs.org/@suprsend/react/latest
- [S4] https://docs.suprsend.com/docs/headless-inbox ; https://docs.suprsend.com/docs/react-customize-inbox
- [S5] https://docs.suprsend.com/docs/outbound-webhook ; https://docs.suprsend.com/docs/amazon-ses
- [S6] https://docs.suprsend.com/docs/logging ; https://docs.suprsend.com/docs/branch
- [S7] https://docs.suprsend.com/docs/security

MagicBell
- [M1] https://www.magicbell.com/pricing
- [M2] https://www.magicbell.com/docs/api/reference
- [M3] https://registry.npmjs.org/@magicbell/react/latest ; https://registry.npmjs.org/@magicbell/magicbell-react/latest
- [M4] https://www.magicbell.com/security

Engagespot
- [E1] https://docs.engagespot.co/docs/concepts/data-region/
- [E2] https://docs.engagespot.co/docs/features/channels/email/mailgun/
- [E3] https://registry.npmjs.org/@engagespot/react-component/latest

Others
- [O1] https://onesignal.com/dpa
- [CIO1] https://docs.customer.io/messaging/channels/in-app/inbox/get-started/

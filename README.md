# YouTube Auto Translations

Personal automation for the Travel & Life channel. Translates titles into 50 language variants and adds captions in 30 base languages. Existing translations and original video settings are preserved.

## Current state (2026-10-28)

- Worker deployed at https://youtube-auto-translations.danikpaw888.workers.dev
- Google Cloud project: YouTube Translations (`sylvan-epoch-513017-m5`)
- YouTube Data API v3 enabled; OAuth branding, client and requested scope configured.
- OAuth scope: `https://www.googleapis.com/auth/youtube.force-ssl`.
- OAuth callback: `/oauth/callback`; owner setup form: `/connect`.
- Owner approved Google access and credential storage. Google OAuth is connected and in production mode; refresh token and channel identity were verified successfully.
- Automation is enabled. Cron checks for new uploads every 15 minutes (`*/15 * * * *`).
- Initial baseline contains 50 existing uploads and no queued jobs. Existing uploads are not modified by the initial baseline.
- No Google secrets are included in this archive.

## Secrets and connection

Set `GOOGLE_CLIENT_ID`, `GOOGLE_CLIENT_SECRET`, `SETUP_KEY`, and a randomly generated 32-byte hex `OAUTH_ENCRYPTION_KEY` as Worker secrets. The owner setup form requires `SETUP_KEY`. OAuth uses state, a secure encrypted HttpOnly cookie, PKCE, an exact HTTPS redirect, and verifies the returned YouTube channel against `CHANNEL_ID`. The refresh token is stored encrypted in KV as `oauth_refresh_v1`; the encryption key is separate in Worker secrets. Existing `GOOGLE_REFRESH_TOKEN` secret configurations remain supported.

The owner-only `/verify` form refreshes Google authorization, confirms the expected channel, and initializes the upload baseline without changing videos. Production uses `ENABLED=true` after these checks passed. Initial discovery records existing uploads as a baseline rather than modifying them. The worker requires a published Russian caption source and a default video language. Daily quota budgeting means all translations can take multiple days; this is not a guarantee that translations finish before publication.

## Validation

Run `node --check worker.mjs` and `node --test oauth.test.mjs`. Tests cover PKCE, secure cookies, invalid setup key, cross-origin requests, expired/tampered state, wrong channel rejection, encrypted token persistence, replay rejection, and keeping automation disabled after connection.

Latest Workers type package could not be retrieved in this environment due to DNS/network restrictions; syntax checks, mocked OAuth tests, and Cloudflare deployment validation passed.

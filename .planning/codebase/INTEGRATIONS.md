# INTEGRATIONS

## External APIs / Third-Party Services
- Google Tag Manager is actively embedded in the public site layout with container `GTM-WBDKL43Q` in `web/resources/views/layouts/public.blade.php`.
- Bandsintown widget is embedded via remote script `https://widget.bandsintown.com/main.min.js` in `web/resources/views/welcome.blade.php`.
- External CDN assets are used directly in Blade:
- Google Fonts in `web/resources/views/layouts/public.blade.php`.
- Bunny Fonts in `web/resources/views/partials/head.blade.php`.
- Font Awesome kit script in `web/resources/views/layouts/public.blade.php`.
- Alpine.js is loaded from jsDelivr CDN in `web/resources/views/layouts/public.blade.php`.
- Commerce/music/social outbound links are hard-coded in `web/resources/views/welcome.blade.php` and `web/resources/views/layouts/public.blade.php` (FFM, merch stores, streaming and social URLs).

## Database Integrations
- Primary runtime DB in local env is MySQL (`DB_CONNECTION=mysql`) in `web/.env.example`.
- Laravel supports sqlite/mysql/mariadb/pgsql/sqlsrv in `web/config/database.php`.
- SQLite artifact exists at `web/database/database.sqlite` and is used in testing/in-memory flows (`web/phpunit.xml`).
- Redis connection support is configured in `web/config/database.php` with host/password/port env bindings.
- Database-backed session/cache/queue defaults are configured in:
- `web/config/session.php` (`driver` defaults to `database`).
- `web/config/cache.php` (`default` store `database`).
- `web/config/queue.php` (`default` queue `database`).
- Required persistence tables are provisioned in migrations:
- Users/sessions/password resets in `web/database/migrations/0001_01_01_000000_create_users_table.php`.
- Cache tables in `web/database/migrations/0001_01_01_000001_create_cache_table.php`.
- Jobs/batches/failed jobs in `web/database/migrations/0001_01_01_000002_create_jobs_table.php`.

## Authentication Providers And Identity Flows
- Auth guard is Laravel session-based `web` guard in `web/config/auth.php`.
- User provider is Eloquent `App\Models\User` in `web/config/auth.php`.
- Fortify is the auth backend (`web/composer.json`) with custom wiring in `web/app/Providers/FortifyServiceProvider.php`.
- Enabled Fortify features include registration, password reset, email verification, and 2FA in `web/config/fortify.php`.
- Fortify actions are customized via `web/app/Actions/Fortify/CreateNewUser.php` and `web/app/Actions/Fortify/ResetUserPassword.php`.
- Two-factor state is persisted on `users` table via `web/database/migrations/2025_08_14_170933_add_two_factor_columns_to_users_table.php`.
- Livewire settings pages implement profile/password/2FA management under `web/resources/views/pages/settings/` (notably `⚡profile.blade.php`, `⚡password.blade.php`, `⚡two-factor.blade.php`).
- No social OAuth provider integration is present in app code or config (`web/config/services.php` only exposes credential slots).

## Messaging / Email / Notification Integrations
- Default mailer is `log` in `web/.env.example` and `web/config/mail.php`.
- Mail transports are preconfigured for SMTP, SES, Postmark, and Resend in `web/config/mail.php`.
- Service credential placeholders for Postmark/Resend/SES/Slack exist in `web/config/services.php`.
- Slack logging webhook channel is available (via `LOG_SLACK_WEBHOOK_URL`) in `web/config/logging.php` but not hard-enabled by default env.

## Storage / Queue External Backends
- Local/public disks are active defaults in `web/config/filesystems.php`.
- S3 disk integration is configurable in `web/config/filesystems.php` and `web/.env.example` (AWS keys/region/bucket).
- Queue backends support SQS and Redis in `web/config/queue.php`, while current default remains database-backed.

## Webhooks And Inbound Integration Endpoints
- No explicit inbound webhook routes are defined in first-party routes (`web/routes/web.php`, `web/routes/settings.php`, `web/routes/console.php`).
- No `web/routes/api.php` file is present, and route definitions found are `GET`, `view`, redirects, and `Route::livewire(...)` pages.
- Outbound webhook-like capability exists only via Slack logging channel config in `web/config/logging.php`.

## Not Yet Implemented (Documented Intent)
- `web/README.md` includes a TODO for “mailchimp signup form”, indicating planned but currently absent Mailchimp integration.

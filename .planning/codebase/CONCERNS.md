# CONCERNS

## Scope
- This map focuses on technical debt, known issues, security/performance risks, and fragile areas in the current repository layout.
- Concerns are evidence-based and reference concrete files.

## High Risk

1. Split-repo structure increases operational fragility.
- Evidence: root `.gitignore` ignores both `web/` and `app/`.
- Evidence: independent nested repos exist at `web/.git` and `app/.git`.
- Risk: root-level planning/automation can drift from shipped code, and cross-surface changes require multi-repo coordination.

2. Debug tooling route can become externally reachable if debug is enabled outside local.
- Evidence: `web/.env.example` defaults to `APP_ENV=local` and `APP_DEBUG=true`.
- Evidence: `web/composer.json` includes `laravel/boost` (dev dependency).
- Evidence: a route for `POST /_boost/browser-logs` is registered when running the app (`php artisan route:list`).
- Risk: log-ingestion endpoints are attractive for log spam/abuse if debug settings leak into non-local environments.

3. Third-party script supply-chain dependency on the public site.
- Evidence: `web/resources/views/layouts/public.blade.php` loads Google Tag Manager, Font Awesome kit, and AlpineJS from external CDNs.
- Evidence: `web/resources/views/welcome.blade.php` loads Bandsintown widget JS from an external origin.
- Risk: runtime dependence on external scripts increases outage surface and script-trust risk; no in-repo fallback is defined.

## Medium Risk

4. No explicit app-level hardening headers/CSP setup is visible.
- Evidence: `web/bootstrap/app.php` does not add custom security middleware.
- Evidence: `web/public/.htaccess` handles rewrites only; no CSP/HSTS/header policy is defined there.
- Risk: browser-side protections are left to upstream infra defaults and may be inconsistent across environments.

5. Public page has many `target="_blank"` links without `rel="noopener noreferrer"`.
- Evidence: `web/resources/views/layouts/public.blade.php`.
- Evidence: `web/resources/views/welcome.blade.php`.
- Risk: reverse-tabnabbing / opener-link exposure on external navigation.

6. Large asset payload and probable dead/duplicate media increase page and repo weight.
- Evidence: `web/public/images/leather_texture.jpg` is very large (~18MB) while `.webp` variants also exist.
- Evidence: mixed variants in `web/public/images/` (for example both `.jpg/.jpeg` and `.webp` siblings).
- Risk: slower deploys, larger repository footprint, and avoidable frontend transfer costs.

7. Compiled frontend artifacts are committed, which can drift from source.
- Evidence: tracked files in `web/public/build/assets/` and `web/public/build/manifest.json`.
- Evidence: `web/.gitignore` leaves `/public/build` commented out.
- Risk: stale build outputs, noisy merge conflicts, and source-of-truth ambiguity.

8. Database-backed defaults for session/cache/queue are safe for bootstrap but scale poorly.
- Evidence: `web/config/session.php` defaults `SESSION_DRIVER` to `database`.
- Evidence: `web/config/cache.php` defaults cache store to `database`.
- Evidence: `web/config/queue.php` defaults queue connection to `database`.
- Risk: DB contention and latency under load if Redis/memory-backed stores are not configured.

9. Deployment documentation appears stale/incomplete.
- Evidence: `web/README.md` references `pull.php` deployment flow.
- Evidence: no `pull.php` exists under `web/`.
- Risk: institutional knowledge trap and failed/unsafe manual deploy attempts.

## Fragile Areas

10. Marketing page is monolithic and tightly coupled to inline behavior.
- Evidence: `web/resources/views/welcome.blade.php` contains large content, inline scripts, and embed setup in one file.
- Risk: high change blast radius for content edits and greater chance of regressions.

11. Inline DOM scripting is brittle to markup changes.
- Evidence: `web/resources/views/welcome.blade.php` mutates `readMoreLink.childNodes[0].textContent`.
- Risk: small template/i18n changes can silently break behavior.

12. Frontend/public-page coverage is minimal.
- Evidence: `web/tests/Feature/ExampleTest.php` only asserts home route returns 200.
- Evidence: no tests assert behavior/content for `web/resources/views/welcome.blade.php` or `web/resources/views/layouts/public.blade.php`.
- Risk: regressions in public UX, embeds, and client-side interactions are likely to ship undetected.

## Mobile Track Debt

13. Flutter app is still starter-template grade and not integrated with domain features.
- Evidence: default counter app remains in `app/lib/main.dart`.
- Evidence: only template smoke test exists in `app/test/widget_test.dart`.
- Risk: mobile surface is not production-ready and appears disconnected from web/auth flows.

14. Mobile release identity/signing is placeholder and unsafe for release.
- Evidence: TODO comments and debug signing in `app/android/app/build.gradle.kts`.
- Evidence: placeholder bundle IDs (`com.example...`) in `app/android/app/build.gradle.kts`, `app/ios/Runner.xcodeproj/project.pbxproj`, and `app/macos/Runner/Configs/AppInfo.xcconfig`.
- Risk: release-blocking configuration debt and potential accidental debug-signed builds.

15. No visible CI automation for the Flutter app in this repository.
- Evidence: workflows exist under `web/.github/workflows/` for PHP/JS only.
- Evidence: no corresponding `app/.github/workflows/` files are present.
- Risk: mobile regressions rely on manual checks and can drift unnoticed.

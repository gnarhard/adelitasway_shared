# STACK

## Repository Shape
- Top-level repo contains two runtime projects: `web/` (Laravel web app) and `app/` (Flutter app).
- `web/` and `app/` each have their own `.git` directory (`web/.git`, `app/.git`), so this is effectively a multi-project workspace.
- Shared root note exists in `README.md` and references shared state concept.

## Languages And Runtimes
- PHP backend in `web/` with minimum `php: ^8.2` in `web/composer.json`.
- CI runs Laravel tests/lint on PHP `8.4` and `8.5` in `web/.github/workflows/tests.yml` and `web/.github/workflows/lint.yml`.
- JavaScript/Node toolchain in `web/` via Vite + npm (`web/package.json`, `web/package-lock.json`).
- Node `22` is used in CI (`web/.github/workflows/tests.yml`).
- Dart/Flutter app in `app/` with Dart SDK constraint `^3.11.1` in `app/pubspec.yaml` and lockfile SDK range in `app/pubspec.lock`.
- Flutter channel/revision is tracked in `app/.metadata` (`channel: stable`).
- Platform-native stubs are present for Android/Kotlin (`app/android/app/build.gradle.kts`), iOS/Swift (`app/ios/Runner/AppDelegate.swift`), macOS/Swift (`app/macos/Runner/AppDelegate.swift`), and Flutter Web (`app/web/index.html`).

## Core Frameworks
- Laravel framework: `laravel/framework` (constraint `^12.0`) in `web/composer.json`, locked at `v12.51.0` in `web/composer.lock`.
- Auth backend: `laravel/fortify` (constraint `^1.30`) in `web/composer.json`, locked at `v1.34.1` in `web/composer.lock`.
- Reactive UI stack: `livewire/livewire` (constraint `^4.0`) + `livewire/flux` (constraint `^2.9.0`) in `web/composer.json`, locked at `v4.1.4` and `v2.12.0` in `web/composer.lock`.
- CSS/build stack: Tailwind CSS v4 + Vite via `@tailwindcss/vite`, `tailwindcss`, `vite`, `laravel-vite-plugin` in `web/package.json`.
- Flutter app framework: `flutter` SDK dependency in `app/pubspec.yaml`.

## Key Dependencies (Locked Examples)
- PHP: `laravel/framework v12.51.0`, `laravel/fortify v1.34.1`, `livewire/livewire v4.1.4`, `livewire/flux v2.12.0`, `laravel/tinker v2.11.1` from `web/composer.lock`.
- Dev PHP: `laravel/pint v1.27.1`, `pestphp/pest v4.3.2` from `web/composer.lock`.
- JS: `vite 7.3.1`, `tailwindcss 4.1.18`, `@tailwindcss/vite 4.1.18`, `laravel-vite-plugin 2.1.0`, `axios 1.13.5`, `concurrently 9.2.1` from `web/package-lock.json`.
- Dart: `cupertino_icons 1.0.8`, `flutter_lints 6.0.0` from `app/pubspec.lock`.

## App Configuration Surface
- Laravel environment defaults are defined in `web/.env.example` (app URL, DB, queue, session, cache, mail, Redis, AWS).
- Laravel bootstrap routing wires `web`, `console`, and health endpoint `/up` in `web/bootstrap/app.php`.
- Provider registration is explicit in `web/bootstrap/providers.php` (`AppServiceProvider`, `FortifyServiceProvider`).
- Auth/session defaults and guard/provider model are in `web/config/auth.php`.
- DB connections (sqlite/mysql/mariadb/pgsql/sqlsrv) and Redis clients are in `web/config/database.php`.
- Cache/session/queue storage defaults are database-backed in `web/config/cache.php`, `web/config/session.php`, and `web/config/queue.php`, aligned with `web/.env.example` values.
- Filesystem disks (`local`, `public`, optional `s3`) are in `web/config/filesystems.php`.
- Mail transport configuration is in `web/config/mail.php`; third-party credential slots are in `web/config/services.php`.

## Frontend Build And Styling
- Vite entrypoints are `resources/css/app.css` and `resources/js/app.js` in `web/vite.config.js`.
- `web/resources/js/app.js` is currently empty; frontend behavior is mostly Blade + Livewire + Alpine-in-CDN.
- Tailwind v4 is CSS-configured directly via `@theme` and `@source` in `web/resources/css/app.css`.
- Flux CSS is imported from vendor assets in `web/resources/css/app.css`.
- Public landing layout uses `@vite` and CDN scripts in `web/resources/views/layouts/public.blade.php`.

## Testing And Quality Tooling
- PHP tests use Pest (`web/tests/Pest.php`) and PHPUnit config (`web/phpunit.xml`).
- PHP style/lint uses Laravel Pint with preset `laravel` in `web/pint.json`.
- CI workflows run lint and test pipelines in `web/.github/workflows/lint.yml` and `web/.github/workflows/tests.yml`.
- Flutter has default widget testing scaffold in `app/test/widget_test.dart` and analyzer config in `app/analysis_options.yaml`.

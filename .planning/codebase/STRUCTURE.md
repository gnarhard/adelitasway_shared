# Repository Structure (ARCH Scope)

## Top-Level Layout
- `README.md` documents the umbrella repo intent (shared state between Laravel and Flutter).
- `_shared/` exists as a coordination boundary but is currently empty.
- `web/` contains the implemented Laravel 12 + Livewire/Flux web application.
- `app/` contains a Flutter project scaffold with platform folders and starter UI code.
- `.planning/codebase/` contains generated codebase mapping artifacts.

## Laravel App Layout (`web/`)
- Runtime bootstrap: `web/artisan`, `web/bootstrap/app.php`, `web/bootstrap/providers.php`.
- Domain/application code: `web/app/`.
- Route definitions: `web/routes/web.php`, `web/routes/settings.php`, `web/routes/console.php`.
- Configuration: `web/config/*.php` including `web/config/fortify.php`, `web/config/auth.php`, `web/config/database.php`.
- Database schema and factories: `web/database/migrations/`, `web/database/factories/`, `web/database/seeders/`.
- View/UI layer: `web/resources/views/` with `layouts/`, `components/`, `pages/auth/`, `pages/settings/`.
- Frontend assets/build inputs: `web/resources/css/app.css`, `web/resources/js/app.js`, `web/vite.config.js`.
- Public assets: `web/public/images/` and compiled output in `web/public/build/`.
- Test suite: `web/tests/` with Pest bootstrap `web/tests/Pest.php` and feature suites under `web/tests/Feature/`.

## Laravel Internal Organization (`web/app/`)
- `web/app/Providers/` for bootstrap policy and package configuration.
- `web/app/Models/` for Eloquent entities (currently `User.php`).
- `web/app/Actions/Fortify/` for Fortify use-case actions (`CreateNewUser`, `ResetUserPassword`).
- `web/app/Concerns/` for reusable validation rule traits.
- `web/app/Livewire/Actions/` for Livewire-invokable action objects (logout behavior).
- `web/app/Http/Controllers/` currently holds base controller only.

## Flutter App Layout (`app/`)
- Main Dart code currently lives in `app/lib/main.dart` only.
- Platform projects exist under `app/android/`, `app/ios/`, `app/macos/`, and `app/web/`.
- Package/dependency metadata in `app/pubspec.yaml` and lockfile `app/pubspec.lock`.
- Starter widget test in `app/test/widget_test.dart`.

## Naming Conventions Observed
- PHP namespaces follow PSR-4 paths from `web/composer.json` (for example `App\Providers\...` maps to `web/app/Providers/`).
- Action classes use verb-first names (`CreateNewUser`, `ResetUserPassword`) in `web/app/Actions/Fortify/`.
- Shared traits end with `ValidationRules` in `web/app/Concerns/`.
- Route names use dotted identifiers (`dashboard`, `profile.edit`, `user-password.edit`) in `web/routes/web.php` and `web/routes/settings.php`.
- Blade layout/component aliasing uses namespace-style tags (`x-layouts::...`, `x-pages::...`) in files like `web/resources/views/layouts/app.blade.php` and `web/resources/views/pages/auth/login.blade.php`.
- Settings page component files in `web/resources/views/pages/settings/` use a lightning-prefix filename pattern for Livewire page components.
- Tests follow `*Test.php` naming grouped by domain folders in `web/tests/Feature/Auth/` and `web/tests/Feature/Settings/`.

## Key Locations for Architecture Work
- Auth and account workflows: `web/app/Providers/FortifyServiceProvider.php`, `web/resources/views/pages/auth/`, `web/resources/views/pages/settings/`.
- Persistence model and schema: `web/app/Models/User.php`, `web/database/migrations/`.
- UI shell composition: `web/resources/views/layouts/`, `web/resources/views/components/`.
- Build and asset pipeline: `web/vite.config.js`, `web/resources/css/app.css`, `web/resources/js/app.js`.
- Cross-surface integration zone (currently unimplemented): `_shared/` and future API client wiring from `app/lib/` to `web/routes/` endpoints.

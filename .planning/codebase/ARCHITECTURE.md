# Architecture (ARCH Scope)

## System Pattern
- The workspace is organized as a multi-app tree: a Laravel web monolith in `web/`, a Flutter client scaffold in `app/`, and an empty shared boundary directory in `_shared/`.
- The primary implemented runtime is the Laravel app, bootstrapped via `web/bootstrap/app.php` and providers listed in `web/bootstrap/providers.php`.
- Architectural style in `web/` is server-rendered MVC + component-driven UI: route definitions in `web/routes/*.php`, Eloquent model(s) in `web/app/Models/`, and Blade/Livewire UI in `web/resources/views/`.
- Livewire Flux/Volt-style page components are embedded directly in Blade files under `web/resources/views/pages/` (for example settings pages), reducing separate class-file sprawl.
- The Flutter app (`app/lib/main.dart`) is currently an independent starter implementation and is not yet coupled to the Laravel runtime.

## Layering and Responsibilities
- Delivery layer (HTTP/bootstrap): `web/bootstrap/app.php`, `web/routes/web.php`, `web/routes/settings.php`, `web/routes/console.php`.
- Middleware and auth gating are applied at route group boundaries in `web/routes/settings.php` with `auth`, `verified`, and conditional password-confirm middleware.
- Application/service layer is small and explicit: auth lifecycle customization in `web/app/Providers/FortifyServiceProvider.php` and environment defaults in `web/app/Providers/AppServiceProvider.php`.
- Use-case actions are separated from views/controllers through `web/app/Actions/Fortify/CreateNewUser.php` and `web/app/Actions/Fortify/ResetUserPassword.php`.
- Reusable validation policy lives in traits under `web/app/Concerns/PasswordValidationRules.php` and `web/app/Concerns/ProfileValidationRules.php`.
- Domain/data layer is currently centered on `web/app/Models/User.php` plus migrations in `web/database/migrations/`.
- Presentation layer splits into public marketing UI (`web/resources/views/welcome.blade.php` and `web/resources/views/layouts/public.blade.php`) and authenticated app UI (`web/resources/views/layouts/app*.blade.php`, `web/resources/views/pages/settings/`).

## Data Flow
- Public page flow: request to `/` in `web/routes/web.php` returns `welcome` view, composed by `web/resources/views/layouts/public.blade.php`, styled by Vite input `web/resources/css/app.css` configured in `web/vite.config.js`.
- Dashboard flow: `dashboard` route in `web/routes/web.php` requires auth/verified middleware and renders `web/resources/views/dashboard.blade.php` inside Flux-based app layouts.
- Login/registration flow: Fortify view callbacks in `web/app/Providers/FortifyServiceProvider.php` resolve Blade pages in `web/resources/views/pages/auth/`; Fortify then executes auth pipelines and persists session data in tables created by `web/database/migrations/0001_01_01_000000_create_users_table.php`.
- Profile update flow: route alias `pages::settings.profile` in `web/routes/settings.php` loads the inline Livewire component in `web/resources/views/pages/settings/⚡profile.blade.php`, validates via `ProfileValidationRules`, mutates `User`, and dispatches UI events.
- Password update flow: `web/resources/views/pages/settings/⚡password.blade.php` validates current/new password via `PasswordValidationRules` and writes hashed values through `User` casts in `web/app/Models/User.php`.
- Two-factor flow: feature flags in `web/config/fortify.php`, route conditional in `web/routes/settings.php`, and orchestration UI logic in `web/resources/views/pages/settings/⚡two-factor.blade.php`; persistence uses 2FA columns added in `web/database/migrations/2025_08_14_170933_add_two_factor_columns_to_users_table.php`.

## Entry Points
- Web runtime entry: `web/public/index.php` (front controller) + framework bootstrap in `web/bootstrap/app.php`.
- Route entry points: `web/routes/web.php`, `web/routes/settings.php`, `web/routes/console.php`.
- Service registration entry: `web/bootstrap/providers.php`.
- Frontend build entry: `web/vite.config.js` with inputs `web/resources/css/app.css` and `web/resources/js/app.js`.
- Flutter runtime entry: `app/lib/main.dart` with `runApp(const MyApp())`.
- Flutter package/runtime config: `app/pubspec.yaml`.

## Key Abstractions
- Provider abstraction for boot-time policy and package hooks in `web/app/Providers/AppServiceProvider.php` and `web/app/Providers/FortifyServiceProvider.php`.
- Action abstraction for Fortify contracts in `web/app/Actions/Fortify/`.
- Trait abstraction for shared validation rules in `web/app/Concerns/`.
- View component abstraction via Blade namespaces and Flux tags in `web/resources/views/layouts/` and `web/resources/views/components/`.
- Inline page-component abstraction (Volt-like) in `web/resources/views/pages/settings/` where class + template are colocated.

## Architectural Notes
- Most business behavior currently sits in auth/account-management flows; non-auth domain modules are not yet present under `web/app/`.
- The public website experience is mostly static content with selective client-side behavior in `web/resources/views/welcome.blade.php` and external scripts referenced from `web/resources/views/layouts/public.blade.php`.
- Cross-app integration between `web/` and `app/` is not implemented yet: no shared API contract files in `_shared/`, and no mobile API client code beyond Flutter starter code in `app/lib/main.dart`.

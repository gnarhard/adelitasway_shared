# Coding Conventions

**Analysis Date:** 2026-03-05

## Naming Patterns

This repository has two active codebases with different conventions: Flutter in `app/` and Laravel in `web/`.

**Files:**
- PHP classes use PSR-4 paths and PascalCase filenames under `web/app` (for example `web/app/Actions/Fortify/CreateNewUser.php`, `web/app/Providers/FortifyServiceProvider.php`).
- Traits are grouped under `web/app/Concerns` and use descriptive suffixes like `*Rules` (`web/app/Concerns/PasswordValidationRules.php`).
- Database migrations use Laravel timestamped snake_case names in `web/database/migrations`.
- Blade templates are mostly kebab-case in `web/resources/views`, with Livewire/Flux page files including a `⚡` prefix for interactive settings pages (for example `web/resources/views/pages/settings/⚡profile.blade.php`).
- Dart uses snake_case filenames (`app/lib/main.dart`, `app/test/widget_test.dart`).

**Functions and Methods:**
- PHP methods are camelCase and strongly typed (`create(array $input): User`, `configureRateLimiting(): void`) in `web/app/Actions/Fortify/CreateNewUser.php` and `web/app/Providers/FortifyServiceProvider.php`.
- Invokable action pattern is used in `web/app/Livewire/Actions/Logout.php` via `__invoke()`.
- Route definitions use closures for simple endpoints in `web/routes/web.php` and grouped route declarations in `web/routes/settings.php`.
- Dart state internals are private with underscore prefixes (`_counter`, `_incrementCounter`) in `app/lib/main.dart`.

**Variables and Keys:**
- PHP local variables are camelCase (`$throttleKey`, `$verificationUrl`).
- Request/input/database keys are snake_case (`password_confirmation`, `two_factor_secret`) across `web/app` and `web/tests`.

## Code Style

**Formatting:**
- Primary formatter for PHP is Laravel Pint with preset `laravel` (`web/pint.json`).
- Lint command is Composer-driven: `composer lint` runs `pint --parallel` (`web/composer.json`).
- Baseline whitespace and indentation rules are set in `web/.editorconfig` (UTF-8, LF, 4 spaces; YAML set to 2 spaces).
- Flutter static analysis uses `flutter_lints` via `app/analysis_options.yaml` and `app/pubspec.yaml`.

**Linting and Static Checks:**
- CI linter workflow executes Pint in `web/.github/workflows/lint.yml`.
- There is no project-level ESLint or Prettier configuration for `web/resources/js` at this time.

## Import Organization

- PHP files follow `namespace` + `use` imports, then class declaration (example: `web/app/Models/User.php`).
- Imports are generally grouped by project namespace first (`App\...`) followed by Laravel/framework classes (seen in `web/app/Actions/Fortify/CreateNewUser.php` and `web/app/Providers/AppServiceProvider.php`).
- Blade views rely on Laravel helpers (`route()`, `asset()`, `@vite`) rather than direct class imports (`web/resources/views/layouts/public.blade.php`).

## Error Handling

- Validation-first pattern: Fortify actions validate inputs with `Validator::make(...)->validate()` and let framework validation exceptions bubble (`web/app/Actions/Fortify/CreateNewUser.php`, `web/app/Actions/Fortify/ResetUserPassword.php`).
- Conditional behavior is usually expressed through guards and early branching rather than custom exception hierarchies (for example feature toggles/rate limiting in `web/app/Providers/FortifyServiceProvider.php`).
- Production safety guard is explicitly configured with `DB::prohibitDestructiveCommands(app()->isProduction())` in `web/app/Providers/AppServiceProvider.php`.
- No custom application exception classes are currently present under `web/app`.

## Logging

- Logging is configured through Laravel channel config in `web/config/logging.php` (stack/single/daily/slack/syslog/errorlog).
- Default channel comes from environment (`LOG_CHANNEL`, `LOG_LEVEL`) and writes to `storage/logs/laravel.log` for file channels.
- Application code in `web/app` currently has little explicit `Log::` usage; logging behavior is primarily framework-config driven.

## Comments and Documentation

- PHPDoc blocks are used for method intent and array shape/generic hints (`web/app/Concerns/ProfileValidationRules.php`, `web/database/factories/UserFactory.php`).
- Blade templates use section banner comments to organize long page content (`web/resources/views/welcome.blade.php`).
- Flutter starter files contain explanatory comments from the default template (`app/lib/main.dart`, `app/test/widget_test.dart`).

## Function and Module Design

- Service provider methods are decomposed into small private methods (`configureActions`, `configureViews`, `configureRateLimiting`) for readability (`web/app/Providers/FortifyServiceProvider.php`).
- Shared rule logic is extracted into reusable traits (`web/app/Concerns/PasswordValidationRules.php`, `web/app/Concerns/ProfileValidationRules.php`).
- Test data construction is centralized in model factories with explicit named states (`unverified`, `withTwoFactor`) in `web/database/factories/UserFactory.php`.
- Laravel app structure follows framework defaults (`web/app`, `web/routes`, `web/resources`, `web/tests`), while Flutter remains near-template layout in `app/lib` and `app/test`.

*Convention analysis: 2026-03-05*
*Update when lint rules, naming standards, or app structure change*

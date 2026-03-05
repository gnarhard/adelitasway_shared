# Testing Patterns

**Analysis Date:** 2026-03-05

## Test Framework

This repository currently uses two separate test stacks:
- Laravel/PHP tests in `web/tests`.
- Flutter widget tests in `app/test`.

**Laravel Runner and Assertions:**
- Primary framework is Pest v4 (`web/composer.json`, `web/tests/Pest.php`).
- PHPUnit is the underlying runner/config layer (`web/phpunit.xml`, `web/composer.json`).
- Assertions mix Laravel test response assertions and Pest `expect()` expectations (examples in `web/tests/Feature/Auth/AuthenticationTest.php` and `web/tests/Feature/Settings/ProfileUpdateTest.php`).

**Flutter Runner and Assertions:**
- Uses `flutter_test` from SDK (`app/pubspec.yaml`).
- Widget tests use `testWidgets`, `WidgetTester`, and Flutter finders/expectations (`app/test/widget_test.dart`).

**Run Commands:**
```bash
cd web && composer test                            # Clears config, checks Pint style, runs Laravel test suite
cd web && ./vendor/bin/pest                        # Direct Pest run (also used in CI)
cd web && php artisan test                         # Laravel test command used by composer script
cd web && php artisan test --compact --filter=...  # Targeted local run pattern (documented in web/AGENTS.md)
cd web && composer lint                            # Pint formatting pass
cd app && flutter test                             # Flutter widget/unit tests
cd app && flutter analyze                          # Static analysis for Dart/Flutter
```

## Test File Organization

**Laravel Test Layout:**
- Global test bootstrap and defaults: `web/tests/Pest.php`.
- Base test case: `web/tests/TestCase.php`.
- Feature tests under `web/tests/Feature/**`.
- Unit tests under `web/tests/Unit/**`.
- PHPUnit suite registration maps these two directories in `web/phpunit.xml`.

**Flutter Test Layout:**
- Widget test(s) live in `app/test` (currently `app/test/widget_test.dart`).

## Test Structure

**Pest Style:**
- Uses closure-based `test('description', function () { ... });` syntax.
- Uses `beforeEach()` where cross-test setup is needed (for example `web/tests/Feature/Settings/TwoFactorAuthenticationTest.php`).
- Uses route helpers (`route('...')`), auth helpers (`actingAs`), and expressive assertions (`assertOk`, `assertRedirect`, `assertSessionHasErrorsIn`).

**Livewire Component Testing:**
- Interactive settings flows are tested through `Livewire::test(...)` with chained `set()` and `call()` methods.
- Patterns shown in `web/tests/Feature/Settings/ProfileUpdateTest.php` and `web/tests/Feature/Settings/PasswordUpdateTest.php`.

**Flutter Widget Test Style:**
- Arrange/act/assert flow is explicit with comments in `app/test/widget_test.dart`.
- Typical pattern: `pumpWidget` -> trigger UI event -> `pump` -> assert widget tree text/icon state.

## Mocking and Fakes

**What is used in this codebase:**
- Laravel facades faked for side effects:
  - `Notification::fake()` in `web/tests/Feature/Auth/PasswordResetTest.php`.
  - `Event::fake()` in `web/tests/Feature/Auth/EmailVerificationTest.php`.
- No direct Mockery usage appears in committed tests under `web/tests`.

**How behavior is controlled:**
- Feature flags are toggled at runtime in tests (for example `Features::twoFactorAuthentication([...])` in `web/tests/Feature/Settings/TwoFactorAuthenticationTest.php`).
- Session state is set explicitly when needed for auth-gated flows (`withSession(['auth.password_confirmed_at' => time()])`).

## Fixtures and Factories

- User creation is standardized through `User::factory()` across feature tests (`web/tests/Feature/Auth/*`, `web/tests/Feature/Settings/*`).
- Factory states (`unverified`, `withTwoFactor`) are defined in `web/database/factories/UserFactory.php` and reused by tests.
- There is no separate shared `fixtures/` directory at this point; test data is created inline with factories and explicit overrides.

## Coverage and Execution in CI

- CI executes tests in `web/.github/workflows/tests.yml` across a PHP matrix (`8.4`, `8.5`).
- CI enables Xdebug coverage support (`coverage: xdebug`) but does not enforce a minimum coverage threshold in repository config.
- PHPUnit source inclusion is scoped to `web/app` via `web/phpunit.xml`, which defines what coverage reporting targets when enabled.

## Test Types Currently Present

- Feature/integration-heavy HTTP auth and settings tests (`web/tests/Feature/Auth`, `web/tests/Feature/Settings`).
- Livewire interaction tests for profile/password/2FA pages.
- Minimal unit test placeholder in `web/tests/Unit/ExampleTest.php`.
- One Flutter widget smoke test in `app/test/widget_test.dart`.
- No dedicated JavaScript unit test framework (Vitest/Jest) or browser E2E framework config is present for `web/resources/js`.

## Common Patterns to Follow

- Prefer model factories over hand-built records (`web/database/factories/UserFactory.php`).
- Use named route helpers in requests/assertions to avoid brittle URL literals (`web/tests/Feature/**`).
- Assert authentication state explicitly with `$this->assertAuthenticated()` / `$this->assertGuest()` where auth transitions are tested.
- Gate optional feature tests with skip logic rather than hard failures when a feature is disabled (`web/tests/Feature/Auth/AuthenticationTest.php`).
- Keep tests deterministic with in-memory sqlite defaults from `web/phpunit.xml` and `RefreshDatabase` applied to feature tests in `web/tests/Pest.php`.

*Testing analysis: 2026-03-05*
*Update when test tools, CI workflows, or suite structure changes*

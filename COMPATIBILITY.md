# Nova 4 → Laravel 13 compatibility fork

Private, license-bound fork of Laravel Nova **4.32.14** with targeted patches so the package can be required on Laravel 10.48.29+ through Laravel 13.

**This is not an official Laravel product.** You must own a valid Nova license. Do not redistribute.

## Package identity

| Item | Value |
|------|--------|
| Composer name | `laravel/nova` (drop-in private replacement) |
| PHP namespace | `Laravel\Nova\` (unchanged) |
| Base version | 4.32.14 |

## Why this fork exists

Official Nova 4 tops out at Laravel 12. Official Laravel 13 support ships in **Nova 5**. This fork is an interim private bridge for applications that must stay on Nova 4 APIs while moving the framework forward.

## Code changes

### `composer.json`

- Kept Composer name `laravel/nova` for drop-in path/VCS replacement of the official package.
- Widened `illuminate/support` to `^10.48.29|^11.44.1|^12.0|^13.0`.
- Widened `inertiajs/inertia-laravel` to `^0.6\|^1\|^2`.
- Widened Symfony components to `^6|^7|^8`.
- Widened `nesbot/carbon` to `^2.62.1|^3`.
- Require `laravel/ui` `^4.6.3` (provides `AuthenticatesUsers` / password reset traits through Laravel 13).
- Raised PHP to `^8.1`.

### `src/NovaCoreServiceProvider.php`

- Deferred `ServeNova` middleware registration via `booted()` and `pushMiddleware` capability checks for Laravel 11–13 kernel boot timing.
- Guarded Octane / Spatie Once listeners with `class_exists` / method checks.

### `src/Http/Middleware/HandleInertiaRequests.php`

- Hardened `share()` for non-array parent results (Inertia 2).
- Only disable Inertia SSR when the config key exists.

### `src/Fields/FormData.php`

- Widened `FormData::make()` so its declaration stays compatible with `Illuminate\Support\Fluent::make($attributes = [])` (added in Laravel 12.9+ / present in Laravel 13). Without this, PHP fatals on class load: *Declaration of FormData::make(...) must be compatible with Fluent::make(...)*.
- Runtime still requires a `NovaRequest` as the first argument (throws `InvalidArgumentException` otherwise).

## Host application checklist

1. Valid Nova license + `NOVA_LICENSE_KEY` for production hosts.
2. Laravel **≥ 10.48.29** (prefer 11/12/13 on a tested upgrade path).
3. `laravel/ui` `^4.6.3` present (pulled by this package).
4. On Laravel 13, review CSRF middleware rename (`PreventRequestForgery`) in the **app**, not only Nova.
5. On Laravel 13, configure `cache.serializable_classes` if you cache PHP objects.
6. Third-party Nova packages may still need their own Laravel 13 / Nova 5 upgrades.

## Install (path or VCS)

```json
{
  "repositories": [
    {
      "type": "path",
      "url": "../nova4-laravel13",
      "options": { "symlink": true }
    }
  ],
  "require": {
    "laravel/nova": "^4.0"
  }
}
```

Or VCS (private remote):

```json
{
  "repositories": [
    {
      "type": "vcs",
      "url": "git@github.com:YOUR_ORG/nova4-laravel13.git"
    }
  ],
  "require": {
    "laravel/nova": "4.32.14"
  }
}
```

Remove any older path repository that points at an unpatched Nova 4.32.14 tree.

## Verification status

Compatibility is **not claimed** until the consuming Laravel 13 application boots and passes Nova login, dashboard, CRUD, relations, filters, actions, uploads, and authorization tests.

## Rollback

1. Point Composer back at official `laravel/nova` (or the original path copy).
2. Run `composer update laravel/nova --with-dependencies`.
3. Discard this package from the application lockfile.

## Safer long-term path

Upgrade to officially licensed **Nova 5.x** when third-party Nova packages and resources allow it.

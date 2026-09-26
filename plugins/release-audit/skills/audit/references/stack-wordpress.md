# Stack: WordPress / WooCommerce (plugins, themes, sites)

## Baseline commands (use what exists; install dev tools in the project only if composer/npm is available)
- `php -l` on all PHP files (syntax)
- PHPCS with WordPress-Coding-Standards and PHPCompatibilityWP (`vendor/bin/phpcs --standard=WordPress`)
- PHPStan with szepeviktor/phpstan-wordpress if configured
- `composer audit`, `npm audit --omit=dev`
- WP-CLI on staging: `wp plugin list`, `wp core verify-checksums`, `wp option get siteurl`, `wp cron event list`, `wp db check`
- Plugin Check (the official `plugin-check` plugin) on staging for distributed plugins

## Code/security checks specific to WP
- Every form/AJAX/REST write: nonce verified (`check_admin_referer`, `check_ajax_referer`, `wp_verify_nonce`) AND capability checked (`current_user_can`) – nonce is not authorization
- REST routes: `permission_callback` present and not `__return_true` for private data
- Input: `sanitize_*`, `absint`, `wp_unslash`; output: `esc_html`, `esc_attr`, `esc_url`, `wp_kses`
- DB: `$wpdb->prepare` for every query with variables; no string-concatenated SQL
- `admin-ajax` `wp_ajax_nopriv_*` handlers reviewed for data exposure
- File uploads via `wp_handle_upload` with allowed mime types; no direct execution in uploads
- No `eval`, `unserialize` on user data, `extract`, `shell_exec`
- Options with secrets not autoloaded and not exposed via REST
- Direct file access guard (`defined('ABSPATH') || exit;`)
- WooCommerce: HPOS compatibility declared and tested; order meta via CRUD (`$order->get_meta`), not postmeta directly; hooks for stock/price do not break on variable products; taxes/rounding match WooCommerce settings
- Activation/deactivation/uninstall: tables created with `dbDelta`, uninstall cleans up only its own data, upgrade routine versioned
- Cron: `wp_schedule_event` not rescheduled on every page load; long jobs chunked (Action Scheduler)

## Production checks
- `WP_DEBUG` false and `WP_DEBUG_DISPLAY` false in production; `DISALLOW_FILE_EDIT` true
- XML-RPC disabled or protected; user enumeration (`/?author=1`, `/wp-json/wp/v2/users`) blocked
- Login brute-force protection; admin 2FA
- Real cron (system cron hitting wp-cron) instead of traffic-driven wp-cron for critical jobs
- Backup covers both DB and `wp-content/uploads`; restore tested to a separate site

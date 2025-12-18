# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Pantheon WordPress Composer Managed site based on Roots Bedrock. WordPress core and dependencies are managed via Composer instead of traditional file-based WordPress installations.

**Key Architecture:**
- WordPress core is installed as a Composer dependency at `web/wp/`
- Content directories are relocated outside WordPress core:
  - Themes: `web/app/themes/`
  - Plugins: `web/app/plugins/`
  - Must-use plugins: `web/app/mu-plugins/`
- Configuration is environment-aware using `.env` files and the Roots WPConfig system
- The site uses Pantheon's Integrated Composer build process

## Essential Commands

### Dependency Management
```bash
# Install all dependencies
composer install

# Add a plugin from WordPress.org
composer require wpackagist-plugin/{plugin-slug}

# Add a theme from WordPress.org
composer require wpackagist-theme/{theme-slug}

# Add a package from Packagist
composer require {vendor}/{package}

# Update dependencies
composer update
```

### Linting and Code Quality
```bash
# Run all linters
composer lint

# Individual linters
composer lint:php        # Check PHP syntax
composer lint:phpcs      # Check PHP CodeSniffer
composer lint:phpcbf     # Auto-fix PHPCS issues
composer lint:bash       # Check bash scripts with shellcheck
```

### WP-CLI
WP-CLI commands run from the repository root and automatically use `web/wp` as the WordPress path:
```bash
wp {command}
```

### Sage Theme Installation
```bash
composer install-sage
```

## Configuration System

### Environment Configuration Files
- `.env.example` - Template for local environment variables
- `.env.pantheon` - Pantheon-specific environment settings (auto-loaded)
- `.env` or `.env.local` - Local overrides (gitignored)

The configuration loading order is:
1. `web/wp-config.php` - Minimal bootstrap
2. `config/application.php` - Main application config
3. `config/application.pantheon.php` - Pantheon-specific settings
4. `config/environments/{environment}.php` - Environment-specific config (if exists)

### Important Configuration Notes
- **Never modify** `config/application.php` directly - it's maintained by the upstream and changes may cause merge conflicts
- **Add customizations** to `web/wp-config.php` instead
- WordPress admin is at `/wp/wp-admin/` (not `/wp-admin/`)
- Content directory is at `/app` instead of `/wp-content`

## Upstream Configuration

The `upstream-configuration/` directory contains a separate `composer.json` for managing custom upstream dependencies. This allows forking this repository to create custom upstreams where:
- Shared dependencies across all sites go in `upstream-configuration/composer.json`
- Site-specific dependencies stay in the root `composer.json`

## Development Constraints

### Security and File Editing
These constants are defined in production:
- `AUTOMATIC_UPDATER_DISABLED` - true
- `DISALLOW_FILE_EDIT` - true (no file editor in admin)
- `DISALLOW_FILE_MODS` - true (no plugin/theme installs from admin)

All plugin and theme management must be done through Composer.

### Composer Configuration
- Packages are optimized for production (`optimize-autoloader: true`)
- Platform PHP version is pinned to 8.1.31
- Composer patches are enabled via `cweagans/composer-patches`
- Custom installer paths route WordPress plugins/themes/mu-plugins to `web/app/`

### Special Scripts
- `post-install-cmd` - Runs `@maybe-create-symlinks` to handle Lando or local development symlinks
- `pre-update-cmd` - Runs `WordPressComposerManaged\ComposerScripts::preUpdate`
- `post-update-cmd` - Runs `WordPressComposerManaged\ComposerScripts::postUpdate` and symlink creation

The helper scripts are located in `private/scripts/helpers.sh`.

## Code Standards

This project uses the Pantheon WordPress Coding Standards (via `pantheon-systems/pantheon-wp-coding-standards`).

### PHPCS Exclusions
By default, the following are excluded from linting:
- `web/wp` - WordPress core
- `web/app/mu-plugins/*` - MU plugins
- `web/app/plugins/*` - All plugins (remove this exclusion if you want to lint custom plugins)
- `web/app/themes/twentytwentytwo/` - Default theme
- `vendor/` - Composer dependencies

To lint custom plugins, remove the `web/app/plugins/*` exclusion from `phpcs.xml`.

## Pantheon-Specific Details

### Required Plugins
The following Pantheon plugins are pre-installed:
- `pantheon-systems/pantheon-mu-plugin` - Pantheon platform integration
- `wpackagist-plugin/pantheon-advanced-page-cache` - Edge caching
- `wpackagist-plugin/wp-native-php-sessions` - PHP session handling

### Database Configuration
On Pantheon, database connection details are automatically set from `$_ENV['PANTHEON_ENVIRONMENT']`. For local development, use `.env` or `.env.local` to define:
- `DB_NAME`, `DB_USER`, `DB_PASSWORD`, `DB_HOST`
- Or use `DATABASE_URL` as a DSN string

### URLs and HTTPS Detection
- `WP_HOME` and `WP_SITEURL` are set from environment variables
- Reverse proxy HTTPS detection is handled automatically via `HTTP_X_FORWARDED_PROTO`

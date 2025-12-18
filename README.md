# Composer Speed Test Project

This is a test project for comparing Composer installation performance on Pantheon sites.

## Purpose

This project is designed to test and compare the speed of Composer operations when creating identical WordPress sites side by side. It contains a curated set of popular WordPress plugins and Pantheon-specific optimizations to provide a realistic test scenario.

## Installed Plugins

This project includes the following plugins via Composer:

### Popular WordPress Plugins
- **Contact Form 7** (^6.1) - Contact form plugin
- **Akismet** (^5.6) - Spam filtering
- **Jetpack** (^15.3) - Feature suite from Automattic
- **Yoast SEO** (^26.6) - SEO optimization

### Pantheon-Specific Plugins
- **Pantheon Advanced Page Cache** - Page caching optimization
- **WP Native PHP Sessions** - Session handling for Pantheon
- **Pantheon HUD** (^0.4.5) - Environment indicator
- **WP Redis** (^1.4) - Object caching via Redis
- **Pantheon MU Plugin** - Must-use plugin for Pantheon integration

## Technology Stack

- **WordPress**: Managed via [Roots/Bedrock](https://roots.io/bedrock/)
- **Composer**: Dependency management
- **Pantheon**: Integrated Composer build process

## Usage

This project uses the WordPress Composer Managed workflow with Bedrock. The WordPress core is installed as a Composer dependency rather than being committed to the repository.

### Installing Dependencies

```bash
composer install
```

### File Structure

- `web/app/plugins/` - Regular plugins
- `web/app/mu-plugins/` - Must-use plugins
- `web/app/themes/` - Themes
- `web/wp/` - WordPress core (managed by Composer)

## Testing Notes

This project is specifically configured for performance testing and contains a representative set of commonly-used plugins to provide realistic test data for Composer operations.

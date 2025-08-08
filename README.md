# WordPress Plugin Library

A simple, practical foundation for building WordPress plugins faster and more reliably.

## What this library gives you

Instead of starting from scratch every time you build a WordPress plugin, this library provides the essential building blocks you need:

- **Solid foundation** for plugin structure and lifecycle
- **Simple hook system** that eliminates boilerplate code
- **Template engine** for clean HTML/PHP separation
- **API builder** for creating REST endpoints easily
- **Helpful utilities** for common WordPress tasks

## Documentation

Simple, practical guides are available in the [`docs/`](docs/) folder:

- [**Getting Started**](docs/getting-started.md) - Quick setup and overview
- [**Plugin Foundation**](docs/plugin-foundation.md) - Main plugin structure
- [**Hooks and Actions**](docs/hooks-and-actions.md) - Easy WordPress hook integration
- [**Templates and Views**](docs/templates-and-views.md) - Display logic and HTML templates
- [**API Endpoints**](docs/api-endpoints.md) - REST API creation
- [**Utilities**](docs/utilities.md) - Helper functions and tools

## Why use this?

Building WordPress plugins involves a lot of repetitive setup code. This library handles the boring stuff so you can focus on your plugin's unique functionality.

**Before:**
```php
// Lots of manual hook registration
add_action('init', [$this, 'init']);
add_filter('the_content', [$this, 'modifyContent']);
add_action('wp_ajax_my_action', [$this, 'handleAjax']);
// ... and so on
```

**After:**
```php
/**
 * @action init
 */
public function init() { }

/**
 * @filter the_content  
 */
public function modifyContent($content) { }

/**
 * @ajax my_action
 */
public function handleAjax() { }
```

Clean, simple, and less error-prone.

## Quick Start

1. Extend the `Plugin` class in your main plugin file
2. Use annotations in your methods to register hooks
3. Call `YourPlugin::load()` to initialize your plugin

```php
<?php
use GreatScottPlugins\WordPressPlugin\Plugin;

class MyPlugin extends Plugin {
    /**
     * @action init
     */
    public function initialize() {
        // Plugin initialization code
    }
    
    /**
     * @filter the_content
     */
    public function modifyContent($content) {
        return $content . ' - Modified by MyPlugin';
    }
}

// Load the plugin
MyPlugin::load();
```

## Requirements

- PHP 7.4 or higher
- WordPress (any recent version)
- PSR-4 autoloading (via Composer)

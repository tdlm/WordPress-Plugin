# Plugin Class

**Location**: `src/Plugin.php`  
**Namespace**: `GreatScottPlugins\WordPressPlugin`

## Overview

The `Plugin` class is an abstract base class that provides the foundation for WordPress plugins. It extends the `Singleton` class and uses the `Hookable` trait, giving your plugin both singleton behavior and hook management capabilities.

## Purpose

- Provides a standardized structure for WordPress plugins
- Ensures only one instance of your plugin exists (singleton pattern)
- Automatically handles WordPress hook registration via annotations
- Offers utility methods for common plugin operations

## Key Features

### 1. Singleton Behavior
The class extends `Singleton`, ensuring only one instance of your plugin exists throughout the WordPress request lifecycle.

### 2. Hook Management
Uses the `Hookable` trait to provide annotation-based hook registration. Simply add annotations to your methods and they'll be automatically registered with WordPress.

### 3. Plugin Path Utilities
Provides convenient methods to get plugin paths and URLs:

- `dir($path = '')` - Get plugin directory path
- `file()` - Get plugin file path  
- `url($path = '')` - Get plugin URL

## Usage

### Basic Implementation

```php
<?php
use GreatScottPlugins\WordPressPlugin\Plugin;

class MyAwesomePlugin extends Plugin {
    
    /**
     * Initialize the plugin
     * @action init
     */
    public function init() {
        // Plugin initialization code
    }
    
    /**
     * Add custom functionality to content
     * @filter the_content
     */
    public function enhanceContent($content) {
        return $content . '<p>Enhanced by MyAwesome Plugin!</p>';
    }
    
    /**
     * Handle plugin activation
     * @on_activate
     */
    public function onActivation() {
        // Code to run when plugin is activated
        flush_rewrite_rules();
    }
}

// Load the plugin
MyAwesomePlugin::load();
```

### Path Utilities

```php
// Get plugin directory
$plugin_dir = MyAwesomePlugin::dir();
$assets_dir = MyAwesomePlugin::dir('assets/');

// Get plugin URL
$plugin_url = MyAwesomePlugin::url();
$css_url = MyAwesomePlugin::url('assets/style.css');

// Get plugin file path
$plugin_file = MyAwesomePlugin::file();
```

## Methods

### Static Methods

#### `load()`
Initializes the plugin by creating a singleton instance.

#### `dir($path = ''): string`
Gets the plugin directory path, optionally appending a subpath.

- **Parameters**: `$path` - Optional path to append
- **Returns**: Full directory path

#### `file(): string`
Gets the main plugin file path using reflection.

- **Returns**: Path to the main plugin file

#### `url($path = ''): string`
Gets the plugin URL, optionally appending a subpath.

- **Parameters**: `$path` - Optional path to append
- **Returns**: Full plugin URL

## Annotations Supported

When extending the Plugin class, you can use these annotations in your method docblocks:

- `@action hook_name` - Register WordPress action
- `@filter hook_name` - Register WordPress filter
- `@shortcode shortcode_name` - Register WordPress shortcode
- `@ajax action_name` - Register AJAX handler
- `@command command_name` - Register WP-CLI command
- `@on_activate` - Run on plugin activation
- `@on_deactivate` - Run on plugin deactivation

## Best Practices

1. **Always extend Plugin**: Never instantiate Plugin directly - it's abstract
2. **Use annotations**: Leverage the annotation system for clean hook registration
3. **Call load() once**: Only call `YourPlugin::load()` once in your main plugin file
4. **Namespace properly**: Follow PSR-4 autoloading conventions
5. **Type hint parameters**: Use PHP type hints for better code reliability

## Example Plugin Structure

```
my-plugin/
├── my-plugin.php              # Main plugin file
├── src/
│   └── MyPlugin.php          # Your Plugin class
├── assets/
│   ├── css/
│   └── js/
└── templates/
    └── views/
```

**my-plugin.php**:
```php
<?php
/**
 * Plugin Name: My Awesome Plugin
 */

require_once __DIR__ . '/vendor/autoload.php';

MyPlugin::load();
```
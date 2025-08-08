# Hookable Trait

**Location**: `src/Hookable.php`  
**Namespace**: `GreatScottPlugins\WordPressPlugin`

## Overview

The `Hookable` trait provides annotation-based WordPress hook registration. Instead of manually calling `add_action()`, `add_filter()`, etc., you can use PHPDoc annotations in your method comments to automatically register WordPress hooks.

## Purpose

- Simplifies WordPress hook registration through annotations
- Automatically parses method docblocks for hook definitions
- Supports actions, filters, shortcodes, AJAX handlers, and WP-CLI commands
- Provides a clean, declarative way to define WordPress functionality

## Key Features

### 1. Annotation-Based Registration
Use simple annotations in method docblocks to register hooks:

```php
/**
 * @action init
 */
public function initialize() {
    // This method will be hooked to 'init'
}
```

### 2. Multiple Hook Types
Supports all major WordPress hook types:
- Actions (`@action`)
- Filters (`@filter`)
- Shortcodes (`@shortcode`)
- AJAX handlers (`@ajax`)
- WP-CLI commands (`@command`)
- Plugin activation/deactivation (`@on_activate`, `@on_deactivate`)

### 3. Flexible Priority Control
Specify hook priorities directly in annotations:

```php
/**
 * @action init, 5
 */
public function earlyInit() {
    // Runs with priority 5
}
```

### 4. Automatic Parameter Detection
Automatically detects the number of parameters your method accepts and passes that to WordPress.

## Usage

### Basic Hook Registration

```php
<?php
use GreatScottPlugins\WordPressPlugin\Hookable;

class MyFeature {
    use Hookable;
    
    /**
     * Initialize feature
     * @action init
     */
    public function init() {
        // Automatically hooked to 'init' action
    }
    
    /**
     * Modify post content
     * @filter the_content
     */
    public function enhanceContent($content) {
        return $content . '<p>Enhanced!</p>';
    }
    
    /**
     * Register shortcode
     * @shortcode my_shortcode
     */
    public function renderShortcode($atts) {
        return '<p>Hello from shortcode!</p>';
    }
}
```

### Hook Priorities

```php
/**
 * Early initialization
 * @action init, 5
 */
public function earlyInit() {
    // Runs at priority 5
}

/**
 * Late initialization  
 * @action init, 20
 */
public function lateInit() {
    // Runs at priority 20
}
```

### AJAX Handlers

```php
/**
 * Handle AJAX request
 * @ajax my_ajax_action
 */
public function handleAjaxRequest() {
    // Automatically registers:
    // - wp_ajax_my_ajax_action (for logged-in users)
    // - wp_ajax_nopriv_my_ajax_action (for non-logged-in users)
    
    wp_send_json_success(['message' => 'Success!']);
}

/**
 * Auto-named AJAX handler
 * @ajax
 */
public function saveUserData() {
    // Action name auto-generated as 'save_user_data'
    // (method name converted to snake_case)
}
```

### WP-CLI Commands

```php
/**
 * Custom WP-CLI command
 * @command my-plugin import
 */
public function importData($args, $assoc_args) {
    WP_CLI::success('Data imported!');
}
```

### Plugin Lifecycle Hooks

```php
/**
 * Run on plugin activation
 * @on_activate
 */
public function activate() {
    // Create database tables, set default options, etc.
    flush_rewrite_rules();
}

/**
 * Run on plugin deactivation
 * @on_deactivate  
 */
public function deactivate() {
    // Cleanup, remove cron jobs, etc.
    flush_rewrite_rules();
}
```

## Methods

### Public Methods

#### `addDocHooks()`
Parses all methods in the class for hook annotations and registers them with WordPress.

- **Automatically called** by the `Singleton` class
- **Manual usage**: Call this method if not using `Singleton`

#### `addAction($name, $callback, $args = [])`
Manually register a WordPress action hook.

- **Parameters**:
  - `$name` - Hook name
  - `$callback` - Method callback array
  - `$args` - Array with 'priority' and 'arg_count'

#### `addFilter($name, $callback, $args = [])`
Manually register a WordPress filter hook.

- **Parameters**: Same as `addAction()`

#### `addShortcode($name, $callback)`
Manually register a WordPress shortcode.

- **Parameters**:
  - `$name` - Shortcode name
  - `$callback` - Method callback array

### Protected Methods

#### `addHook($type, $name, $callback, $args = [])`
Internal method that handles the actual WordPress hook registration.

## Supported Annotations

### Action Hooks
```php
/**
 * @action hook_name
 * @action hook_name, priority
 */
```

### Filter Hooks
```php
/**
 * @filter hook_name
 * @filter hook_name, priority
 */
```

### Shortcodes
```php
/**
 * @shortcode shortcode_name
 * @shortcode shortcode_name, priority
 */
```

### AJAX Handlers
```php
/**
 * @ajax action_name
 * @ajax  // Auto-generates name from method
 */
```

### WP-CLI Commands
```php
/**
 * @command command_name
 */
```

### Plugin Lifecycle
```php
/**
 * @on_activate
 * @on_deactivate
 */
```

## Advanced Examples

### Multiple Hooks on One Method

```php
/**
 * Handle both actions
 * @action wp_loaded
 * @action admin_init
 */
public function dualInit() {
    // Runs on both wp_loaded and admin_init
}
```

### Complex AJAX with Custom Names

```php
/**
 * Handle user profile updates
 * @ajax update_user_profile
 */
public function handleProfileUpdate() {
    // Available as:
    // wp_ajax_update_user_profile
    // wp_ajax_nopriv_update_user_profile
}
```

### Filter with Priority

```php
/**
 * High priority content filter
 * @filter the_content, 5
 */
public function priorityContentFilter($content) {
    return $content;
}
```

## Best Practices

1. **Descriptive method names**: Use clear method names as they may become AJAX action names
2. **Appropriate priorities**: Use priorities to control execution order
3. **Parameter counts**: Ensure your methods accept the right number of parameters for their hooks
4. **Error handling**: Add proper error handling in hook methods
5. **Documentation**: Document what each hook method does

## Automatic Registration

When using with `Singleton`, hook registration happens automatically:

```php
class MyPlugin extends Singleton {
    use Hookable;
    
    /**
     * @action init
     */
    public function init() {
        // Automatically registered when MyPlugin::instance() is called
    }
}

$plugin = MyPlugin::instance(); // Hooks are now registered
```

## Manual Registration

If not using `Singleton`, call `addDocHooks()` manually:

```php
class MyFeature {
    use Hookable;
    
    public function __construct() {
        $this->addDocHooks();
    }
    
    /**
     * @action init
     */
    public function init() {
        // Will be registered after addDocHooks() is called
    }
}
```

## Dependencies

The trait uses:
- `GreatScottPlugins\WordPressPlugin\Util\Strings` for converting method names to snake_case
- WordPress functions: `add_action()`, `add_filter()`, `add_shortcode()`, etc.
- WP-CLI functions (when available)
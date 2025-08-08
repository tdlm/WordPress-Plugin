# ActionDecoratorHooks Trait

**Location**: `src/Hooks/ActionDecoratorHooks.php`  
**Namespace**: `GreatScottPlugins\WordPressPlugin\Hooks`

## Overview

The `ActionDecoratorHooks` trait is an enhanced version of the `Hookable` trait that provides stricter condition checking and improved hook registration functionality. It includes additional safety measures and enhanced pattern matching for WordPress hook registration.

## Purpose

- Provides enhanced hook registration with stricter validation
- Improves upon the base `Hookable` trait with better condition checking
- Offers the same annotation-based approach with additional safety measures
- Maintains compatibility with the original `Hookable` interface

## Key Differences from Hookable

### 1. Stricter Condition Checking
Uses explicit `false !== preg_match_all()` checks instead of just truthy values, following strict comparison best practices.

### 2. Enhanced Error Handling
Provides better error handling and validation throughout the hook registration process.

### 3. Improved Pattern Matching
Uses more robust regex pattern matching for hook annotations.

## Usage

The usage is identical to the `Hookable` trait, but with enhanced reliability:

```php
<?php
use GreatScottPlugins\WordPressPlugin\Hooks\ActionDecoratorHooks;

class MyEnhancedPlugin {
    use ActionDecoratorHooks;
    
    /**
     * Initialize with enhanced safety
     * @action init
     */
    public function init() {
        // Enhanced hook registration with stricter validation
    }
    
    /**
     * Enhanced filter handling
     * @filter the_content, 10
     */
    public function modifyContent($content) {
        return $content . '<p>Enhanced with ActionDecoratorHooks!</p>';
    }
    
    /**
     * Secure AJAX handling
     * @ajax secure_action
     */
    public function handleSecureAjax() {
        // Enhanced AJAX registration with better validation
        wp_send_json_success(['status' => 'secure']);
    }
}
```

## Methods

The trait provides the same public interface as `Hookable`:

### Public Methods

#### `addDocHooks()`
Enhanced version of doc hook parsing with stricter validation.

#### `addAction($name, $callback, $args = [])`
Registers WordPress action hooks with enhanced validation.

#### `addFilter($name, $callback, $args = [])`
Registers WordPress filter hooks with enhanced validation.

#### `addShortcode($name, $callback)`
Registers WordPress shortcodes with enhanced validation.

### Protected Methods

#### `addHook($type, $name, $callback, $args = [])`
Enhanced internal hook registration with stricter condition checking.

## Enhanced Features

### 1. Strict Validation
```php
// Enhanced condition checking
if (false !== preg_match_all(
    '#\* @(?P<type>filter|action|shortcode)\s+(?P<name>[a-z0-9\/\=\-\._]+)(?:,\s+(?P<priority>\d+))?#',
    $phpdoc,
    $matches,
    PREG_SET_ORDER
)) {
    // Process matches with enhanced safety
}
```

### 2. Improved AJAX Handling
```php
/**
 * Enhanced AJAX handler with automatic naming
 * @ajax
 */
public function processUserData() {
    // Action name auto-generated as 'process_user_data'
    // with enhanced validation and safety checks
}
```

### 3. Better CLI Command Support
```php
/**
 * Enhanced WP-CLI command registration
 * @command my-plugin enhanced-import
 */
public function enhancedImport($args, $assoc_args) {
    WP_CLI::success('Enhanced import completed!');
}
```

## When to Use ActionDecoratorHooks

Choose `ActionDecoratorHooks` over `Hookable` when:

1. **Strict Standards Required**: Your project follows strict PHP coding standards
2. **Enhanced Reliability**: You need the most robust hook registration possible
3. **Production Applications**: Building plugins for production environments where reliability is critical
4. **Large Teams**: Working with large development teams where code consistency is important

## Example Implementation

```php
<?php
use GreatScottPlugins\WordPressPlugin\Plugin;
use GreatScottPlugins\WordPressPlugin\Hooks\ActionDecoratorHooks;

class MyProductionPlugin extends Plugin {
    use ActionDecoratorHooks;
    
    /**
     * Enhanced initialization
     * @action init, 5
     */
    public function earlyInit() {
        // Critical early initialization with enhanced validation
        $this->setupCriticalServices();
    }
    
    /**
     * Enhanced content filtering
     * @filter the_content, 10
     */
    public function enhanceContent($content) {
        if (empty($content)) {
            return $content;
        }
        
        return $content . $this->generateEnhancement();
    }
    
    /**
     * Secure admin AJAX handler
     * @ajax admin_secure_action
     */
    public function handleAdminAction() {
        // Enhanced security checks
        if (!current_user_can('manage_options')) {
            wp_send_json_error('Insufficient permissions');
            return;
        }
        
        // Process action with enhanced validation
        wp_send_json_success(['message' => 'Action completed securely']);
    }
    
    /**
     * Enhanced CLI command
     * @command my-plugin validate-data
     */
    public function validateData($args, $assoc_args) {
        // Enhanced data validation with strict checking
        WP_CLI::line('Starting enhanced data validation...');
        
        $errors = $this->performStrictValidation();
        
        if (empty($errors)) {
            WP_CLI::success('All data validated successfully!');
        } else {
            WP_CLI::error('Validation failed: ' . implode(', ', $errors));
        }
    }
    
    private function setupCriticalServices() {
        // Enhanced service setup
    }
    
    private function generateEnhancement() {
        // Enhanced content generation
        return '<div class="enhanced-content">Enhanced with strict validation</div>';
    }
    
    private function performStrictValidation() {
        // Enhanced validation logic
        return [];
    }
}
```

## Best Practices with ActionDecoratorHooks

1. **Consistent Usage**: Use throughout your entire plugin for consistency
2. **Error Handling**: Take advantage of the enhanced error handling capabilities
3. **Validation**: Leverage the stricter validation for more reliable code
4. **Documentation**: Document the enhanced features you're using
5. **Testing**: Test thoroughly to ensure enhanced features work as expected

## Migration from Hookable

Migrating from `Hookable` to `ActionDecoratorHooks` is straightforward:

```php
// Before
use GreatScottPlugins\WordPressPlugin\Hookable;

class MyPlugin {
    use Hookable;
    // ... rest of code unchanged
}

// After  
use GreatScottPlugins\WordPressPlugin\Hooks\ActionDecoratorHooks;

class MyPlugin {
    use ActionDecoratorHooks;
    // ... rest of code unchanged - interface is identical
}
```

## Technical Implementation

The trait maintains the same public interface while enhancing internal validation:

- **Strict Comparisons**: Uses `=== false` instead of falsy checks
- **Enhanced Regex**: More robust pattern matching for annotations
- **Better Error Handling**: Improved error detection and handling
- **Validation Layer**: Additional validation steps for hook registration

## Dependencies

Same as `Hookable` trait:
- `GreatScottPlugins\WordPressPlugin\Util\Strings`
- WordPress core functions
- WP-CLI (when available)

## Performance Considerations

The enhanced validation comes with minimal performance overhead:
- Slightly more thorough regex matching
- Additional validation steps
- Improved error checking

The performance impact is negligible and the enhanced reliability outweighs any minimal overhead.
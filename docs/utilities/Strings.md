# Strings Utility Class

**Location**: `src/Util/Strings.php`  
**Namespace**: `GreatScottPlugins\WordPressPlugin\Util`

## Overview

The `Strings` utility class provides common string manipulation functions for WordPress plugin development. It offers static methods for various string transformations that are commonly needed in plugin development.

## Purpose

- Provides common string manipulation utilities
- Offers standardized string transformation methods
- Supports WordPress coding conventions
- Enables consistent string handling across plugins

## Key Features

### 1. Case Conversion
Convert strings between different case formats (currently supports snake_case conversion).

### 2. Static Interface
All methods are static for easy access without instantiation.

### 3. WordPress Integration
Designed specifically for WordPress plugin development needs.

## Available Methods

### `toSnakeCase($str): string`

Converts a string from camelCase or PascalCase to snake_case format.

**Parameters:**
- `$str` (string) - The input string to convert

**Returns:**
- `string` - The converted snake_case string

**Usage:**
```php
<?php
use GreatScottPlugins\WordPressPlugin\Util\Strings;

// Convert camelCase to snake_case
$result = Strings::toSnakeCase('myMethodName');
// Returns: 'my_method_name'

$result = Strings::toSnakeCase('getUserData');  
// Returns: 'get_user_data'

// Convert PascalCase to snake_case
$result = Strings::toSnakeCase('MyClassName');
// Returns: 'my_class_name'

$result = Strings::toSnakeCase('DatabaseConnection');
// Returns: 'database_connection'

// Already lowercase strings remain unchanged
$result = Strings::toSnakeCase('already_snake_case');
// Returns: 'already_snake_case'

$result = Strings::toSnakeCase('lowercase');
// Returns: 'lowercase'
```

## Implementation Details

### `toSnakeCase()` Algorithm

The method uses a regular expression to identify uppercase letters that are not at the beginning of the string and adds an underscore before them, then converts the entire string to lowercase.

**Regex Pattern:** `/(?<!^)[A-Z]/`
- `(?<!^)` - Negative lookbehind: not at the start of string
- `[A-Z]` - Matches any uppercase letter

**Replacement:** `_$0` - Adds underscore before the matched letter

## Practical Examples

### AJAX Action Name Generation

The `Hookable` trait uses this utility to auto-generate AJAX action names:

```php
class MyHandler {
    /**
     * @ajax
     */
    public function saveUserProfile() {
        // Action name becomes 'save_user_profile'
        wp_send_json_success(['message' => 'Saved!']);
    }
    
    /**
     * @ajax  
     */
    public function deletePostData() {
        // Action name becomes 'delete_post_data'
        wp_send_json_success(['message' => 'Deleted!']);
    }
}
```

### Database Table Naming

```php
class DatabaseManager {
    
    public function createTable($className) {
        global $wpdb;
        
        $table_name = $wpdb->prefix . Strings::toSnakeCase($className);
        
        // MyUserData becomes wp_my_user_data
        // ProductCatalog becomes wp_product_catalog
        
        $sql = "CREATE TABLE {$table_name} (...)";
        dbDelta($sql);
    }
}
```

### Option Name Generation

```php
class SettingsManager {
    
    public function saveOption($settingClass, $value) {
        $option_name = 'my_plugin_' . Strings::toSnakeCase($settingClass);
        
        // UserPreferences becomes my_plugin_user_preferences
        // EmailSettings becomes my_plugin_email_settings
        
        update_option($option_name, $value);
    }
}
```

### Hook Name Generation

```php
class EventManager {
    
    public function createCustomHook($eventName) {
        $hook_name = 'my_plugin_' . Strings::toSnakeCase($eventName);
        
        // UserLoggedIn becomes my_plugin_user_logged_in
        // DataProcessed becomes my_plugin_data_processed
        
        do_action($hook_name, $data);
    }
}
```

## Extension Examples

While the class currently only has one method, it's designed to be extended:

```php
class ExtendedStrings extends Strings {
    
    /**
     * Convert snake_case to camelCase
     */
    public static function toCamelCase($str) {
        return lcfirst(str_replace('_', '', ucwords($str, '_')));
    }
    
    /**
     * Convert to PascalCase
     */
    public static function toPascalCase($str) {
        return str_replace('_', '', ucwords($str, '_'));
    }
    
    /**
     * Convert to kebab-case
     */
    public static function toKebabCase($str) {
        return strtolower(preg_replace('/(?<!^)[A-Z]/', '-$0', $str));
    }
    
    /**
     * Slugify a string for URLs
     */
    public static function slugify($str) {
        $str = strtolower($str);
        $str = preg_replace('/[^a-z0-9-_]/', '-', $str);
        $str = preg_replace('/-+/', '-', $str);
        return trim($str, '-');
    }
}

// Usage
$camel = ExtendedStrings::toCamelCase('user_profile_data');    // userProfileData
$pascal = ExtendedStrings::toPascalCase('user_profile_data');  // UserProfileData  
$kebab = ExtendedStrings::toKebabCase('UserProfileData');      // user-profile-data
$slug = ExtendedStrings::slugify('My Awesome Plugin!');       // my-awesome-plugin
```

## WordPress Integration

The utility is designed to work seamlessly with WordPress conventions:

```php
class WordPressIntegration {
    
    public function registerPostType($className) {
        $post_type = Strings::toSnakeCase($className);
        
        // ProductCatalog becomes product_catalog
        register_post_type($post_type, [
            'public' => true,
            'label' => ucwords(str_replace('_', ' ', $post_type))
        ]);
    }
    
    public function registerTaxonomy($className, $postType) {
        $taxonomy = Strings::toSnakeCase($className);
        $post_type = Strings::toSnakeCase($postType);
        
        // ProductCategory, ProductCatalog becomes product_category, product_catalog
        register_taxonomy($taxonomy, $post_type, [
            'public' => true,
            'hierarchical' => true
        ]);
    }
    
    public function addMetaBox($className, $postType) {
        $meta_box_id = Strings::toSnakeCase($className) . '_meta';
        $post_type = Strings::toSnakeCase($postType);
        
        add_meta_box(
            $meta_box_id,
            ucwords(str_replace('_', ' ', Strings::toSnakeCase($className))),
            [$this, 'renderMetaBox'],
            $post_type
        );
    }
}
```

## Performance Considerations

The `toSnakeCase()` method is lightweight and efficient:

- Uses a single regular expression operation
- Minimal memory overhead
- Suitable for frequent use (like AJAX action generation)
- No external dependencies

## Best Practices

1. **Consistent Naming**: Use for consistent naming conventions across your plugin
2. **Cache Results**: For frequently converted strings, consider caching results
3. **Validation**: Validate input strings when necessary
4. **Documentation**: Document string format expectations in your code

## Testing Examples

```php
class StringsTest extends PHPUnit_TestCase {
    
    public function testToSnakeCase() {
        // Test camelCase conversion
        $this->assertEquals('my_method', Strings::toSnakeCase('myMethod'));
        $this->assertEquals('get_user_data', Strings::toSnakeCase('getUserData'));
        
        // Test PascalCase conversion  
        $this->assertEquals('my_class', Strings::toSnakeCase('MyClass'));
        $this->assertEquals('database_connection', Strings::toSnakeCase('DatabaseConnection'));
        
        // Test edge cases
        $this->assertEquals('a', Strings::toSnakeCase('A'));
        $this->assertEquals('lowercase', Strings::toSnakeCase('lowercase'));
        $this->assertEquals('already_snake', Strings::toSnakeCase('already_snake'));
        $this->assertEquals('', Strings::toSnakeCase(''));
        
        // Test complex cases
        $this->assertEquals('html_to_pdf_converter', Strings::toSnakeCase('HTMLToPDFConverter'));
        $this->assertEquals('xml_http_request', Strings::toSnakeCase('XMLHttpRequest'));
    }
}
```

## Future Enhancements

The class is designed to be extended with additional string utilities as needed:

- Case conversions (camelCase, PascalCase, kebab-case)
- Pluralization/singularization
- String validation methods
- Encoding/decoding utilities
- WordPress-specific string formatters

## Dependencies

- None - uses only native PHP functions
- Compatible with PHP 7.4+
- No WordPress dependencies (pure utility class)
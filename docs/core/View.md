# View Class

**Location**: `src/View.php`  
**Namespace**: `GreatScottPlugins\WordPressPlugin`

## Overview

The `View` class provides a simple and elegant way to handle template rendering in WordPress plugins. It offers both static and instance-based methods for including and rendering PHP templates with data passing capabilities.

## Purpose

- Simplifies template rendering in WordPress plugins
- Provides clean separation between logic and presentation
- Offers flexible data passing to templates
- Supports both direct inclusion and string rendering
- Includes shortcode rendering utilities

## Key Features

### 1. Template Rendering
Render PHP templates with data and return as strings or include directly.

### 2. Data Passing
Pass associative arrays of data to templates for use within the template scope.

### 3. Static Convenience Methods
Use static methods for quick template operations without instantiation.

### 4. WordPress Integration
Leverages WordPress's `load_template()` function for proper template handling.

### 5. Shortcode Support
Includes utility methods for rendering widgets as shortcodes.

## Usage

### Basic Template Rendering

```php
<?php
use GreatScottPlugins\WordPressPlugin\View;

// Static usage - render and return as string
$html = View::template('/path/to/template', [
    'title' => 'My Page',
    'content' => 'Hello World!',
    'items' => ['item1', 'item2', 'item3']
]);

echo $html;
```

### Template Inclusion

```php
// Static usage - include template directly
View::includeTemplate('/path/to/template', [
    'user' => $current_user,
    'settings' => $plugin_settings
]);
```

### Instance-Based Usage

```php
// Create view instance
$view = new View('/path/to/template', [
    'data' => $some_data,
    'config' => $config_array
]);

// Include the template
$view->include();

// Or render to string
$output = $view->render();
```

### Template Files

Templates are standard PHP files that receive data via the global scope:

**templates/user-profile.php**:
```php
<div class="user-profile">
    <h2><?php echo esc_html($title); ?></h2>
    <p>Welcome, <?php echo esc_html($user->display_name); ?>!</p>
    
    <?php if (!empty($items)): ?>
        <ul>
            <?php foreach ($items as $item): ?>
                <li><?php echo esc_html($item); ?></li>
            <?php endforeach; ?>
        </ul>
    <?php endif; ?>
</div>
```

**Using the template**:
```php
$html = View::template(Plugin::dir('templates/user-profile'), [
    'title' => 'User Dashboard',
    'user' => wp_get_current_user(),
    'items' => ['Dashboard', 'Profile', 'Settings']
]);
```

## Methods

### Static Methods

#### `template($path, $data = []): string`
Renders a template and returns the output as a string.

- **Parameters**:
  - `$path` - Path to template file (without .php extension)
  - `$data` - Associative array of data to pass to template
- **Returns**: Rendered template as string

```php
$output = View::template('/path/to/template', ['key' => 'value']);
```

#### `includeTemplate($path, $data = [])`
Includes a template file directly (outputs immediately).

- **Parameters**:
  - `$path` - Path to template file (without .php extension)  
  - `$data` - Associative array of data to pass to template
- **Returns**: void (outputs directly)

```php
View::includeTemplate('/path/to/template', ['key' => 'value']);
```

#### `shortcode($shortcode, $atts): string`
Renders a widget as a shortcode and returns the output.

- **Parameters**:
  - `$shortcode` - Widget class name
  - `$atts` - Widget attributes array
- **Returns**: Rendered widget as string

```php
$widget_output = View::shortcode('WP_Widget_Calendar', [
    'title' => 'Calendar'
]);
```

### Instance Methods

#### `__construct($path, $data = [])`
Creates a new View instance.

- **Parameters**:
  - `$path` - Path to template file (without .php extension)
  - `$data` - Associative array of data to pass to template

#### `include()`
Includes the template file directly (outputs immediately).

```php
$view = new View('/path/to/template', $data);
$view->include(); // Outputs template
```

#### `render(): string`
Renders the template and returns output as a string.

```php
$view = new View('/path/to/template', $data);
$html = $view->render(); // Returns template as string
```

## Advanced Examples

### Complex Data Passing

```php
class BlogRenderer {
    
    public function renderPostList($posts) {
        return View::template(Plugin::dir('templates/post-list'), [
            'posts' => $posts,
            'current_user' => wp_get_current_user(),
            'site_url' => home_url(),
            'date_format' => get_option('date_format'),
            'helper' => $this // Pass the class instance for helper methods
        ]);
    }
    
    public function formatDate($date) {
        return date('F j, Y', strtotime($date));
    }
}
```

**Template (templates/post-list.php)**:
```php
<div class="post-list">
    <?php foreach ($posts as $post): ?>
        <article class="post">
            <h3><a href="<?php echo get_permalink($post); ?>">
                <?php echo esc_html($post->post_title); ?>
            </a></h3>
            
            <time datetime="<?php echo $post->post_date; ?>">
                <?php echo $helper->formatDate($post->post_date); ?>
            </time>
            
            <div class="excerpt">
                <?php echo wpautop($post->post_excerpt); ?>
            </div>
        </article>
    <?php endforeach; ?>
</div>
```

### Template Hierarchy

```php
class ThemeRenderer {
    
    public function render($template_name, $data = []) {
        // Try plugin template first, fall back to theme
        $plugin_template = Plugin::dir("templates/{$template_name}");
        $theme_template = get_template_directory() . "/plugin-templates/{$template_name}";
        
        if (file_exists($plugin_template . '.php')) {
            return View::template($plugin_template, $data);
        } elseif (file_exists($theme_template . '.php')) {
            return View::template($theme_template, $data);
        }
        
        return '<p>Template not found</p>';
    }
}
```

### AJAX Response Rendering

```php
class AjaxHandler {
    
    /**
     * @ajax load_user_widget
     */
    public function loadUserWidget() {
        $user_id = intval($_POST['user_id']);
        $user = get_user_by('id', $user_id);
        
        if (!$user) {
            wp_send_json_error('User not found');
            return;
        }
        
        $html = View::template(Plugin::dir('templates/user-widget'), [
            'user' => $user,
            'meta' => get_user_meta($user_id)
        ]);
        
        wp_send_json_success(['html' => $html]);
    }
}
```

## Best Practices

1. **Template Organization**: Keep templates in a dedicated `templates/` directory
2. **Data Escaping**: Always escape output in templates using `esc_html()`, `esc_attr()`, etc.
3. **Path Management**: Use `Plugin::dir()` to get proper template paths
4. **Template Extensions**: Don't include `.php` in template paths - it's added automatically
5. **Data Structure**: Use associative arrays for clear, named data passing
6. **Separation of Concerns**: Keep business logic out of templates
7. **Template Caching**: Consider caching rendered templates for performance

## Template Best Practices

### Security
```php
<!-- Good: Escaped output -->
<h1><?php echo esc_html($title); ?></h1>
<img src="<?php echo esc_url($image_url); ?>" alt="<?php echo esc_attr($alt_text); ?>">

<!-- Bad: Unescaped output -->
<h1><?php echo $title; ?></h1>
```

### Conditional Rendering
```php
<?php if (!empty($items)): ?>
    <ul>
        <?php foreach ($items as $item): ?>
            <li><?php echo esc_html($item); ?></li>
        <?php endforeach; ?>
    </ul>
<?php else: ?>
    <p>No items found.</p>
<?php endif; ?>
```

### Helper Methods
```php
// Pass helper objects or functions in data
$html = View::template('my-template', [
    'items' => $items,
    'format_date' => function($date) {
        return date('F j, Y', strtotime($date));
    }
]);
```

## Error Handling

The View class relies on WordPress's `load_template()` function, which will trigger PHP errors if template files don't exist. Always ensure template files exist or implement your own existence checking:

```php
class SafeView extends View {
    
    public static function safeTemplate($path, $data = []) {
        if (!file_exists($path . '.php')) {
            return '<p>Template not found: ' . esc_html($path) . '</p>';
        }
        
        return parent::template($path, $data);
    }
}
```
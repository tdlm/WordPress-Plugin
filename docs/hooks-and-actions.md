# Hooks and Actions

WordPress works through a system of hooks and actions. This library makes it super easy to connect your code to these hooks without writing repetitive code.

## What it does

Instead of manually calling `add_action()` and `add_filter()` everywhere, you just add simple comments to your methods:

```php
/**
 * @action init
 */
public function doSomething() {
    // This runs when WordPress initializes
}

/**
 * @filter the_content
 */
public function modifyContent($content) {
    return $content . ' - Enhanced!';
}
```

## Types of hooks supported

- **Actions** - Run code at specific times
- **Filters** - Modify content or data
- **AJAX** - Handle frontend requests
- **Shortcodes** - Create custom content blocks
- **WP-CLI commands** - Add command line tools

## Why this is useful

- Less repetitive code
- Cleaner, more readable code
- Automatic hook registration
- Fewer bugs from manual hook setup

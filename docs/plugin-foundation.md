# Plugin Foundation

This is the main building block for creating WordPress plugins. It handles all the basic setup and structure so you don't have to worry about the technical details.

## What it does

- Creates a single instance of your plugin (no duplicates)
- Automatically connects your code to WordPress hooks
- Provides helper methods to find files and URLs in your plugin
- Handles plugin activation and deactivation

## How to use it

Create your main plugin class by extending the Plugin class:

```php
class MyPlugin extends Plugin {
    /**
     * @action init
     */
    public function start() {
        // Your plugin starts here
    }
}

MyPlugin::load();
```

## Why this matters

Instead of writing boilerplate code for every plugin, you get a solid foundation that follows WordPress best practices. Your plugin will be more reliable and easier to maintain.

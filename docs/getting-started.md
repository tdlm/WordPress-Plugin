# Getting Started

This WordPress plugin library gives you a solid foundation to build plugins faster and more reliably.

## What you get

- **Plugin Foundation** - Main plugin structure and lifecycle management
- **Hook System** - Easy WordPress hook registration with annotations
- **Template Engine** - Clean separation of display logic and data
- **API Builder** - Simple REST API endpoint creation
- **Utilities** - Helper functions for common tasks

## Quick setup

1. Install via Composer in your plugin directory
2. Create your main plugin class
3. Add hook annotations to your methods
4. Load your plugin

```php
<?php
use GreatScottPlugins\WordPressPlugin\Plugin;

class MyAwesomePlugin extends Plugin {
    /**
     * @action init
     */
    public function initialize() {
        // Your plugin starts here
    }
}

MyAwesomePlugin::load();
```

## What makes this different

Instead of copying and pasting boilerplate code for every plugin, you get:
- Consistent structure across all your plugins
- Fewer bugs from manual setup
- Faster development time
- Better code organization
- WordPress best practices built-in

## Next steps

- Read about the [Plugin Foundation](plugin-foundation.md)
- Learn how [Hooks and Actions](hooks-and-actions.md) work
- Explore [Templates and Views](templates-and-views.md) for display logic

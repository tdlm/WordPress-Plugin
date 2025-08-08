# WordPress-Plugin

WordPress Plugin Class with Hookable

A structured foundation for building WordPress plugins using modern PHP patterns and practices.

## Features

- **Singleton Pattern**: Ensures single instance of plugin classes
- **Hook Management**: Annotation-based WordPress hook registration  
- **View System**: Template rendering and management
- **API Support**: REST API endpoint registration
- **Utility Functions**: Common string manipulation functions

## Documentation

Comprehensive documentation is available in the [`docs/`](docs/) folder:

- [**Getting Started**](docs/README.md) - Overview and quick start guide
- [**Core Components**](docs/core/) - Main classes and traits
- [**Hook Decorators**](docs/hooks/) - Advanced hook management
- [**Utilities**](docs/utilities/) - Helper functions and utilities

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

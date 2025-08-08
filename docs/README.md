# WordPress Plugin Library Documentation

This library provides a structured foundation for building WordPress plugins using modern PHP patterns and practices.

## Overview

The Great Scott Plugins WordPress Plugin library is designed to simplify WordPress plugin development by providing:

- **Singleton Pattern**: Ensures single instance of plugin classes
- **Hook Management**: Annotation-based WordPress hook registration
- **View System**: Template rendering and management
- **API Support**: REST API endpoint registration
- **Utility Functions**: Common string manipulation functions

## Architecture

The library follows these key design principles:

- **Trait-based Composition**: Functionality is provided through traits that can be mixed into your classes
- **Annotation-driven**: Use PHPDoc annotations to register hooks, commands, and API endpoints
- **WordPress Best Practices**: Follows WordPress coding standards and conventions
- **Type Safety**: Uses PHP 7.4+ type hints for better code reliability

## Components

### Core Components

- [**Plugin**](core/Plugin.md) - Abstract base class for WordPress plugins
- [**Singleton**](core/Singleton.md) - Singleton pattern implementation
- [**Hookable**](core/Hookable.md) - WordPress hook management trait
- [**View**](core/View.md) - Template rendering system

### Hook Decorators

- [**ActionDecoratorHooks**](hooks/ActionDecoratorHooks.md) - Enhanced hook management
- [**ApiDecoratorHooks**](hooks/ApiDecoratorHooks.md) - REST API endpoint registration

### Utilities

- [**Strings**](utilities/Strings.md) - String manipulation utilities

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
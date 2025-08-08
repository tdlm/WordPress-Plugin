# Singleton Class

**Location**: `src/Singleton.php`  
**Namespace**: `GreatScottPlugins\WordPressPlugin`

## Overview

The `Singleton` class is an abstract class that implements the Singleton design pattern for WordPress plugins. It ensures that only one instance of a class can exist during a request lifecycle and provides automatic initialization capabilities.

## Purpose

- Implements the Singleton design pattern
- Ensures single instance per class throughout the application
- Provides automatic hook registration via the `Hookable` trait
- Offers initialization lifecycle management

## Key Features

### 1. Single Instance Guarantee
Prevents multiple instances of the same class from being created, which is crucial for WordPress plugins to avoid conflicts and duplicate functionality.

### 2. Automatic Hook Registration
Uses the `Hookable` trait to automatically parse method annotations and register WordPress hooks.

### 3. Initialization Lifecycle
Provides an `init()` method hook that gets called automatically when the instance is created.

### 4. Protection Against Common Singleton Violations
Prevents cloning, unserialization, and direct instantiation.

## Usage

### Basic Implementation

```php
<?php
use GreatScottPlugins\WordPressPlugin\Singleton;

class MyService extends Singleton {
    
    private $data = [];
    
    /**
     * Initialize the service
     * Called automatically when instance is created
     */
    public function init() {
        $this->data = [
            'initialized_at' => time(),
            'status' => 'ready'
        ];
    }
    
    /**
     * @action wp_loaded
     */
    public function onWordPressLoaded() {
        // This hook will be automatically registered
        do_something();
    }
    
    public function getData() {
        return $this->data;
    }
}

// Get the singleton instance
$service = MyService::instance();
$same_service = MyService::instance(); // Returns the same instance

var_dump($service === $same_service); // true
```

### With Parameters

```php
class ConfigurableService extends Singleton {
    
    private $config;
    
    public function init($config = []) {
        $this->config = array_merge([
            'debug' => false,
            'cache' => true
        ], $config);
    }
}

// Pass parameters to init method
$service = ConfigurableService::instance(['debug' => true]);
```

## Methods

### Static Methods

#### `instance(...$params): Singleton`
Creates or returns the singleton instance of the class.

- **Parameters**: `...$params` - Parameters passed to the `init()` method
- **Returns**: The singleton instance
- **Behavior**:
  - Creates new instance if none exists
  - Calls `addDocHooks()` automatically if available
  - Calls `init()` method if defined
  - Returns existing instance on subsequent calls

## Protected/Private Methods

#### `__construct()`
Private constructor prevents direct instantiation. Must use `instance()` method instead.

#### `__clone()`
Prevents cloning of the singleton instance.

#### `__wakeup()`
Prevents unserialization of the singleton instance.

## Instance Management

The class maintains instances in a static array `$instances` indexed by class name, allowing different singleton classes to coexist:

```php
$service1 = ServiceA::instance();
$service2 = ServiceB::instance();
$service1_again = ServiceA::instance();

// $service1 and $service1_again are the same instance
// $service2 is a different singleton instance
```

## Lifecycle Hooks

### `init(...$params)`
Optional method called automatically when the singleton instance is first created.

```php
public function init($param1, $param2) {
    // Initialization code here
    // Parameters come from instance() call
}
```

### `addDocHooks()`
Automatically called if the method exists. This comes from the `Hookable` trait and parses method annotations to register WordPress hooks.

## Best Practices

1. **Always use instance()**: Never try to instantiate directly with `new`
2. **Initialize in init()**: Put setup code in the `init()` method, not the constructor
3. **Stateful operations**: Use singletons for services that need to maintain state
4. **Avoid global state**: While singletons are global, try to keep their interface clean
5. **Testing considerations**: Singletons can make unit testing harder - consider dependency injection

## Common Patterns

### Service Registration

```php
class PluginServices extends Singleton {
    
    private $services = [];
    
    public function register($name, $service) {
        $this->services[$name] = $service;
    }
    
    public function get($name) {
        return $this->services[$name] ?? null;
    }
}

// Usage
$registry = PluginServices::instance();
$registry->register('mailer', new MailService());
$mailer = $registry->get('mailer');
```

### Configuration Management

```php
class PluginConfig extends Singleton {
    
    private $settings = [];
    
    public function init() {
        $this->settings = get_option('my_plugin_settings', []);
    }
    
    public function get($key, $default = null) {
        return $this->settings[$key] ?? $default;
    }
    
    public function set($key, $value) {
        $this->settings[$key] = $value;
        update_option('my_plugin_settings', $this->settings);
    }
}
```

## When to Use Singleton

**Good use cases:**
- Plugin main classes
- Service registries
- Configuration managers
- Logger instances
- Database connection managers

**Avoid for:**
- Simple data containers
- Stateless utility classes
- Classes that need multiple instances
- Classes used in unit tests (without proper mocking)
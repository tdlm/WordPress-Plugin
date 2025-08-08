# ApiDecoratorHooks Trait

**Location**: `src/Hooks/ApiDecoratorHooks.php`  
**Namespace**: `GreatScottPlugins\WordPressPlugin\Hooks`

## Overview

The `ApiDecoratorHooks` trait provides a powerful system for registering WordPress REST API endpoints using class-level annotations. It simplifies API development by allowing you to define API configuration directly in your class docblocks and automatically register routes during WordPress initialization.

## Purpose

- Simplifies WordPress REST API endpoint registration
- Uses annotation-based configuration for API settings
- Provides structured approach to API versioning and namespacing
- Automatically handles route registration with WordPress
- Supports complex API hierarchies with subspaces

## Key Features

### 1. Annotation-Based Configuration
Define API settings using class docblock annotations:

```php
/**
 * My API Controller
 * @api-namespace my-plugin
 * @api-version 2
 * @api-subspace users
 */
class UserApiController {
    use ApiDecoratorHooks;
}
```

### 2. Automatic Route Registration
Routes are automatically registered during the `rest_api_init` action.

### 3. Flexible API Structure
Supports complex API structures with namespaces, versions, and subspaces.

### 4. Dynamic Route Management
Routes can be set programmatically and managed at runtime.

## Usage

### Basic API Setup

```php
<?php
use GreatScottPlugins\WordPressPlugin\Hooks\ApiDecoratorHooks;

/**
 * User API Controller
 * @api-namespace my-plugin
 * @api-version 1
 */
class UserApiController {
    use ApiDecoratorHooks;
    
    public function __construct() {
        // Define API routes
        self::setApiRoutes([
            '/users' => [
                'methods' => 'GET',
                'callback' => [$this, 'getUsers'],
                'permission_callback' => '__return_true'
            ],
            '/users/(?P<id>\d+)' => [
                'methods' => 'GET',
                'callback' => [$this, 'getUser'],
                'permission_callback' => '__return_true'
            ]
        ]);
        
        // Hooks are automatically registered
        $this->addApiHooks();
    }
    
    public function getUsers($request) {
        // Return all users
        return rest_ensure_response([
            'users' => get_users(['number' => 10])
        ]);
    }
    
    public function getUser($request) {
        $user_id = $request['id'];
        $user = get_user_by('id', $user_id);
        
        if (!$user) {
            return new WP_Error('user_not_found', 'User not found', ['status' => 404]);
        }
        
        return rest_ensure_response(['user' => $user]);
    }
}

// Initialize the API
new UserApiController();
```

This creates endpoints accessible at:
- `GET /wp-json/my-plugin/v1/users`
- `GET /wp-json/my-plugin/v1/users/123`

### With Subspace

```php
/**
 * Admin User API Controller  
 * @api-namespace my-plugin
 * @api-version 2
 * @api-subspace admin
 */
class AdminUserApiController {
    use ApiDecoratorHooks;
    
    public function __construct() {
        self::setApiRoutes([
            '/users' => [
                'methods' => ['GET', 'POST'],
                'callback' => [$this, 'handleUsers'],
                'permission_callback' => [$this, 'checkAdminPermission']
            ]
        ]);
        
        $this->addApiHooks();
    }
    
    public function checkAdminPermission() {
        return current_user_can('manage_options');
    }
}
```

This creates endpoints at:
- `GET/POST /wp-json/my-plugin/v2/admin/users`

## Methods

### Static Configuration Methods

#### `setApiNamespace($namespace)`
Sets the API namespace.

```php
self::setApiNamespace('my-plugin');
```

#### `setApiVersion($version)`
Sets the API version.

```php
self::setApiVersion('2');
```

#### `setApiSubspace($subspace)`
Sets the API subspace (optional).

```php
self::setApiSubspace('admin');
```

#### `setApiRoutes($routes)`
Sets the API routes array.

```php
self::setApiRoutes([
    '/endpoint' => [
        'methods' => 'GET',
        'callback' => [$this, 'method'],
        'permission_callback' => '__return_true'
    ]
]);
```

### Static Getter Methods

#### `getApiNamespace(): string`
Returns the current API namespace.

#### `getApiVersion(): string`
Returns the current API version.

#### `getApiSubspace(): string`
Returns the current API subspace.

#### `getApiRoutes(): array`
Returns the current API routes array.

### Registration Methods

#### `registerEndpoints()`
Registers all defined routes with WordPress. Automatically called on `rest_api_init`.

#### `addApiHooks()`
Parses class annotations and sets up API configuration. Call this manually or use with automatic initialization.

## Supported Annotations

### Class-Level Annotations

#### `@api-namespace`
```php
/**
 * @api-namespace my-plugin
 */
```
Sets the API namespace. Required for route registration.

#### `@api-version`
```php
/**
 * @api-version 2
 */
```
Sets the API version. Defaults to '1' if not specified.

#### `@api-subspace`
```php
/**
 * @api-subspace admin
 */
```
Sets an optional API subspace for grouping related endpoints.

## Advanced Examples

### Complex API Structure

```php
/**
 * E-commerce API Controller
 * @api-namespace my-store
 * @api-version 3
 * @api-subspace products
 */
class ProductApiController {
    use ApiDecoratorHooks;
    
    public function __construct() {
        self::setApiRoutes([
            '/products' => [
                [
                    'methods' => 'GET',
                    'callback' => [$this, 'getProducts'],
                    'permission_callback' => '__return_true',
                    'args' => [
                        'page' => [
                            'validate_callback' => function($param) {
                                return is_numeric($param);
                            }
                        ]
                    ]
                ],
                [
                    'methods' => 'POST',
                    'callback' => [$this, 'createProduct'],
                    'permission_callback' => [$this, 'canManageProducts']
                ]
            ],
            '/products/(?P<id>\d+)' => [
                [
                    'methods' => 'GET',
                    'callback' => [$this, 'getProduct'],
                    'permission_callback' => '__return_true'
                ],
                [
                    'methods' => 'PUT',
                    'callback' => [$this, 'updateProduct'],
                    'permission_callback' => [$this, 'canManageProducts']
                ],
                [
                    'methods' => 'DELETE',
                    'callback' => [$this, 'deleteProduct'],
                    'permission_callback' => [$this, 'canManageProducts']
                ]
            ],
            '/products/(?P<id>\d+)/variants' => [
                'methods' => 'GET',
                'callback' => [$this, 'getProductVariants'],
                'permission_callback' => '__return_true'
            ]
        ]);
        
        $this->addApiHooks();
    }
    
    public function getProducts($request) {
        $page = $request->get_param('page') ?: 1;
        // Implementation...
    }
    
    public function canManageProducts() {
        return current_user_can('manage_woocommerce');
    }
}
```

This creates endpoints like:
- `GET /wp-json/my-store/v3/products/products`
- `POST /wp-json/my-store/v3/products/products`
- `GET /wp-json/my-store/v3/products/products/123`
- `PUT /wp-json/my-store/v3/products/products/123`
- `DELETE /wp-json/my-store/v3/products/products/123`
- `GET /wp-json/my-store/v3/products/products/123/variants`

### Multiple API Controllers

```php
/**
 * Base API configuration
 * @api-namespace my-plugin
 * @api-version 1
 */
abstract class BaseApiController {
    use ApiDecoratorHooks;
    
    protected function requireAuth() {
        return is_user_logged_in();
    }
}

/**
 * User endpoints
 * @api-subspace users
 */
class UserApiController extends BaseApiController {
    // Routes: /wp-json/my-plugin/v1/users/*
}

/**
 * Settings endpoints  
 * @api-subspace settings
 */
class SettingsApiController extends BaseApiController {
    // Routes: /wp-json/my-plugin/v1/settings/*
}
```

## Route Definition Format

Routes should follow WordPress REST API conventions:

```php
[
    'route-pattern' => [
        'methods' => 'GET|POST|PUT|DELETE', // or array of methods
        'callback' => [$this, 'methodName'],
        'permission_callback' => 'callback_function',
        'args' => [
            'param_name' => [
                'validate_callback' => 'validation_function',
                'sanitize_callback' => 'sanitization_function',
                'required' => true,
                'default' => 'default_value'
            ]
        ]
    ]
]
```

## Best Practices

1. **Namespace Uniquely**: Use unique namespaces to avoid conflicts
2. **Version APIs**: Always specify versions for backward compatibility
3. **Secure Endpoints**: Implement proper permission callbacks
4. **Validate Input**: Use validation callbacks for parameters
5. **Follow REST Standards**: Use appropriate HTTP methods and status codes
6. **Document APIs**: Document your endpoints for other developers
7. **Error Handling**: Return proper WP_Error objects for failures

## Error Handling

```php
public function getUser($request) {
    $user_id = $request['id'];
    
    if (!is_numeric($user_id)) {
        return new WP_Error(
            'invalid_user_id',
            'User ID must be numeric',
            ['status' => 400]
        );
    }
    
    $user = get_user_by('id', $user_id);
    
    if (!$user) {
        return new WP_Error(
            'user_not_found',
            'User not found',
            ['status' => 404]
        );
    }
    
    return rest_ensure_response(['user' => $user]);
}
```

## Testing API Endpoints

```bash
# Test with curl
curl -X GET "http://your-site.com/wp-json/my-plugin/v1/users"
curl -X POST "http://your-site.com/wp-json/my-plugin/v1/users" \
     -H "Content-Type: application/json" \
     -d '{"name":"John Doe"}'
```

## Dependencies

- WordPress REST API (available since WordPress 4.7)
- `register_rest_route()` function
- Standard WordPress permission functions

## Automatic Registration

When using with `Singleton`, API registration happens automatically:

```php
class MyApiPlugin extends Singleton {
    use ApiDecoratorHooks;
    
    /**
     * @action init
     */
    public function init() {
        $this->addApiHooks();
    }
}
```
# API Endpoints

Create REST API endpoints for your plugin without the usual complexity. Perfect for AJAX requests, mobile apps, or integrations with other services.

## What it does

- Automatically registers WordPress REST API endpoints
- Handles API versioning and namespacing
- Manages permissions and authentication
- Organizes endpoints by functionality

## How to set it up

```php
/**
 * @api-namespace my-plugin
 * @api-version 1
 */
class UserAPI {
    use ApiDecoratorHooks;
    
    public function __construct() {
        self::setApiRoutes([
            '/users' => [
                'methods' => 'GET',
                'callback' => [$this, 'getUsers']
            ]
        ]);
    }
}
```

This creates: `/wp-json/my-plugin/v1/users`

## Common use cases

- AJAX requests from your plugin's frontend
- Mobile app integration
- Third-party service webhooks
- Admin dashboard data feeds

## Benefits

- Clean, organized API structure
- Automatic WordPress integration
- Built-in security and validation
- Easy to test and debug

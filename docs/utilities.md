# Utilities

Helper functions that make common WordPress development tasks easier. These are the little things that save you time and prevent bugs.

## String Helpers

Convert between different naming conventions:

```php
Strings::toSnakeCase('myMethodName'); // returns 'my_method_name'
```

This is useful for:
- Creating database table names
- Generating hook names
- AJAX action names
- WordPress option names

## Why these matter

WordPress has specific naming conventions, and these utilities help you follow them consistently. They also prevent common mistakes like typos in hook names or inconsistent naming across your plugin.

## Common patterns

- Convert class names to database table names
- Generate consistent hook names from method names
- Create URL-friendly slugs
- Standardize option and meta key names

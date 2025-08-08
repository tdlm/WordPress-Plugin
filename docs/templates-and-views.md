# Templates and Views

This handles displaying content to users. Think of it as a clean way to separate your HTML templates from your PHP logic.

## What it does

- Loads HTML/PHP template files
- Passes data to templates safely
- Renders content as strings or displays directly
- Keeps your display code organized

## How it works

Create a template file:
```php
<!-- templates/user-profile.php -->
<div class="user-info">
    <h2><?php echo esc_html($name); ?></h2>
    <p><?php echo esc_html($email); ?></p>
</div>
```

Use it in your code:
```php
$html = View::template('templates/user-profile', [
    'name' => 'John Doe',
    'email' => 'john@example.com'
]);
```

## Why this matters

- Keeps HTML separate from PHP logic
- Makes templates reusable
- Easier to maintain and update designs
- Safer data handling with built-in escaping

# EtchWP Development Guide

Comprehensive guide for developing, customizing, and maintaining EtchWP-based WordPress sites.

## Table of Contents

1. [Local Development Environment](#local-development-environment)
2. [Creating New Client Sites](#creating-new-client-sites)
3. [Code Organization](#code-organization)
4. [CSS & Styling Guidelines](#css--styling-guidelines)
5. [JavaScript Patterns](#javascript-patterns)
6. [PHP Coding Standards](#php-coding-standards)
7. [Template Customization](#template-customization)
8. [Hook Usage Examples](#hook-usage-examples)
9. [Block Editor Development](#block-editor-development)

## Local Development Environment

### Prerequisites

- **WordPress**: 6.9 or higher
- **PHP**: 8.0 or higher
- **Node.js**: Latest LTS (if using build tools)
- **Git**: For version control
- **Composer**: Optional, for dependency management

### Setup with wp-env

```bash
# Install wp-env globally
npm install -g @wordpress/env

# Navigate to your project directory
cd /path/to/wordpress-site

# Start WordPress environment
wp-env start

# Install Edge Theme
wp-env run cli wp theme install etch-theme --activate

# Install Edge Plugin
wp-env run cli wp plugin install edge-plugin --activate

# Copy child theme to themes directory
cp -r etchwp-child-theme wp-content/themes/

# Activate child theme
wp-env run cli wp theme activate etchwp-child-theme
```

### Setup with Docker Compose

```yaml
# docker-compose.yml
version: '3.8'

services:
  wordpress:
    image: wordpress:latest
    ports:
      - "8080:80"
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
      WORDPRESS_DB_NAME: wordpress
    volumes:
      - ./wp-content:/var/www/html/wp-content
      - ./etchwp-child-theme:/var/www/html/wp-content/themes/etchwp-child-theme

  db:
    image: mysql:8.0
    environment:
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress
      MYSQL_ROOT_PASSWORD: rootpassword
```

```bash
docker-compose up -d
```

### Setup with Local by Flywheel / MAMP / XAMPP

1. Create new WordPress site
2. Install Edge Theme via WordPress admin or WP-CLI
3. Install Edge Plugin via WordPress admin or WP-CLI
4. Copy `etchwp-child-theme` to `wp-content/themes/`
5. Activate child theme in WordPress admin

### WP-CLI Quick Setup

```bash
# Download WordPress
wp core download

# Configure database
wp config create --dbname=wordpress --dbuser=root --dbpass=password

# Install WordPress
wp core install --url=localhost --title="EtchWP Site" --admin_user=admin --admin_password=admin --admin_email=admin@example.com

# Install themes and plugins
wp theme install etch-theme --activate
wp plugin install edge-plugin --activate

# Activate child theme
wp theme activate etchwp-child-theme
```

## Creating New Client Sites

### Workflow

1. **Clone Boilerplate**
   ```bash
   git clone https://github.com/akaienso/etchwp-child-theme.git client-name-theme
   cd client-name-theme
   ```

2. **Update Theme Information**
   
   Edit `style.css` header:
   ```css
   /*
   Theme Name: Client Name Theme
   Theme URI: https://clientwebsite.com
   Author: Your Name
   Author URI: https://yourwebsite.com
   Description: Custom child theme for Client Name
   Template: etch-theme
   Version: 1.0.0
   */
   ```

3. **Initialize Git Repository**
   ```bash
   git remote remove origin  # Remove upstream remote
   git remote add origin [your-repository-url]
   git branch -M main
   ```

4. **Set Up Project Structure**
   ```bash
   mkdir -p css js templates/parts
   ```

5. **Begin Customization**
   - Add custom functions to `functions.php`
   - Create custom templates as needed
   - Add custom styles and scripts

### Project Structure Template

```
client-name-theme/
├── functions.php
├── style.css
├── theme.json
├── index.php
├── templates/
│   ├── index.html
│   ├── home.html
│   ├── 404.html
│   └── parts/
│       └── header.html
├── css/
│   ├── admin.css
│   └── custom.css
├── js/
│   ├── admin.js
│   └── custom.js
├── inc/
│   ├── post-types.php
│   ├── taxonomies.php
│   └── customizer.php
└── README.md
```

## Code Organization

### File Structure Best Practices

#### Functions.php Organization

Organize `functions.php` into logical sections:

```php
<?php
/**
 * Client Name Theme Functions
 *
 * @package client-name-theme
 * @since 1.0.0
 */

declare(strict_types=1);

// ============================================
// THEME SETUP
// ============================================

/**
 * Theme setup
 */
function clientname_theme_setup() {
    // Theme support declarations
    add_theme_support('post-thumbnails');
    add_theme_support('title-tag');
}
add_action('after_setup_theme', 'clientname_theme_setup');

// ============================================
// ASSET ENQUEUEING
// ============================================

/**
 * Enqueue styles and scripts
 */
function clientname_enqueue_assets() {
    // Styles
    wp_enqueue_style(
        'clientname-style',
        get_stylesheet_directory_uri() . '/css/custom.css',
        array(),
        wp_get_theme()->get('Version')
    );
    
    // Scripts
    wp_enqueue_script(
        'clientname-script',
        get_stylesheet_directory_uri() . '/js/custom.js',
        array(),
        wp_get_theme()->get('Version'),
        true
    );
}
add_action('wp_enqueue_scripts', 'clientname_enqueue_assets');

// ============================================
// CUSTOM POST TYPES
// ============================================

// Include separate file for post types
require_once get_stylesheet_directory() . '/inc/post-types.php';

// ============================================
// CUSTOM FUNCTIONS
// ============================================

/**
 * Example custom function
 */
function clientname_custom_function() {
    // Your code here
}
```

#### Separate Include Files

For complex functionality, use separate files:

```php
// functions.php
require_once get_stylesheet_directory() . '/inc/post-types.php';
require_once get_stylesheet_directory() . '/inc/taxonomies.php';
require_once get_stylesheet_directory() . '/inc/customizer.php';
require_once get_stylesheet_directory() . '/inc/admin.php';
```

### Naming Conventions

- **Functions**: `clientname_function_name()` (lowercase, underscores)
- **Classes**: `ClientName_Class_Name` (PascalCase, underscores)
- **Constants**: `CLIENTNAME_CONSTANT_NAME` (uppercase, underscores)
- **Files**: `custom-functions.php` (lowercase, hyphens)
- **CSS Classes**: `.clientname-block__element--modifier` (BEM)

## CSS & Styling Guidelines

### Organization

#### File Structure

```
css/
├── admin.css          # Admin area styles
├── custom.css         # Frontend custom styles
└── editor.css        # Block editor styles (optional)
```

#### Enqueue Styles

```php
function clientname_enqueue_styles() {
    // Frontend styles
    wp_enqueue_style(
        'clientname-custom',
        get_stylesheet_directory_uri() . '/css/custom.css',
        array('etch-theme-style'), // Parent theme as dependency
        wp_get_theme()->get('Version')
    );
    
    // Admin styles
    if (is_admin()) {
        wp_enqueue_style(
            'clientname-admin',
            get_stylesheet_directory_uri() . '/css/admin.css',
            array(),
            wp_get_theme()->get('Version')
        );
    }
    
    // Block editor styles
    add_editor_style('css/editor.css');
}
add_action('wp_enqueue_scripts', 'clientname_enqueue_styles');
```

### BEM Methodology

Use BEM (Block Element Modifier) naming:

```css
/* Block */
.clientname-header { }

/* Element */
.clientname-header__navigation { }
.clientname-header__logo { }

/* Modifier */
.clientname-header--sticky { }
.clientname-header__navigation--mobile { }
```

### CSS Custom Properties

Use CSS variables for theme values:

```css
:root {
    --clientname-primary-color: #0073aa;
    --clientname-spacing-unit: 1rem;
    --clientname-container-width: 1200px;
}

.clientname-button {
    background-color: var(--clientname-primary-color);
    padding: var(--clientname-spacing-unit);
}
```

### WordPress-Specific Styles

#### Block Editor Styles

Add block editor styles via `theme.json` or separate stylesheet:

```json
{
  "styles": {
    "blocks": {
      "core/paragraph": {
        "typography": {
          "fontSize": "1.125rem"
        }
      }
    }
  }
}
```

#### Admin Styles

Target admin areas specifically:

```css
/* wp-admin only */
.wp-admin .clientname-admin-panel {
    /* styles */
}
```

## JavaScript Patterns

### File Organization

```
js/
├── admin.js       # Admin area scripts
└── custom.js      # Frontend scripts
```

### Enqueue Scripts

```php
function clientname_enqueue_scripts() {
    wp_enqueue_script(
        'clientname-custom',
        get_stylesheet_directory_uri() . '/js/custom.js',
        array('jquery'), // Dependencies
        wp_get_theme()->get('Version'),
        true // In footer
    );
    
    // Localize script for PHP data
    wp_localize_script(
        'clientname-custom',
        'clientnameData',
        array(
            'ajaxUrl' => admin_url('admin-ajax.php'),
            'nonce' => wp_create_nonce('clientname_nonce'),
        )
    );
}
add_action('wp_enqueue_scripts', 'clientname_enqueue_scripts');
```

### Modern JavaScript

Use ES2022+ syntax:

```javascript
// custom.js
(function() {
    'use strict';
    
    // Use const/let, not var
    const init = () => {
        // Your code
    };
    
    // DOM ready
    if (document.readyState === 'loading') {
        document.addEventListener('DOMContentLoaded', init);
    } else {
        init();
    }
})();
```

### WordPress AJAX

```javascript
// Frontend AJAX
fetch(clientnameData.ajaxUrl, {
    method: 'POST',
    headers: {
        'Content-Type': 'application/x-www-form-urlencoded',
    },
    body: new URLSearchParams({
        action: 'clientname_ajax_action',
        nonce: clientnameData.nonce,
        data: 'value'
    })
})
.then(response => response.json())
.then(data => {
    console.log(data);
});
```

```php
// functions.php
function clientname_ajax_handler() {
    check_ajax_referer('clientname_nonce', 'nonce');
    
    // Process request
    wp_send_json_success(array('message' => 'Success'));
}
add_action('wp_ajax_clientname_ajax_action', 'clientname_ajax_handler');
add_action('wp_ajax_nopriv_clientname_ajax_action', 'clientname_ajax_handler');
```

## PHP Coding Standards

### WordPress Coding Standards

Follow [WordPress PHP Coding Standards](https://developer.wordpress.org/coding-standards/wordpress-coding-standards/php/):

- Use spaces, not tabs (4 spaces for indentation)
- Use single quotes for strings when possible
- Use strict comparison (`===`, `!==`)
- Sanitize input, escape output
- Use WordPress functions instead of PHP equivalents

### Function Structure

```php
<?php
/**
 * Function description
 *
 * @param string $param1 Parameter description.
 * @param int    $param2 Another parameter.
 * @return bool True on success, false on failure.
 */
function clientname_example_function(string $param1, int $param2): bool {
    // Validate input
    if (empty($param1)) {
        return false;
    }
    
    // Process
    $result = do_something($param1, $param2);
    
    // Return
    return $result;
}
```

### Security Best Practices

```php
// Sanitize input
$user_input = sanitize_text_field($_POST['input']);

// Escape output
echo esc_html($variable);
echo esc_url($url);
echo esc_attr($attribute);

// Verify nonces
if (!isset($_POST['nonce']) || !wp_verify_nonce($_POST['nonce'], 'action_name')) {
    wp_die('Security check failed');
}

// Check capabilities
if (!current_user_can('manage_options')) {
    return;
}
```

## Template Customization

### Overriding Parent Templates

1. **Copy template from parent**:
   ```bash
   cp ../etch-theme/templates/index.html templates/index.html
   ```

2. **Modify as needed**:
   ```html
   <!-- wp:etch/element {"metadata":{"name":"Custom Container"}} -->
   <!-- Your custom blocks here -->
   <!-- /wp:etch/element -->
   ```

3. **WordPress automatically uses child theme template**

### Creating Custom Templates

Create new templates in `templates/`:

```html
<!-- wp:template-part {"slug":"header","tagName":"header"} /-->

<!-- wp:etch/element {"metadata":{"name":"Main Content"}} -->
<!-- wp:post-content /-->
<!-- /wp:etch/element -->

<!-- wp:template-part {"slug":"footer","tagName":"footer"} /-->
```

### Template Parts

Create reusable template parts in `templates/parts/`:

```html
<!-- templates/parts/custom-header.html -->
<!-- wp:etch/element {"metadata":{"name":"Custom Header"}} -->
<!-- Header content -->
<!-- /wp:etch/element -->
```

Reference in templates:
```html
<!-- wp:template-part {"slug":"custom-header","tagName":"header"} /-->
```

## Hook Usage Examples

### Actions

```php
// After theme setup
add_action('after_setup_theme', 'clientname_theme_setup');

// Enqueue assets
add_action('wp_enqueue_scripts', 'clientname_enqueue_assets');

// Admin initialization
add_action('admin_init', 'clientname_admin_init');

// Widgets
add_action('widgets_init', 'clientname_register_widgets');
```

### Filters

```php
// Modify excerpt length
function clientname_excerpt_length(int $length): int {
    return 20;
}
add_filter('excerpt_length', 'clientname_excerpt_length');

// Modify excerpt more text
function clientname_excerpt_more(string $more): string {
    return '...';
}
add_filter('excerpt_more', 'clientname_excerpt_more');

// Modify body classes
function clientname_body_classes(array $classes): array {
    if (is_front_page()) {
        $classes[] = 'homepage';
    }
    return $classes;
}
add_filter('body_class', 'clientname_body_classes');
```

### Custom Hooks

Create custom hooks for extensibility:

```php
// Define action
do_action('clientname_before_header');

// Use in template or other functions
function clientname_render_header() {
    do_action('clientname_before_header');
    // Header code
    do_action('clientname_after_header');
}
```

## Block Editor Development

### Registering Block Patterns

```php
function clientname_register_patterns() {
    register_block_pattern(
        'clientname/hero-pattern',
        array(
            'title' => __('Hero Section', 'clientname'),
            'description' => __('A hero section with image and text', 'clientname'),
            'content' => '<!-- wp:columns -->
<!-- wp:column -->
<!-- wp:image -->
<!-- /wp:image -->
<!-- /wp:column -->
<!-- wp:column -->
<!-- wp:heading -->
<!-- /wp:heading -->
<!-- /wp:column -->
<!-- /wp:columns -->',
            'categories' => array('featured'),
        )
    );
}
add_action('init', 'clientname_register_patterns');
```

### Custom Block Styles

```php
function clientname_register_block_styles() {
    register_block_style(
        'core/button',
        array(
            'name' => 'outline',
            'label' => __('Outline', 'clientname'),
        )
    );
}
add_action('init', 'clientname_register_block_styles');
```

### Extending theme.json

```json
{
  "$schema": "https://schemas.wp.org/wp/6.9/theme.json",
  "version": 3,
  "settings": {
    "color": {
      "palette": [
        {
          "slug": "primary",
          "color": "#0073aa",
          "name": "Primary"
        }
      ]
    },
    "typography": {
      "fontSizes": [
        {
          "slug": "small",
          "size": "0.875rem",
          "name": "Small"
        }
      ]
    }
  }
}
```

## Testing

### PHP Syntax Check

```bash
php -l functions.php
```

### WordPress Coding Standards

```bash
# Install PHPCS
composer require --dev wp-coding-standards/wpcs

# Check code
./vendor/bin/phpcs functions.php --standard=WordPress
```

### Manual Testing Checklist

- [ ] Theme activates without errors
- [ ] All templates render correctly
- [ ] Custom functions work as expected
- [ ] No PHP errors in debug log
- [ ] No JavaScript console errors
- [ ] Styles apply correctly
- [ ] Block editor functions properly
- [ ] Edge Plugin compatibility verified

## Resources

- **WordPress Codex**: https://codex.wordpress.org/
- **WordPress Developer Handbook**: https://developer.wordpress.org/
- **EtchWP Documentation**: https://docs.etchwp.com
- **WordPress Coding Standards**: https://developer.wordpress.org/coding-standards/

---

**Remember**: Always test your code, follow WordPress standards, and document your customizations.

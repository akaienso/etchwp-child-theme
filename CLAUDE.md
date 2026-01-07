# EtchWP Development Context

## Project Overview

The EtchWP ecosystem consists of:

1. **Edge Theme** (`etch-theme/`) - The parent FSE (Full Site Editing) block theme
   - Minimal, block-based architecture
   - Relies on Edge Plugin for core functionality
   - Updated via WordPress standard theme update mechanism
   - **DO NOT MODIFY** - This is reference only

2. **EtchWP Child Theme** (`etchwp-child-theme/`) - Customizable child theme boilerplate
   - Used as a starting point for new client installations
   - All customizations go here
   - Upstream source: https://github.com/Digital-Gravy/etch-child-theme
   - Official docs: https://docs.etchwp.com

## Architecture

### Theme Structure

**Parent Theme (`etch-theme/`):**
- Minimal FSE block theme
- Uses custom `etch/element` blocks in templates
- `theme.json` defines layout and spacing settings
- HTML templates in `templates/` directory
- No traditional PHP templates (minimal `index.php` for WordPress requirement)

**Child Theme (`etchwp-child-theme/`):**
- Currently a boilerplate with minimal customizations
- `functions.php` is empty and ready for custom code
- `theme.json` inherits from parent (minimal override)
- Can override parent templates by copying to child theme

### Block Editor Integration

- Full Site Editing (FSE) enabled
- Custom blocks provided by Edge Plugin (`etch/element`, `etch/text`)
- Template editing via Site Editor
- Block patterns and styles managed through Edge Plugin

## Key Files

### Parent Theme (`etch-theme/`)

- `style.css` - Theme header (version 0.0.3, requires WP 6.1+, PHP 8.0+)
- `theme.json` - FSE configuration (content width: 745px, wide: 1340px)
- `templates/index.html` - Default template using `etch/element` blocks
- `templates/home.html` - Homepage template
- `templates/404.html` - 404 error template

### Child Theme (`etchwp-child-theme/`)

- `style.css` - Child theme header (Template: etch-theme)
- `functions.php` - **Primary customization file** (currently empty)
- `theme.json` - Inherits from parent (can override settings)
- `templates/` - Override parent templates here if needed

## Development Guidelines

### File Modification Rules

- **NEVER** modify files in `etch-theme/` (parent theme)
- **ALL** customizations go in `etchwp-child-theme/`
- Use `functions.php` for PHP customizations
- Override templates by copying from parent to child `templates/` directory
- Extend `theme.json` in child theme to override parent settings

### Coding Standards

- **PHP**: PSR-12, 4-space indent, strict types
- **WordPress**: Follow WordPress Coding Standards
- **Hooks**: Document all hook usage with inline comments
- **Functions**: Prefix custom functions to avoid conflicts (e.g., `etchwp_`)

### Template Customization

1. **Override Templates**: Copy parent template from `etch-theme/templates/` to `etchwp-child-theme/templates/`
2. **Block Patterns**: Register custom patterns in `functions.php`
3. **Block Styles**: Add custom block styles via `theme.json` or `functions.php`
4. **Template Parts**: Create custom template parts in `templates/parts/`

## Common Tasks

### Adding Custom Functions

```php
// etchwp-child-theme/functions.php
<?php
/**
 * Custom functions for EtchWP Child Theme
 */

// Example: Enqueue custom styles
function etchwp_enqueue_styles() {
    wp_enqueue_style(
        'etchwp-child-style',
        get_stylesheet_directory_uri() . '/style.css',
        array(),
        wp_get_theme()->get('Version')
    );
}
add_action('wp_enqueue_scripts', 'etchwp_enqueue_styles');
```

### Overriding Parent Template

1. Copy template from `etch-theme/templates/` to `etchwp-child-theme/templates/`
2. Modify as needed
3. WordPress will automatically use child theme template

### Extending theme.json

```json
{
  "$schema": "https://schemas.wp.org/wp/6.9/theme.json",
  "version": 3,
  "settings": {
    "color": {
      "custom": true,
      "customPalette": true
    }
  }
}
```

### Working with Edge Plugin Blocks

- Custom blocks are provided by Edge Plugin
- Blocks: `etch/element`, `etch/text`
- Refer to https://docs.etchwp.com for block documentation
- Customize block output via filters if available in Edge Plugin

## Testing Requirements

Before deployment:

1. **WordPress Compatibility**: Test on required WP version (6.9+)
2. **PHP Compatibility**: Verify PHP 8.0+ compatibility
3. **Edge Plugin**: Ensure Edge Plugin is installed and activated
4. **Block Editor**: Test all customizations in Site Editor
5. **Template Overrides**: Verify child theme templates render correctly
6. **Functionality**: Test all custom functions and hooks
7. **Performance**: Check page load times and Core Web Vitals

## Upstream Sync Process

The child theme boilerplate is maintained upstream at:
- **Repository**: https://github.com/Digital-Gravy/etch-child-theme
- **Sync Method**: Git merge (see `UPSTREAM-SYNC.md` for details)

### Key Points

- Upstream provides boilerplate updates
- Local customizations must be preserved during merges
- Common conflict areas: `functions.php`, `theme.json`, `style.css`
- Always test after merging upstream changes

## Dependencies

- **WordPress**: 6.9+ (child theme), 6.1+ (parent theme)
- **PHP**: 8.0+
- **Edge Plugin**: Required (provides core functionality)
- **Edge Theme**: Required (parent theme)

## Resources

- Official Documentation: https://docs.etchwp.com
- Upstream Repository: https://github.com/Digital-Gravy/etch-child-theme
- Parent Theme: Updated via WordPress theme updates
- Edge Plugin: Updated via WordPress plugin updates

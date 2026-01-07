# EtchWP Child Theme

A customizable child theme boilerplate for the EtchWP (Edge Theme) ecosystem. This theme serves as a starting point for new client installations, providing a clean foundation for customizations while maintaining compatibility with parent theme updates.

## Requirements

- **WordPress**: 6.9 or higher
- **Edge Theme**: Latest version (installed as parent theme)
- **Edge Plugin**: Latest version (provides core functionality)
- **PHP**: 8.0 or higher

## Installation

### Via WordPress Admin

1. Download or clone this repository to your WordPress themes directory:
   ```bash
   cd wp-content/themes/
   git clone https://github.com/akaienso/etchwp-child-theme.git etchwp-child-theme
   ```

2. Ensure the parent **Edge Theme** (`etch-theme`) is installed and activated.

3. Install and activate the **Edge Plugin** (required for core functionality).

4. In WordPress Admin, go to **Appearance > Themes** and activate **EtchWP Child Theme**.

### Via WP-CLI

```bash
# Install parent theme first (if not already installed)
wp theme install etch-theme --activate

# Install Edge Plugin (if not already installed)
wp plugin install edge-plugin --activate

# Activate child theme
wp theme activate etchwp-child-theme
```

## Usage

This child theme is designed as a **boilerplate** for new client installations:

1. **Clone or copy** this theme for each new client project
2. **Rename** the theme directory and update `style.css` header with client-specific information
3. **Customize** `functions.php` with client-specific functionality
4. **Override templates** as needed by copying from parent theme to child theme
5. **Extend** `theme.json` to customize block editor settings

### Quick Start

1. Add your customizations to `functions.php`:
   ```php
   <?php
   /**
    * Custom functions for [Client Name]
    */
   
   // Add your custom code here
   ```

2. Override parent templates by copying them to `templates/` directory:
   ```bash
   cp ../etch-theme/templates/index.html templates/
   # Then edit templates/index.html
   ```

3. Customize styles in `style.css` or create additional stylesheet files.

## Customization

### Key Customization Points

#### 1. Functions (`functions.php`)

The primary file for PHP customizations. Add:
- Custom post types and taxonomies
- Theme support declarations
- Enqueue scripts and styles
- WordPress hooks and filters
- Custom functions

Example:
```php
function etchwp_enqueue_assets() {
    wp_enqueue_style(
        'etchwp-custom-style',
        get_stylesheet_directory_uri() . '/css/custom.css',
        array(),
        wp_get_theme()->get('Version')
    );
}
add_action('wp_enqueue_scripts', 'etchwp_enqueue_assets');
```

#### 2. Templates (`templates/`)

Override parent theme templates by copying them here:
- `index.html` - Default template
- `home.html` - Homepage template
- `404.html` - 404 error template
- `parts/` - Template parts (create as needed)

#### 3. Theme Configuration (`theme.json`)

Extend parent theme settings:
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

#### 4. Styles (`style.css`)

Add custom CSS or import additional stylesheets via `functions.php`.

## Structure

```
etchwp-child-theme/
├── functions.php          # Primary customization file
├── style.css             # Theme stylesheet and header
├── theme.json            # Block editor configuration
├── index.php             # Required WordPress file
├── templates/            # Template overrides (create as needed)
│   ├── index.html
│   ├── home.html
│   └── 404.html
├── parts/                # Template parts (create as needed)
├── css/                  # Additional stylesheets (create as needed)
├── js/                   # JavaScript files (create as needed)
└── README.md             # This file
```

## Updates

### Parent Theme & Plugin Updates

**Edge Theme** and **Edge Plugin** receive updates through WordPress's standard update mechanism:

1. **Automatic Updates**: Configure in WordPress Admin under **Dashboard > Updates**
2. **Manual Updates**: Go to **Appearance > Themes** or **Plugins** and click "Update Now"
3. **WP-CLI**: 
   ```bash
   wp theme update etch-theme
   wp plugin update edge-plugin
   ```

**Important**: Parent theme updates are safe and will not affect your child theme customizations.

### Child Theme Updates

This child theme boilerplate is maintained upstream. To sync updates:

1. **Add upstream remote** (if not already added):
   ```bash
   git remote add upstream https://github.com/Digital-Gravy/etch-child-theme.git
   ```

2. **Fetch and merge upstream changes**:
   ```bash
   git fetch upstream
   git merge upstream/main
   ```

3. **Resolve conflicts** if any (preserve your local customizations)

4. **Test thoroughly** after merging

See `UPSTREAM-SYNC.md` in the repository root for detailed merge instructions.

## Block Editor Integration

This theme supports Full Site Editing (FSE) and works seamlessly with the WordPress Block Editor:

- **Site Editor**: Customize templates via **Appearance > Editor**
- **Custom Blocks**: Provided by Edge Plugin (`etch/element`, `etch/text`)
- **Block Patterns**: Register custom patterns in `functions.php`
- **Block Styles**: Add via `theme.json` or `functions.php`

## Documentation

For complete EtchWP documentation, visit:
- **Official Documentation**: https://docs.etchwp.com
- **Edge Theme**: Updated via WordPress theme updates
- **Edge Plugin**: Updated via WordPress plugin updates

## Development

### Local Development Setup

1. Install WordPress locally (wp-env, Local, MAMP, etc.)
2. Install Edge Theme and Edge Plugin
3. Clone this repository to themes directory
4. Activate child theme
5. Start customizing!

### Coding Standards

- **PHP**: PSR-12, WordPress Coding Standards
- **CSS**: WordPress CSS Coding Standards, BEM naming
- **JavaScript**: ES2022+, WordPress JavaScript Standards
- See `.cursorrules` in repository root for detailed guidelines

## Contributing

This is a customized version of the EtchWP child theme boilerplate. The upstream source is maintained by Digital Gravy at https://github.com/Digital-Gravy/etch-child-theme.

For contributions to this repository:

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

For bug reports or feature requests related to this customized version, please open an issue on this repository. For issues with the upstream boilerplate, please contact Digital Gravy.

## License

This theme, like WordPress, is licensed under the GPL.

```
Copyright (C) 2025 Digital Gravy

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the
GNU General Public License for more details.
```

Full license text: [LICENSE](LICENSE)

## Changelog

### 1.0.0
- Initial release
- Child theme boilerplate for EtchWP/Edge Theme
- Basic structure and documentation

---

**Repository**: [akaienso/etchwp-child-theme](https://github.com/akaienso/etchwp-child-theme)  
**Upstream Source**: [Digital-Gravy/etch-child-theme](https://github.com/Digital-Gravy/etch-child-theme)  
**Parent Theme**: [Edge Theme](https://etchwp.com/)  
**Documentation**: [docs.etchwp.com](https://docs.etchwp.com)

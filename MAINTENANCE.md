# EtchWP Maintenance Guide

Comprehensive guide for maintaining and supporting EtchWP-based WordPress sites.

## Table of Contents

1. [Update Procedures](#update-procedures)
2. [Upstream Child Theme Sync](#upstream-child-theme-sync)
3. [Backup and Rollback](#backup-and-rollback)
4. [Troubleshooting](#troubleshooting)
5. [Performance Optimization](#performance-optimization)
6. [Security Considerations](#security-considerations)

## Update Procedures

### Edge Theme Updates

Edge Theme receives updates through WordPress's standard theme update mechanism.

#### Automatic Updates

Configure automatic updates in WordPress:

1. Go to **Dashboard > Updates**
2. Enable automatic updates for Edge Theme
3. Or configure via `wp-config.php`:
   ```php
   define('WP_AUTO_UPDATE_CORE', true);
   add_filter('auto_update_theme', '__return_true');
   ```

#### Manual Updates

**Via WordPress Admin:**
1. Go to **Dashboard > Updates**
2. Click "Update Now" next to Edge Theme
3. Wait for update to complete
4. Test site functionality

**Via WP-CLI:**
```bash
wp theme update etch-theme
wp theme list  # Verify version
```

#### Post-Update Checklist

After updating Edge Theme:

- [ ] **Activate theme** - Ensure it's still active
- [ ] **Check Site Editor** - Verify templates load correctly
- [ ] **Test frontend** - Check homepage and key pages
- [ ] **Verify child theme** - Ensure child theme still works
- [ ] **Check error logs** - Look for PHP errors
- [ ] **Test Edge Plugin** - Verify plugin compatibility
- [ ] **Check customizations** - Ensure nothing broke

### Edge Plugin Updates

Edge Plugin provides core functionality and must be kept up to date.

#### Update Process

**Via WordPress Admin:**
1. Go to **Plugins > Installed Plugins**
2. Click "Update Now" for Edge Plugin
3. Or go to **Dashboard > Updates**

**Via WP-CLI:**
```bash
wp plugin update edge-plugin
wp plugin list  # Verify version
```

#### Post-Update Checklist

- [ ] **Activate plugin** - Ensure it's still active
- [ ] **Test custom blocks** - Verify `etch/element` and other blocks work
- [ ] **Check Site Editor** - Ensure block editor functions
- [ ] **Test frontend** - Verify blocks render correctly
- [ ] **Check compatibility** - Ensure theme compatibility
- [ ] **Review changelog** - Check for breaking changes

### WordPress Core Updates

WordPress core updates are separate but important:

```bash
# Check for updates
wp core check-update

# Update core
wp core update

# Update database if needed
wp core update-db
```

**Important**: Always backup before WordPress core updates.

## Upstream Child Theme Sync

The child theme boilerplate is maintained upstream. See `UPSTREAM-SYNC.md` for detailed merge instructions.

### Quick Sync Process

```bash
cd etchwp-child-theme

# Fetch upstream
git fetch upstream

# Create sync branch
git checkout -b sync/upstream-$(date +%Y%m%d)

# Merge
git merge upstream/main

# Resolve conflicts if any
# Test thoroughly
# Merge to main branch
```

### When to Sync

- **Regularly**: Monthly or quarterly to stay current
- **After upstream releases**: When upstream publishes new versions
- **Before major projects**: Ensure you have latest boilerplate
- **After security fixes**: If upstream releases security patches

### Conflict Prevention

To minimize conflicts:

1. **Keep customizations organized** - Use clear sections in `functions.php`
2. **Document changes** - Note what you've customized
3. **Use separate files** - Put complex code in `/inc/` directory
4. **Version control** - Commit regularly with clear messages

## Backup and Rollback

### Backup Strategy

#### Full Site Backup

**Before any updates or major changes:**

```bash
# Backup database
wp db export backup-$(date +%Y%m%d).sql

# Backup files
tar -czf backup-$(date +%Y%m%d).tar.gz wp-content/themes/etchwp-child-theme

# Or use backup plugin
wp plugin install updraftplus --activate
```

#### Git-Based Backup

If using Git (recommended):

```bash
# Commit current state
git add .
git commit -m "Backup before [update/change description]"

# Create backup branch
git branch backup/$(date +%Y%m%d)

# Push to remote
git push origin backup/$(date +%Y%m%d)
```

### Rollback Procedures

#### Rollback Theme Update

**Via WordPress Admin:**
1. Go to **Appearance > Themes**
2. If previous version available, reinstall
3. Or restore from backup

**Via WP-CLI:**
```bash
# List available versions
wp theme list --update=available

# Install specific version (if available)
wp theme install etch-theme --version=0.0.2 --force
```

**Via Git:**
```bash
# Revert to previous commit
git revert HEAD

# Or checkout previous version
git checkout HEAD~1 functions.php
```

#### Rollback Plugin Update

```bash
# List versions
wp plugin list --update=available

# Install previous version
wp plugin install edge-plugin --version=1.0.0 --force
```

#### Database Rollback

```bash
# Restore database backup
wp db import backup-20250101.sql

# Or via phpMyAdmin
# Import SQL file through phpMyAdmin interface
```

#### File Rollback

```bash
# Restore from backup
tar -xzf backup-20250101.tar.gz

# Or restore from Git
git checkout HEAD~1 -- functions.php
```

### Emergency Recovery

If site is broken:

1. **Access via FTP/SFTP** - Bypass WordPress if needed
2. **Rename broken theme** - `etchwp-child-theme-broken`
3. **Restore backup** - Copy backup files back
4. **Check error logs** - Identify the issue
5. **Fix incrementally** - Restore one component at a time

## Troubleshooting

### Common Issues

#### Theme Not Activating

**Symptoms**: Theme won't activate, shows error

**Solutions**:
```bash
# Check PHP version
php -v  # Should be 8.0+

# Check theme structure
wp theme list

# Verify parent theme installed
wp theme list | grep etch-theme

# Check for syntax errors
php -l functions.php
```

#### Templates Not Rendering

**Symptoms**: Blank pages, wrong templates showing

**Solutions**:
1. **Clear cache** (if using caching plugin)
2. **Regenerate permalinks**: Settings > Permalinks > Save
3. **Check template hierarchy**: Verify template files exist
4. **Check Site Editor**: Ensure templates are assigned
5. **Review error logs**: Check for PHP errors

#### Edge Plugin Blocks Not Working

**Symptoms**: Custom blocks (`etch/element`) not appearing or not working

**Solutions**:
```bash
# Verify plugin is active
wp plugin list | grep edge-plugin

# Check plugin version compatibility
wp plugin list

# Reinstall plugin
wp plugin deactivate edge-plugin
wp plugin uninstall edge-plugin
wp plugin install edge-plugin --activate
```

#### Styling Issues

**Symptoms**: Styles not applying, layout broken

**Solutions**:
1. **Clear browser cache** - Hard refresh (Ctrl+F5)
2. **Check CSS file path** - Verify enqueue path is correct
3. **Check CSS specificity** - Parent theme styles may override
4. **Verify file exists** - Check file is in correct location
5. **Check enqueue order** - Child theme styles should load after parent

#### JavaScript Errors

**Symptoms**: Console errors, functionality broken

**Solutions**:
1. **Check browser console** - Identify specific errors
2. **Verify script dependencies** - Check jQuery, etc. are loaded
3. **Check script path** - Verify file exists and path is correct
4. **Check for conflicts** - Other plugins/themes may conflict
5. **Verify script is enqueued** - Check `wp_enqueue_script()` call

### Debugging

#### Enable WordPress Debug

Add to `wp-config.php`:

```php
define('WP_DEBUG', true);
define('WP_DEBUG_LOG', true);
define('WP_DEBUG_DISPLAY', false);
define('SCRIPT_DEBUG', true);
```

Check logs:
```bash
# View debug log
tail -f wp-content/debug.log

# Or via WP-CLI
wp eval 'error_log("Test message");'
```

#### Check PHP Errors

```bash
# Check PHP error log
tail -f /var/log/php-errors.log

# Or check WordPress debug log
tail -f wp-content/debug.log
```

#### Database Issues

```bash
# Check database connection
wp db check

# Repair database
wp db repair

# Optimize database
wp db optimize
```

### Getting Help

1. **Check Documentation**: https://docs.etchwp.com
2. **Review Error Logs**: Identify specific error messages
3. **Test in Staging**: Reproduce issue in safe environment
4. **Check Upstream Issues**: https://github.com/Digital-Gravy/etch-child-theme/issues
5. **WordPress Support**: https://wordpress.org/support/

## Performance Optimization

### Caching

#### Object Caching

```php
// wp-config.php
define('WP_CACHE', true);
```

Use caching plugins:
- **WP Super Cache**
- **W3 Total Cache**
- **WP Rocket** (premium)

#### Browser Caching

Configure via `.htaccess` or server config:

```apache
<IfModule mod_expires.c>
    ExpiresActive On
    ExpiresByType text/css "access plus 1 year"
    ExpiresByType application/javascript "access plus 1 year"
    ExpiresByType image/png "access plus 1 year"
    ExpiresByType image/jpg "access plus 1 year"
</IfModule>
```

### Asset Optimization

#### Minify CSS/JS

Use build tools or plugins:
- **Autoptimize**
- **WP Rocket**
- **Webpack** (development)

#### Lazy Loading

```php
// Enable lazy loading for images
add_filter('wp_lazy_loading_enabled', '__return_true');
```

### Database Optimization

```bash
# Optimize database
wp db optimize

# Clean up revisions
wp post delete $(wp post list --post_type=revision --format=ids) --force

# Clean up spam comments
wp comment delete $(wp comment list --status=spam --format=ids) --force
```

### Image Optimization

- Use **WebP** format where possible
- Compress images before upload
- Use responsive images
- Consider CDN for images

### Performance Checklist

- [ ] **Caching enabled** - Object and page caching
- [ ] **Assets optimized** - Minified CSS/JS
- [ ] **Images optimized** - Compressed, WebP where possible
- [ ] **Database optimized** - Regular cleanup
- [ ] **CDN configured** - For static assets
- [ ] **Lazy loading** - For images and iframes
- [ ] **Gzip compression** - Enabled on server
- [ ] **Query optimization** - Efficient database queries

## Security Considerations

### WordPress Security

#### Keep Everything Updated

```bash
# Check for updates
wp core check-update
wp plugin list --update=available
wp theme list --update=available

# Update all
wp core update
wp plugin update --all
wp theme update --all
```

#### Strong Passwords

- Use strong, unique passwords
- Enable two-factor authentication
- Limit login attempts

#### File Permissions

```bash
# Correct file permissions
find . -type d -exec chmod 755 {} \;
find . -type f -exec chmod 644 {} \;

# wp-config.php should be 600
chmod 600 wp-config.php
```

### Theme Security

#### Sanitize Input

```php
// Always sanitize user input
$user_input = sanitize_text_field($_POST['input']);
$email = sanitize_email($_POST['email']);
$url = esc_url_raw($_POST['url']);
```

#### Escape Output

```php
// Always escape output
echo esc_html($variable);
echo esc_url($url);
echo esc_attr($attribute);
echo wp_kses_post($html);
```

#### Verify Nonces

```php
// Always verify nonces
if (!isset($_POST['nonce']) || !wp_verify_nonce($_POST['nonce'], 'action_name')) {
    wp_die('Security check failed');
}
```

#### Check Capabilities

```php
// Check user capabilities
if (!current_user_can('manage_options')) {
    return;
}
```

### Server Security

#### .htaccess Protection

```apache
# Protect wp-config.php
<Files wp-config.php>
    Order allow,deny
    Deny from all
</Files>

# Block directory browsing
Options -Indexes

# Protect .htaccess
<Files .htaccess>
    Order allow,deny
    Deny from all
</Files>
```

#### Security Headers

```php
// Add security headers
function clientname_security_headers() {
    header('X-Content-Type-Options: nosniff');
    header('X-Frame-Options: SAMEORIGIN');
    header('X-XSS-Protection: 1; mode=block');
}
add_action('send_headers', 'clientname_security_headers');
```

### Security Checklist

- [ ] **WordPress updated** - Latest version
- [ ] **Plugins updated** - All plugins current
- [ ] **Themes updated** - Parent and child themes current
- [ ] **Strong passwords** - For all users
- [ ] **Two-factor auth** - Enabled where possible
- [ ] **File permissions** - Correct (755/644)
- [ ] **Input sanitized** - All user input
- [ ] **Output escaped** - All output
- [ ] **Nonces verified** - All forms
- [ ] **Capabilities checked** - All admin functions
- [ ] **Error messages** - Don't reveal sensitive info
- [ ] **Backups** - Regular, tested backups
- [ ] **Security plugin** - Consider Wordfence, Sucuri, etc.

### Monitoring

#### Security Plugins

Consider using:
- **Wordfence Security**
- **Sucuri Security**
- **iThemes Security**

#### Log Monitoring

```bash
# Monitor error logs
tail -f wp-content/debug.log

# Monitor access logs
tail -f /var/log/apache2/access.log
```

## Maintenance Schedule

### Daily

- [ ] Check error logs
- [ ] Monitor site uptime
- [ ] Review security alerts

### Weekly

- [ ] Check for updates
- [ ] Review performance metrics
- [ ] Backup verification

### Monthly

- [ ] Update WordPress, themes, plugins
- [ ] Database optimization
- [ ] Security audit
- [ ] Performance review
- [ ] Backup test restore

### Quarterly

- [ ] Full security audit
- [ ] Performance optimization review
- [ ] Code review
- [ ] Documentation update
- [ ] Upstream sync (child theme)

## Resources

- **WordPress Security**: https://wordpress.org/support/article/hardening-wordpress/
- **EtchWP Documentation**: https://docs.etchwp.com
- **WordPress Codex**: https://codex.wordpress.org/
- **WP-CLI Handbook**: https://wp-cli.org/

---

**Remember**: Regular maintenance prevents problems. Keep everything updated, backed up, and monitored.

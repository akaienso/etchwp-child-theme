# Upstream Sync Guide

This guide explains how to sync updates from the upstream EtchWP child theme repository while preserving your local customizations.

## Overview

The EtchWP child theme boilerplate is maintained upstream at:
- **Repository**: https://github.com/Digital-Gravy/etch-child-theme
- **Purpose**: Receive boilerplate updates, bug fixes, and improvements
- **Strategy**: Merge upstream changes while preserving local customizations

## Prerequisites

- Git repository initialized in your child theme directory
- Local customizations committed to your repository
- Backup of your current theme (recommended)

## Initial Setup

### 1. Add Upstream Remote

If you haven't already added the upstream remote:

```bash
cd etchwp-child-theme
git remote add upstream https://github.com/Digital-Gravy/etch-child-theme.git
```

Verify the remote was added:
```bash
git remote -v
```

You should see:
```
origin    [your-repository-url] (fetch)
origin    [your-repository-url] (push)
upstream  https://github.com/Digital-Gravy/etch-child-theme.git (fetch)
upstream  https://github.com/Digital-Gravy/etch-child-theme.git (push)
```

## Sync Workflow

### Step 1: Ensure Clean Working Directory

Before syncing, ensure you have a clean working directory:

```bash
git status
```

If you have uncommitted changes:
```bash
# Option 1: Commit your changes
git add .
git commit -m "feat: local customizations before upstream sync"

# Option 2: Stash your changes
git stash push -m "Local changes before upstream sync"
```

### Step 2: Fetch Upstream Changes

Fetch the latest changes from upstream:

```bash
git fetch upstream
```

Check what branch upstream uses (usually `main` or `master`):
```bash
git branch -r | grep upstream
```

### Step 3: Create Feature Branch for Merge

Create a branch for the merge to keep your main branch clean:

```bash
git checkout -b sync/upstream-$(date +%Y%m%d)
# Or use a more descriptive name:
git checkout -b sync/upstream-v1.0.1
```

### Step 4: Merge Upstream Changes

Merge upstream changes into your branch:

```bash
# If upstream uses 'main' branch:
git merge upstream/main

# If upstream uses 'master' branch:
git merge upstream/master
```

### Step 5: Resolve Conflicts

If conflicts occur, Git will pause and show you which files have conflicts:

```
Auto-merging functions.php
CONFLICT (content): Merge conflict in functions.php
Auto-merging theme.json
CONFLICT (content): Merge conflict in theme.json
```

#### Common Conflict Files

These files commonly have conflicts:

1. **`functions.php`** - Most likely to conflict
   - **Strategy**: Keep your local customizations, add upstream additions
   - Look for `// Add your own customizations here` marker

2. **`theme.json`** - May conflict if upstream changes settings
   - **Strategy**: Merge settings, preserve your custom values

3. **`style.css`** - May conflict if header changes
   - **Strategy**: Keep your theme header, merge any upstream changes

4. **`README.md`** - Documentation conflicts
   - **Strategy**: Usually safe to accept upstream version, then re-add your custom sections

#### Resolving Conflicts

**Option 1: Manual Resolution (Recommended)**

1. Open conflicted files in your editor
2. Look for conflict markers:
   ```
   <<<<<<< HEAD
   Your local changes
   =======
   Upstream changes
   >>>>>>> upstream/main
   ```
3. Edit to combine both changes appropriately
4. Remove conflict markers
5. Stage resolved files:
   ```bash
   git add functions.php theme.json
   ```

**Option 2: Using Merge Tool**

```bash
# Configure merge tool (if not already configured)
git config merge.tool vimdiff
# Or use your preferred tool: vscode, meld, etc.

# Launch merge tool
git mergetool
```

**Option 3: Accept One Side**

If you want to keep your version entirely:
```bash
git checkout --ours functions.php
git add functions.php
```

If you want to accept upstream version entirely:
```bash
git checkout --theirs functions.php
git add functions.php
```

**⚠️ Warning**: Accepting one side entirely may lose important changes. Review carefully.

### Step 6: Complete the Merge

After resolving all conflicts:

```bash
git commit -m "chore: merge upstream changes from Digital-Gravy/etch-child-theme

- Synced with upstream v[version]
- Preserved local customizations in functions.php
- Resolved conflicts in [list files]"
```

### Step 7: Test Merged Changes

**Critical**: Test thoroughly before merging to your main branch:

1. **Activate theme** in WordPress
2. **Check Site Editor** - Verify templates render correctly
3. **Test custom functions** - Ensure all custom code still works
4. **Check frontend** - Verify site displays correctly
5. **Test Edge Plugin compatibility** - Ensure blocks work properly
6. **Check console** - Look for JavaScript errors
7. **Check PHP errors** - Review error logs

### Step 8: Merge to Main Branch

If testing passes, merge to your main branch:

```bash
git checkout main  # or master
git merge sync/upstream-YYYYMMDD
```

If you use a different workflow (e.g., pull requests), create a PR from your sync branch.

### Step 9: Push Changes

Push your merged changes:

```bash
git push origin main
```

## Pre-Merge Checklist

Before starting the sync process:

- [ ] **Backup created** - Full backup of theme directory
- [ ] **Changes committed** - All local work is committed
- [ ] **Documentation reviewed** - Know what you've customized
- [ ] **Upstream changelog checked** - Review what changed upstream
- [ ] **Testing environment ready** - Local WordPress setup working

## Conflict Resolution Strategies

### For `functions.php`

**Best Practice**: Keep your customizations, add upstream additions:

```php
<?php
/**
 * Custom functions for EtchWP Child Theme
 */

// ============================================
// UPSTREAM CODE (from Digital-Gravy)
// ============================================
// [Upstream functions here]

// ============================================
// LOCAL CUSTOMIZATIONS
// ============================================
// [Your custom functions here]
```

### For `theme.json`

Merge settings carefully:

```json
{
  "$schema": "https://schemas.wp.org/wp/6.9/theme.json",
  "version": 3,
  "settings": {
    // Upstream settings
    "layout": {
      "contentSize": "745px"
    },
    // Your custom settings
    "color": {
      "custom": true
    }
  }
}
```

### For Template Files

If upstream updates templates you've customized:

1. **Compare** your version with upstream version
2. **Identify** what upstream changed
3. **Apply** upstream improvements to your customized version
4. **Test** thoroughly

## Post-Merge Testing Procedures

After merging, perform these tests:

### 1. Basic Functionality
```bash
# Check for PHP syntax errors
php -l functions.php

# Check WordPress theme structure
wp theme list
```

### 2. WordPress Admin
- [ ] Theme activates without errors
- [ ] Site Editor loads correctly
- [ ] Customizer works (if used)
- [ ] No PHP warnings in debug log

### 3. Frontend
- [ ] Homepage loads correctly
- [ ] All templates render properly
- [ ] Custom functions execute
- [ ] No JavaScript console errors
- [ ] CSS styles apply correctly

### 4. Edge Plugin
- [ ] Custom blocks (`etch/element`, etc.) work
- [ ] Block editor functions properly
- [ ] No plugin conflicts

### 5. Performance
- [ ] Page load times acceptable
- [ ] No new database queries
- [ ] Asset loading works correctly

## Troubleshooting

### Merge Conflicts Won't Resolve

If you're stuck on conflicts:

1. **Abort the merge**:
   ```bash
   git merge --abort
   ```

2. **Review upstream changes**:
   ```bash
   git log upstream/main --oneline
   git diff main upstream/main
   ```

3. **Try a different strategy**:
   ```bash
   # Rebase instead of merge (advanced)
   git rebase upstream/main
   ```

### Lost Customizations

If you accidentally lost customizations:

1. **Check reflog**:
   ```bash
   git reflog
   ```

2. **Recover from backup** (if you have one)

3. **Extract from previous commit**:
   ```bash
   git show HEAD~1:functions.php > functions.php.backup
   ```

### Upstream Changes Break Functionality

If upstream changes cause issues:

1. **Identify the problematic change**:
   ```bash
   git log upstream/main --oneline
   git show [commit-hash]
   ```

2. **Revert specific changes**:
   ```bash
   git revert [commit-hash]
   ```

3. **Document the issue** and consider reporting to upstream

## Documenting Merges

After each sync, document what was merged:

```markdown
## Sync Log

### [Date] - Upstream v1.0.1
- **Merged**: Upstream changes from Digital-Gravy/etch-child-theme
- **Conflicts**: functions.php, theme.json
- **Resolution**: Preserved local customizations, merged upstream improvements
- **Testing**: All tests passed
- **Notes**: Upstream added new helper functions, integrated successfully
```

## Advanced: Automated Sync Script

Create a script to automate the sync process:

```bash
#!/bin/bash
# sync-upstream.sh

set -e

echo "Fetching upstream changes..."
git fetch upstream

echo "Creating sync branch..."
BRANCH="sync/upstream-$(date +%Y%m%d)"
git checkout -b "$BRANCH"

echo "Merging upstream..."
git merge upstream/main || {
    echo "Conflicts detected. Please resolve manually."
    exit 1
}

echo "Merge complete. Please test before merging to main."
echo "Branch: $BRANCH"
```

Make it executable:
```bash
chmod +x sync-upstream.sh
```

## Best Practices

1. **Sync Regularly**: Don't let too many changes accumulate upstream
2. **Test Thoroughly**: Always test after merging
3. **Document Changes**: Keep notes on what you've customized
4. **Backup First**: Always backup before syncing
5. **Review Upstream**: Check changelog/releases before syncing
6. **Preserve Customizations**: Never blindly accept upstream over local changes
7. **Communicate**: If you find issues, report to upstream maintainers

## Getting Help

- **Upstream Issues**: https://github.com/Digital-Gravy/etch-child-theme/issues
- **Documentation**: https://docs.etchwp.com
- **Git Help**: `git help merge` or `git help rebase`

---

**Remember**: The goal is to get upstream improvements while preserving your customizations. When in doubt, test thoroughly and preserve your local work.

# Build Validation System

## Overview

Automated system to ensure registry.json accuracy and prevent installation 404 errors.

## Components

### 1. Registry Validator (`scripts/validate-registry.sh`)

Validates that all paths in registry.json point to actual files.

**Usage:**
```bash
# Basic validation
./scripts/validate-registry.sh

# Verbose output with orphaned file detection
./scripts/validate-registry.sh -v

# Get fix suggestions for missing files
./scripts/validate-registry.sh --fix
```

**Exit Codes:**
- `0` = All paths valid
- `1` = Missing files found
- `2` = Registry parse error

### 2. Auto-Detect Components (`scripts/auto-detect-components.sh`)

Scans `.opencode/` directory for new components not in registry.

**Usage:**
```bash
# Dry run - see what would be added
./scripts/auto-detect-components.sh --dry-run

# Auto-add new components
./scripts/auto-detect-components.sh --auto-add
```

**Features:**
- Extracts metadata from file frontmatter
- Generates IDs and names automatically
- Skips node_modules, tests, docs, templates
- Adds to appropriate registry category

### 3. GitHub Actions Workflow

**On Pull Request:**
1. Auto-detects new components
2. Adds them to registry.json
3. Validates all registry paths
4. Commits updates to PR branch
5. Blocks merge if validation fails

**Workflow File:** `.github/workflows/validate-registry.yml`

## Workflow

```
Developer adds new file (.opencode/command/my-cmd.md)
         ↓
    Creates PR to dev
         ↓
GitHub Action runs automatically
         ↓
Auto-detects new file
         ↓
Adds to registry.json
         ↓
Validates all paths
         ↓
✓ Pass → PR can merge
✗ Fail → PR blocked
```

## Fixed Issues

### Original Problem
```
curl: (22) The requested URL returned error: 404
✗ Failed to install command: prompt-enhancer
```

**Root Cause:** Registry had wrong path
- Registry: `.opencode/command/prompt-enchancer.md` (typo)
- Actual: `.opencode/command/prompt-engineering/prompt-enhancer.md`

**Solution:** 
- Fixed registry path
- Added validation to prevent future issues
- Auto-detection ensures new files are registered

## For Contributors

### Adding New Components

1. Create your component file in `.opencode/`
2. Add frontmatter with description:
   ```markdown
   ---
   description: "Your component description"
   ---
   ```
3. Create PR - automation handles the rest!

### Manual Registry Updates

If you need to manually update registry.json:

1. Make your changes
2. Run validator: `./scripts/validate-registry.sh -v`
3. Fix any errors
4. Commit changes

### Testing Locally

```bash
# Validate registry
./scripts/validate-registry.sh -v

# Check for new components
./scripts/auto-detect-components.sh --dry-run

# Add new components
./scripts/auto-detect-components.sh --auto-add

# Validate again
./scripts/validate-registry.sh
```

## Benefits

✅ **Prevents 404 errors** - All paths validated before merge
✅ **Auto-registration** - New components added automatically  
✅ **Zero manual work** - Developers just add files
✅ **CI/CD integration** - Runs on every PR
✅ **Clear feedback** - Detailed error messages and suggestions

## Future Enhancements

- [ ] Validate component metadata completeness
- [ ] Check for duplicate IDs
- [ ] Validate dependencies exist
- [ ] Auto-categorize components (essential/standard/specialized)
- [ ] Generate changelog from new components

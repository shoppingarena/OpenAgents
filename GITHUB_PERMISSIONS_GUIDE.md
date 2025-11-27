# GitHub Permissions Guide - Build Validation Workflows

## ⚠️ Required Permissions

For the build validation workflows to work correctly, you need to configure:

1. **Workflow Permissions** (in workflow files) ✅ Already configured
2. **Repository Settings** (in GitHub) ⚠️ Needs configuration
3. **Branch Protection Rules** (optional but recommended) ⚠️ Needs review

---

## 1. Workflow Permissions ✅

**Status:** Already configured in workflow files

### PR Validation Workflow
```yaml
# .github/workflows/validate-registry.yml
permissions:
  contents: write        # Needed to commit registry updates
  pull-requests: write   # Needed to update PR
```

### Direct Push Workflow
```yaml
# .github/workflows/update-registry.yml
permissions:
  contents: write        # Needed to commit registry updates
```

✅ **These are already set in the workflow files**

---

## 2. Repository Settings ⚠️

**Status:** Needs configuration in GitHub

### Required Settings

Go to: **Settings → Actions → General → Workflow permissions**

**Option 1: Recommended (More Secure)**
```
○ Read repository contents and packages permissions
● Read and write permissions  ← SELECT THIS
```

**Option 2: Alternative (If Option 1 doesn't work)**
```
☑ Allow GitHub Actions to create and approve pull requests
```

### How to Configure

1. Go to your repository on GitHub
2. Click **Settings** (top right)
3. Click **Actions** → **General** (left sidebar)
4. Scroll to **Workflow permissions**
5. Select **"Read and write permissions"**
6. Click **Save**

**Screenshot location:**
```
https://github.com/darrenhinde/OpenAgents/settings/actions
```

---

## 3. Branch Protection Rules ⚠️

**Status:** Needs review

### Current Issue

If you have branch protection on `main` or `dev`, the GitHub Actions bot might be blocked from pushing commits.

### Check Your Settings

Go to: **Settings → Branches → Branch protection rules**

### Recommended Configuration

For `main` branch:
```
☑ Require a pull request before merging
  ☑ Require approvals (1)
  ☐ Dismiss stale pull request approvals when new commits are pushed
  ☑ Require review from Code Owners (optional)

☑ Require status checks to pass before merging
  ☑ Require branches to be up to date before merging
  Status checks:
    ☑ validate-and-update (from validate-registry.yml)

☐ Require conversation resolution before merging (optional)

☑ Allow force pushes
  ☑ Specify who can force push
    ☑ github-actions[bot]  ← IMPORTANT!

☐ Allow deletions
```

For `dev` branch:
```
☑ Require status checks to pass before merging
  Status checks:
    ☑ validate-and-update

☑ Allow force pushes
  ☑ Specify who can force push
    ☑ github-actions[bot]  ← IMPORTANT!
```

### Why Allow Force Push for Bot?

The bot needs to push commits to PR branches. Without this, auto-commits will fail.

**Alternative:** Don't enable branch protection on PR branches, only on `main`/`dev`.

---

## 4. Fork Permissions ⚠️

**Issue:** PRs from forks won't have write permissions

### The Problem

When someone forks your repo and creates a PR:
- ❌ Workflow can't commit to their fork
- ❌ Auto-add won't work
- ✅ Validation still works (read-only)

### Solutions

**Option A: Require contributors to enable workflows**
```markdown
## Contributing

1. Fork the repository
2. Enable GitHub Actions in your fork
3. Create feature branch
4. Add your component
5. Create PR

Note: Auto-detection will run but won't auto-commit to your fork.
You may need to manually run:
./scripts/auto-detect-components.sh --auto-add
```

**Option B: Use pull_request_target (Security Risk!)**
```yaml
# NOT RECOMMENDED - Security risk
on:
  pull_request_target:  # Runs with write permissions
```
⚠️ **Don't use this** - allows malicious code execution

**Option C: Manual review for forks (Recommended)**
```
1. Fork PR comes in
2. Validation runs (read-only)
3. If validation fails, maintainer:
   - Checks out PR branch
   - Runs auto-detect locally
   - Pushes fix to PR
```

---

## 5. Testing Permissions

### Test 1: Check Workflow Permissions

```bash
# Create a test PR
git checkout -b test/permissions
echo "test" > .opencode/command/test-permissions.md
git add .opencode/command/test-permissions.md
git commit -m "test: check workflow permissions"
git push origin test/permissions

# Create PR
gh pr create --base dev --title "Test: Workflow Permissions"

# Watch the workflow run
gh pr checks

# Expected result:
# ✅ Auto-detect runs
# ✅ Auto-add commits to PR branch
# ✅ Validation passes
```

### Test 2: Check Direct Push

```bash
# Push directly to main (if you have permission)
git checkout main
echo "test" > .opencode/command/test-direct.md
git add .opencode/command/test-direct.md
git commit -m "test: direct push workflow"
git push origin main

# Check Actions tab
# Expected result:
# ✅ Auto-detect runs
# ✅ Auto-add commits to main
# ✅ Validation runs
```

---

## 6. Troubleshooting

### Issue: "Resource not accessible by integration"

**Error:**
```
Error: Resource not accessible by integration
```

**Solution:**
1. Go to Settings → Actions → General
2. Enable "Read and write permissions"
3. Save and re-run workflow

---

### Issue: "Refusing to allow a GitHub App to create or update workflow"

**Error:**
```
refusing to allow a GitHub App to create or update workflow
```

**Solution:**
1. Go to Settings → Actions → General
2. Enable "Allow GitHub Actions to create and approve pull requests"
3. Save and re-run workflow

---

### Issue: Bot can't push to PR branch

**Error:**
```
! [remote rejected] feature-branch -> feature-branch (protected branch hook declined)
```

**Solution:**
1. Go to Settings → Branches
2. Edit branch protection rule
3. Under "Allow force pushes", add `github-actions[bot]`
4. Save

---

### Issue: Workflow doesn't run on PR from fork

**Expected Behavior:**
- Validation runs (read-only) ✅
- Auto-commit doesn't work ❌

**Solution:**
This is normal for security. Options:
1. Ask contributor to run auto-detect locally
2. Maintainer checks out PR and runs auto-detect
3. Accept that forks need manual registry updates

---

## 7. Security Considerations

### ✅ Safe Practices

- Use `pull_request` trigger (not `pull_request_target`)
- Limit permissions to minimum needed
- Review auto-commits before merging
- Use branch protection on main

### ❌ Avoid

- `pull_request_target` with write permissions
- Disabling all branch protection
- Auto-merging without review
- Running untrusted code in workflows

---

## 8. Quick Setup Checklist

**Before workflows will work:**

- [ ] Enable "Read and write permissions" in Actions settings
- [ ] Review branch protection rules
- [ ] Allow `github-actions[bot]` to push (if using branch protection)
- [ ] Test with a sample PR
- [ ] Document fork contribution process

**After setup:**

- [ ] Workflows run automatically on PR
- [ ] Bot can commit to PR branches
- [ ] Validation blocks invalid PRs
- [ ] Direct pushes trigger auto-update

---

## 9. Recommended Settings Summary

### Repository Settings
```
Settings → Actions → General → Workflow permissions
  ● Read and write permissions
  ☑ Allow GitHub Actions to create and approve pull requests
```

### Branch Protection (main)
```
Settings → Branches → main
  ☑ Require pull request before merging
  ☑ Require status checks to pass
    ☑ validate-and-update
  ☑ Allow force pushes
    ☑ github-actions[bot]
```

### Branch Protection (dev)
```
Settings → Branches → dev
  ☑ Require status checks to pass
    ☑ validate-and-update
  ☑ Allow force pushes
    ☑ github-actions[bot]
```

---

## 10. Links

- **Actions Settings:** `https://github.com/darrenhinde/OpenAgents/settings/actions`
- **Branch Protection:** `https://github.com/darrenhinde/OpenAgents/settings/branches`
- **Workflow Runs:** `https://github.com/darrenhinde/OpenAgents/actions`

---

## Status

**Current:** ⚠️ Permissions need to be configured in GitHub UI

**After Setup:** ✅ Workflows will work automatically

**Next Step:** Configure repository settings as described above

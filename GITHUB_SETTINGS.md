# Quick Setup - GitHub Settings

## ⚡ 3 Steps to Enable Workflows

### Step 1: Enable Workflow Permissions (Required)

**Go to:** https://github.com/darrenhinde/OpenAgents/settings/actions

**Set:**
```
Workflow permissions:
  ● Read and write permissions  ← SELECT THIS
  ☑ Allow GitHub Actions to create and approve pull requests
```

**Click:** Save

---

### Step 2: Configure Branch Protection (Optional but Recommended)

**Go to:** https://github.com/darrenhinde/OpenAgents/settings/branches

**For `main` branch:**
```
☑ Require pull request before merging
☑ Require status checks to pass before merging
  ☑ validate-and-update
☑ Allow force pushes
  ☑ github-actions[bot]  ← IMPORTANT!
```

**For `dev` branch:**
```
☑ Require status checks to pass before merging
  ☑ validate-and-update
☑ Allow force pushes
  ☑ github-actions[bot]  ← IMPORTANT!
```

---

### Step 3: Test It Works

**Create a test PR:**
```bash
git checkout -b test/workflow
echo "test" > .opencode/command/test.md
git add .opencode/command/test.md
git commit -m "test: workflow permissions"
git push origin test/workflow
gh pr create --base dev --title "Test: Workflow"
```

**Expected result:**
- ✅ Workflow runs
- ✅ Auto-detects test.md
- ✅ Commits to PR branch
- ✅ Validation passes

---

## 🔍 Troubleshooting

### Error: "Resource not accessible by integration"

**Fix:** Enable "Read and write permissions" in Step 1

### Error: "Protected branch hook declined"

**Fix:** Add `github-actions[bot]` to allowed force pushers in Step 2

### Workflow doesn't run

**Check:**
1. Actions are enabled: Settings → Actions → General
2. Workflow file exists: `.github/workflows/validate-registry.yml`
3. PR targets `main` or `dev` branch

---

## 📋 Quick Links

- **Actions Settings:** https://github.com/darrenhinde/OpenAgents/settings/actions
- **Branch Protection:** https://github.com/darrenhinde/OpenAgents/settings/branches
- **Workflow Runs:** https://github.com/darrenhinde/OpenAgents/actions
- **Full Guide:** See `GITHUB_PERMISSIONS_GUIDE.md`

---

## ✅ Checklist

- [ ] Step 1: Enable workflow permissions
- [ ] Step 2: Configure branch protection (optional)
- [ ] Step 3: Test with sample PR
- [ ] Workflows running successfully

**After setup, workflows will automatically:**
- Auto-detect new components
- Update registry.json
- Validate all paths
- Block invalid PRs

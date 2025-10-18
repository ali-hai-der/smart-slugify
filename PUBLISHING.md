# Publishing Guide for smart-slugify

This guide explains how to set up automatic publishing to PyPI using GitHub Actions.

## Overview

I've created two publishing workflows:

1. **`publish.yml`** - Publishes on every push to main (when src/ or pyproject.toml changes)
2. **`publish-on-tag.yml`** - Publishes only when you create version tags (RECOMMENDED)

**Important:** Both workflows run the full test suite (18 test combinations across 3 operating systems and 6 Python versions) before publishing. Publishing only happens if all tests pass.

## Setup Instructions

### Step 1: Create a PyPI Account

1. Go to [pypi.org](https://pypi.org/) and create an account
2. Verify your email address

### Step 2: Generate PyPI API Token

1. Log in to PyPI
2. Go to Account Settings → API tokens
3. Click **"Add API token"**
4. Name: `smart-slugify-github-actions`
5. Scope: **"Entire account"** (or specific to smart-slugify once published)
6. Click **"Add token"**
7. **IMPORTANT**: Copy the token that starts with `pypi-...` - you'll only see this once!

### Step 3: Add Token to GitHub Secrets

1. Go to your GitHub repository: `https://github.com/ali-hai-der/smart-slugify`
2. Click **Settings** → **Secrets and variables** → **Actions**
3. Click **"New repository secret"**
4. Name: `PYPI_API_TOKEN`
5. Value: Paste your PyPI token (the one starting with `pypi-...`)
6. Click **"Add secret"**

### Step 4: Choose Your Publishing Strategy

#### Option A: Publish on Every Push (Active by default)

The `publish.yml` workflow will automatically publish to PyPI when you push changes to:
- `src/**` directory
- `pyproject.toml`
- `setup.py` or `setup.cfg`

**Pros**: Automatic, no extra steps
**Cons**: Every change goes live immediately

#### Option B: Publish on Version Tags (RECOMMENDED)

The `publish-on-tag.yml` workflow publishes only when you create version tags.

**To publish a new version:**

```bash
# 1. Update version in pyproject.toml
# Edit: version = "0.1.1"

# 2. Commit the version change
git add pyproject.toml
git commit -m "Bump version to 0.1.1"

# 3. Create and push a version tag
git tag v0.1.1
git push origin main
git push origin v0.1.1
```

**Pros**: More control, follows semantic versioning
**Cons**: Requires manual tagging

### Step 5: First Publication

For the **first time** publishing to PyPI, you may need to do it manually because the package name needs to be claimed:

```bash
# Install build tools
pip install build twine

# Build the package
python -m build

# Upload to PyPI
twine upload dist/*
# Enter your PyPI username and API token when prompted
```

After the first manual upload, GitHub Actions will work automatically.

## Workflow Details

### What Happens in the Workflow

**Test Phase** (runs first):
1. ✅ Checks out your code
2. ✅ Sets up Python (3.7, 3.8, 3.9, 3.10, 3.11, 3.12)
3. ✅ Tests on Ubuntu, macOS, and Windows
4. ✅ Installs dependencies and package
5. ✅ Runs all tests with pytest

**Publish Phase** (only if ALL tests pass):
6. ✅ Checks out your code
7. ✅ Sets up Python 3.11
8. ✅ Installs build dependencies (`build`, `twine`)
9. ✅ Builds the package (creates `.whl` and `.tar.gz` files)
10. ✅ Checks package integrity with `twine check`
11. ✅ Uploads to PyPI using your API token

If any test fails, the workflow stops and publishing is skipped.

### Monitoring Deployments

- Check workflow runs: `https://github.com/ali-hai-der/smart-slugify/actions`
- View PyPI releases: `https://pypi.org/project/smart-slugify/`

## Versioning Best Practices

Follow [Semantic Versioning](https://semver.org/):

- **MAJOR** version (1.0.0): Incompatible API changes
- **MINOR** version (0.1.0): New functionality, backward compatible
- **PATCH** version (0.0.1): Bug fixes, backward compatible

Example progression:
```
0.1.0 → 0.1.1 → 0.1.2 → 0.2.0 → 0.2.1 → 1.0.0
```

## Troubleshooting

### Error: "Package already exists"
- Make sure you've incremented the version in `pyproject.toml`
- PyPI doesn't allow re-uploading the same version

### Error: "Invalid token"
- Double-check that `PYPI_API_TOKEN` secret is set correctly in GitHub
- Regenerate the token on PyPI if needed

### Error: "Package name already taken"
- The name `smart-slugify` might already be taken on PyPI
- Change `name = "smart-slugify"` in `pyproject.toml` to something unique
- Consider: `smart-slugify-py`, `haider-smart-slugify`, etc.

## Disabling Auto-Publish

If you want to disable auto-publishing:

### Disable push-based publishing:
```bash
git rm .github/workflows/publish.yml
git commit -m "Disable auto-publish on push"
git push
```

### Keep tag-based publishing only:
```bash
# Just keep publish-on-tag.yml and remove publish.yml
git rm .github/workflows/publish.yml
git commit -m "Use tag-based publishing only"
git push
```

## Manual Publishing

You can always publish manually:

```bash
# Build
python -m build

# Test upload to TestPyPI (optional)
twine upload --repository testpypi dist/*

# Upload to PyPI
twine upload dist/*
```

## Next Steps

1. ✅ Set up PyPI account
2. ✅ Generate API token  
3. ✅ Add token to GitHub secrets
4. ✅ Publish first version manually
5. ✅ Push to GitHub and watch it auto-deploy!

---

For questions or issues, refer to:
- [PyPI Help](https://pypi.org/help/)
- [GitHub Actions Docs](https://docs.github.com/en/actions)
- [Python Packaging Guide](https://packaging.python.org/)


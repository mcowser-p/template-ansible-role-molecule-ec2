# Contributing Guide

## Conventional Commits

This project uses [Conventional Commits](https://www.conventionalcommits.org/) for automated versioning and changelog generation.

### Commit Message Format

Each commit message should follow this structure:

```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

### Commit Types

The following commit types will trigger releases:

- **feat**: A new feature (triggers MINOR version bump)
  - Example: `feat: add support for Ubuntu 24.04`
  - Example: `feat(auth): implement OAuth2 authentication`

- **fix**: A bug fix (triggers PATCH version bump)
  - Example: `fix: resolve connection timeout issue`
  - Example: `fix(deps): update ansible-core to 2.16.1`

- **perf**: Performance improvements (triggers PATCH version bump)
  - Example: `perf: optimize task execution speed`

- **refactor**: Code refactoring (triggers PATCH version bump)
  - Example: `refactor: simplify variable handling logic`

- **docs**: Documentation changes (triggers PATCH version bump)
  - Example: `docs: update installation instructions`

### Breaking Changes

To trigger a MAJOR version bump, include `BREAKING CHANGE:` in the commit footer or add `!` after the type:

```
feat!: remove support for Ansible 2.9

BREAKING CHANGE: Minimum Ansible version is now 2.12
```

### Non-Release Commits

These commit types will NOT trigger a release:

- **chore**: Maintenance tasks
  - Example: `chore: update .gitignore`

- **style**: Code style changes (formatting, whitespace)
  - Example: `style: fix indentation in tasks`

- **test**: Adding or updating tests
  - Example: `test: add molecule test for new feature`

- **ci**: CI/CD configuration changes
  - Example: `ci: update GitHub Actions workflow`

- **build**: Build system changes
  - Example: `build: update dependencies`

### Preventing Releases

To prevent a release for a specific commit, use the `no-release` scope:

```
feat(no-release): experimental feature for testing
```

### Examples

**Feature Addition (Minor Release):**
```
feat: add support for custom SSL certificates

- Add ssl_cert_path variable
- Add ssl_key_path variable
- Update documentation
```

**Bug Fix (Patch Release):**
```
fix: correct file permissions on config files

Previously, config files were created with 0644 permissions.
Now they are created with 0600 for better security.

Closes #123
```

**Breaking Change (Major Release):**
```
feat!: restructure role variables

BREAKING CHANGE: Variable names have been updated to follow
Ansible best practices. Update your playbooks accordingly:
- `app_port` is now `application_port`
- `app_user` is now `application_user`
```

**No Release:**
```
docs: fix typo in README

This is a minor documentation fix that doesn't warrant a release.
```

## Release Process

Releases are automatically created when commits are pushed to the `main` branch:

1. Commits are analyzed based on conventional commit format
2. Version number is determined (major.minor.patch)
3. CHANGELOG.md is updated
4. Git tag is created (e.g., v1.2.3)
5. GitHub release is published
6. Changes are committed back to the repository

### Pre-releases

Commits to the `develop` branch will create pre-release versions (e.g., v1.2.3-develop.1).

## Best Practices

1. **Write clear, descriptive commit messages**
2. **Use the appropriate commit type**
3. **Include scope when relevant** (e.g., `feat(auth):`, `fix(api):`)
4. **Reference issues in commit body or footer** (e.g., `Closes #123`)
5. **Keep commits focused** - one logical change per commit
6. **Test before committing** - ensure all tests pass

## Questions?

If you have questions about contributing or the commit format, please open an issue!

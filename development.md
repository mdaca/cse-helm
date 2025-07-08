
# Helm Chart Contribution and Release Workflow

This repository uses a GitHub Actions workflow to automate Helm chart versioning, packaging, and release whenever you push changes to the `main` or `develop` branches.

---

## How to Make Changes

To ensure your changes trigger a proper chart release, follow these steps:

### 1. Commit Message Format

Your commit messages **must** include one of the following tags **at the end** to specify the type of version bump:

- `[major]` — For breaking changes or significant updates
- `[minor]` — For new features or enhancements (non-breaking)
- `[patch]` — For bug fixes or small changes

**Examples:**

- `Add keytab support to deployment and values [minor]`
- `Fix bug in ingress template [patch]`
- `Refactor chart structure [major]`

If no tag is included, the version will not be bumped and no new release will be created.

### 2. Branches

- Push your commits to `main` or `develop`.
- Pull requests to these branches should also follow the commit message format.

### 3. What the Workflow Does

When you push a commit with a valid tag, the GitHub Actions workflow will:

- Read the latest Git tag to determine current version.
- Bump the chart version in `chart/cse/Chart.yaml` according to the tag.
- Package the Helm chart into a `.tgz` archive under the `package/` directory.
- Update the Helm repository index `package/index.yaml`.
- Commit and push the updated `Chart.yaml`, `.tgz` file, and `index.yaml`.
- Create and push a new Git tag for the release.

---

## Notes

- The packaged `.tgz` files and index live in the `package/` directory.
- Ensure your changes are compatible with Helm v3 and follow semantic versioning.
- Use clear and descriptive commit messages including the version bump tag.

---

## Example Commit Message Template

```
<Short summary of the change> [major|minor|patch]

Optional extended description here.
```

---

If you have questions or need help, please reach out to the maintainers.

---

Thank you for contributing!

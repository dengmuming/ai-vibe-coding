---
description: Steps for creating a new release of the application.
---

# Release Workflow

When requested to create a new release or version, perform these structured steps:

1. **Review Latest Changes:**
   - Determine what has changed since the last release by reading `git log` or recent pull requests.
   - Identify whether this should be a major, minor, or patch release based on SemVer principles.
2. **Update Version Numbers:**
   - Modify the version strings in configuration files (e.g., `package.json`, `Cargo.toml`, `go.mod`, `pom.xml` depending on the ecosystem).
3. **Update Changelog:**
   - Compile a user-friendly list of features, bug fixes, and deprecations.
   - Prepend this list to the `CHANGELOG.md` file under the new version header and the current date.
4. **Commit the Release:**
   - `git commit -am "chore(release): prepare vX.Y.Z"`
5. **Tag the Release:**
   - `git tag vX.Y.Z`
   - `git push origin vX.Y.Z`
6. **Notify User:** Announce the successful creation of the new release tag using `notify_user`.

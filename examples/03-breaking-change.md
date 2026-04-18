# Example 3: Breaking change

## The diff

Renames a public function and changes its signature. This is a major version bump.

```diff
- export function getUser(id) {
-   return db.users.find(id);
- }
+ export function getUser({ id, includeDeleted = false }) {
+   return db.users.find({ id, deleted: includeDeleted });
+ }
```

## What `/ship-it` outputs

```
Review:
  ✗ BREAKING: getUser() signature changed from (id) to ({ id })
  ⚠ Callers found in 8 files — none updated in this diff
  ⚠ No migration note written

Commit message:
  feat(users)!: switch getUser to options object, add includeDeleted

  The new signature supports soft-deleted users without a second function.
  Existing callers passing a bare id will break. Migration: wrap the
  argument in an object — getUser(42) → getUser({ id: 42 }).

  BREAKING CHANGE: getUser(id) → getUser({ id, includeDeleted? })

Semver: major (public API signature changed)

CHANGELOG entry:
  ### Changed
  - **BREAKING**: `getUser()` now takes an options object: `{ id, includeDeleted? }`.

  ### Added
  - `getUser()` supports `includeDeleted` to fetch soft-deleted users.

Release notes:
  ## What's new
  `getUser()` now takes an options object and can include soft-deleted
  records. This is a breaking change.

  ## Breaking changes
  `getUser(id)` is now `getUser({ id })`.

  Migration:
  ```diff
  - getUser(42)
  + getUser({ id: 42 })
  ```

  To include soft-deleted users: `getUser({ id: 42, includeDeleted: true })`.

Ship it? [y/N]

⚠ I found 8 callers not updated in this diff. Fix those first? [y/N]
```

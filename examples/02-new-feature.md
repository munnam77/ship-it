# Example 2: New feature

## The diff

Adds a `--format=json` flag to the CLI, with a new JSON output path.

```diff
+ function formatJson(results) {
+   return JSON.stringify(results, null, 2);
+ }
  function formatText(results) { ... }

- const output = formatText(results);
+ const output = argv.format === 'json' ? formatJson(results) : formatText(results);
  console.log(output);
```

## What `/ship-it` outputs

```
Review:
  ✓ New flag added without breaking existing behavior (text remains default)
  ✓ JSON output uses 2-space indent — consistent with config conventions
  ⚠ No test coverage for the new code path — recommend adding one before merge

Commit message:
  feat(cli): add --format=json flag for machine-readable output

  Text output remains the default. JSON is useful for piping into jq or
  consuming from scripts. No existing behavior changes.

Semver: minor (new feature, backwards-compatible)

CHANGELOG entry:
  ### Added
  - `--format=json` flag for machine-readable CLI output

Release notes:
  ## What's new
  The CLI now supports JSON output with `--format=json`. Useful when
  you want to pipe results into jq or consume them from a script.
  Text remains the default, so nothing changes if you don't pass the flag.

  ## Changes
  - Added: `--format=json` flag

Ship it? [y/N]
```

# Example 1: Bug fix

## The diff

```diff
--- a/src/auth.js
+++ b/src/auth.js
@@ -12,7 +12,7 @@ function validateToken(token) {
-  if (token.length > 0) {
+  if (token && token.length > 0) {
     return jwt.verify(token, SECRET);
   }
   return null;
```

## What `/ship-it` outputs

```
Review:
  ✓ Null-safety fix in validateToken()
  ✓ No API change
  ⚠ Consider: jwt.verify throws on invalid tokens — wrap in try/catch (separate issue)

Commit message:
  fix(auth): guard against null token in validateToken

  Calling .length on null token was crashing the auth middleware when
  requests arrived without an Authorization header. Now returns null
  as designed.

Semver: patch (bug fix, no behavior change for valid inputs)

CHANGELOG entry:
  ### Fixed
  - Auth middleware no longer crashes when the Authorization header is missing

Release notes:
  ## What's new
  Fixes a crash in the auth middleware when requests arrive without an
  Authorization header. Previously this threw a TypeError; now it cleanly
  returns 401 as designed.

  ## Changes
  - Fixed: null token handling in validateToken()

Ship it? [y/N]
```

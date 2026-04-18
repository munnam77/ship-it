# ship-it

> The Claude Code skill that turns a messy branch into a clean release.

One command. Reviews your diff, writes the commit message, updates the changelog, picks the right semver bump, drafts the release notes, and asks if you want to ship it.

```
/ship-it
```

That's it. No flags. No config. It reads your code and your git history, figures out what you did, and hands you a polished release.

![demo](./docs/demo.gif)

---

## The problem

You just finished a feature. You're tired. You now have to:

1. Write a commit message that your team won't hate
2. Decide if it's `patch`, `minor`, or `major`
3. Update `CHANGELOG.md` without breaking the format
4. Write a GitHub release description that's actually useful
5. Remember which branch you based this on and whether you rebased

By step 3 you're writing "misc fixes" and calling it done. Your changelog is a lie. Your release notes say "bug fixes and improvements."

## What ship-it does

When you run `/ship-it`, the skill:

- **Reviews the diff** — flags likely bugs, security issues, style drift (not nitpicks)
- **Writes the commit message** — conventional commits, explains the *why* not the *what*
- **Picks the semver bump** — patch / minor / major, based on whether behavior changed, APIs changed, or things broke
- **Updates CHANGELOG.md** — respects [Keep a Changelog](https://keepachangelog.com/) format if present
- **Drafts release notes** — GitHub-flavored markdown, user-facing language, grouped by type
- **Shows you everything** — you approve or edit before anything lands
- **Executes on approval** — creates the commit, tags the release, pushes

It will not push without asking.

## Install

One line, if you have Claude Code:

```bash
claude plugin install munnam77/ship-it
```

Or manually: clone and copy `skills/ship-it` to `~/.claude/skills/`.

## Usage

```
/ship-it              # runs on your current branch vs main
/ship-it --draft      # generate everything, don't commit or push
/ship-it --no-push    # commit and tag locally, don't push
```

## Example

**Before** — your diff:

```diff
+ function parseUserInput(raw) {
+   if (!raw) return null;
+   return raw.trim().toLowerCase();
+ }
- function normalize(s) {
-   return s.toLowerCase();
- }
```

**After running `/ship-it`**:

```
Review:
  ✓ Null-safety added (raw check)
  ⚠  Callers of normalize() need to be updated — 3 call sites found
  ⚠  Behavior changed: now trims whitespace (was silent before)

Commit message:
  refactor(input): rename normalize → parseUserInput, handle null + trim

  Null input now returns null instead of throwing. Whitespace is trimmed
  before lowercasing. Callers updated in auth.js, api.js, form.js.

Semver: minor (behavior change, backwards-compatible with null guard)

CHANGELOG entry:
  ### Changed
  - `parseUserInput()` replaces `normalize()` — now handles null and trims whitespace

Release notes:
  ## What's new
  Input parsing is safer. `parseUserInput()` replaces the old `normalize()`
  and handles edge cases that used to crash (null, trailing whitespace).
  No action needed if you use the default exports.

Ship it? [y/N]
```

## Why I built this

I wrote 400+ commit messages last year. Most of them were garbage because I was tired. My changelogs were worse. My release notes were a copy-paste of the last one.

I wanted a skill that did the boring part well so I could spend that energy on the code. That's all this is.

## What it does NOT do

- It does not auto-push to production. It stops at `git push origin <branch>`.
- It does not write tests for you (see [`skill-lab`](https://github.com/munnam77/skill-lab) for that).
- It does not review more than the diff — it's not a substitute for human code review.

## Design principles

1. **One command.** If it needs flags, it's not doing enough.
2. **Never destructive.** Nothing gets pushed, tagged, or amended without explicit yes.
3. **Conventional commits by default.** Override if your repo uses something else (auto-detected).
4. **Changelogs only if one exists.** Doesn't create them for you — that's a project decision.

## Roadmap

- [ ] Auto-generate PR description from the same context
- [ ] Detect and respect repo-specific commit conventions (e.g. gitmoji, JIRA IDs)
- [ ] Post-merge cleanup mode (`/ship-it --cleanup`)

## Related

Part of a small set of single-purpose Claude Code tools:

- [ship-it](https://github.com/munnam77/ship-it) — this repo
- [claude-hud](https://github.com/munnam77/claude-hud) — know what Claude is costing you
- [skill-lab](https://github.com/munnam77/skill-lab) — test your Claude skills like code
- [the-skill-cookbook](https://github.com/munnam77/the-skill-cookbook) — how to write a 100x skill
- [claude-for-solopreneurs](https://github.com/munnam77/claude-for-solopreneurs) — 10 skills for running a software business solo

## License

MIT

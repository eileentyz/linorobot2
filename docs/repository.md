# Repository workflow

## Keep project work separate from dependencies

The outer repository is `eileentyz/linorobot2`. The clone at `src/linorobot2` has its own Git history and points to `linorobot/linorobot2`. Adding that nested clone with a normal `git add` does not reliably publish its contents as ordinary files.

This workspace uses a `dependencies.repos` manifest. The outer repo ignores `src/` and tracks documentation and project maps directly. A fresh clone restores upstream sources with `vcs import src < dependencies.repos`.

Existing nested files are left in place. Your original Moretea map remains in the nested clone; the copy in the outer `maps/` directory is the version to maintain and publish going forward.

## Add another cloned dependency

Record its relative path under `src`, Git URL, and exact commit in `dependencies.repos`, following the existing entry. Commit the manifest with a note explaining why the package is needed. Do not add build outputs.

## Customize robot code

Changes inside `src/linorobot2` are not included in outer-repository commits. To share those changes, commit and push them to your own upstream-code fork, then update the manifest URL and commit to that published revision. Check `git -C src/linorobot2 status` before changing dependency versions so local work is preserved.

For new project packages, place them in a tracked directory such as `packages/` at the workspace root; colcon can discover packages there too. Include that directory in rosdep's `--from-paths` arguments when it exists.

## Review and publish these workspace improvements

From the outer workspace root:

```bash
git status --short
git diff -- README.md .gitignore
git add README.md .gitignore dependencies.repos docs/ maps/
git diff --cached --stat
git diff --cached --check
git diff --cached
```

After reviewing the staged files:

```bash
git commit -m "Document Moretea workspace setup and simulation"
git push origin HEAD
```

The explicit file list keeps the publish scope clear. These commands publish the workspace documentation and maps; they do not publish changes inside the upstream clone.

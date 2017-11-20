---
layout: post
title: Sequences of Commits
description: "Sequences of commits"
tags: [taskcluster, git, commit, rebase]
---
Commit early and commit often. Microcommits with good commit messages are easy to review and as a consequence,
will receive better code reviews. When it's time to integrate a pull-request, sometimes squashing commits is recommended
done to streamline the git history.

GitHub has a nifty feature allowing users with write access to squash commits and edit the final squashed commit
message. To use that feature, Click the "Squash and merge" button.

If you don't have write access but want to group commits into one, the answer is to manually squash using the
`rebase` command offered by git.

Assuming you're working on a feature branch `my-feature` and `upstream` is a git remote pointing to the official
project repository:

```
(my-feature) > git fetch upstream
(my-feature) > git rebase -i upstream/master
```

Assuming `my-feature` introduced three new commits, `git rebase -i upstream/master` will open your default text
editor and show a view similar to:

```
pick a9c8a1d First commit in my-feature
pick 01b2fd8 Second commit in my-feature
pick b7a3dj8 Third commit in my-feature

# Rebase f4ea0c4..291b676 onto f4ea0c4 (2 commands)
#
# Commands:
#  p, pick = use commit
#  r, reword = use commit, but edit the commit message
#  e, edit = use commit, but stop for amending
#  s, squash = use commit, but meld into previous commit
#  f, fixup = like "squash", but discard this commit's log message
#  x, exec = run command (the rest of the line) using shell
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
# Note that empty commits are commented out
```

_Note: Lines starting with # are comments_

Replace the `pick` command with one of the commands shown under the commands section. To combine commits, replace
`pick` with `s` or `f`.

_Example: Leave the first commit untouched but combine the third  commit with the second_

```
pick a9c8a1d First commit in my-feature
pick 01b2fd8 Second commit in my-feature
f b7a3dj8 Third commit in my-feature
```

That's all. Save the file to make history great again. I typically use `git log` to make sure nothing went astray.

```
f1b7av8 Second commit in my-feature
a9c8a1d First commit in my-feature
```

_Note: The second commit no longer has the same SHA-1 hash. That's because we rewrote history._

## Recommended Readings
* [Git Flight Rules](https://github.com/k88hudson/git-flight-rules)
* [Git Tools - Rewriting History](https://git-scm.com/book/id/v2/Git-Tools-Rewriting-History)
* [Git Branching - Rebasing](https://git-scm.com/book/en/v2/Git-Branching-Rebasing)

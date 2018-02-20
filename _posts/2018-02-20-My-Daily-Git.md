---
title: My Daily Git
layout: post
---

Oy vey.  

I did it.  I made a commit that introduced bugs... Easiest solution: just revert the state of my repository
to last Wednesday.  But how?

This got me on a new Git journey, and here's what I learned.

# Git Revert vs Git Reset
Git revert will "undo" the changes of a specified commit as a new commit, while git reset will
take you back to the specified commit as if nothing happened after it.

If there are a bunch of commits between HEAD and the commit that introduced the bug, then git reset
can be pretty drastic.  Git revert is way less dramatic: it does not attempt to undo all commits, but only
the commit you specify. 

```bash
# Revert back one commit
git revert HEAD

# Revert back another two commits 
git revert HEAD
git revert HEAD

# Revert to a specific commit in the past, leaving commits
# in between mostly untouched (probably a conflict can arise)
git revert shaNum

# Reset to a specific commit (and take on full state at that commit)
git reset --hard shaNum
```

* Atlassian: [Git Revert](https://www.atlassian.com/git/tutorials/undoing-changes/git-revert)
* Atlassian: [Git Reset](https://www.atlassian.com/git/tutorials/undoing-changes/git-reset)
* YouTube: [Resetting and Reverting](https://www.youtube.com/watch?v=u-kAeG4jkMA)

# Notes from Advanced GIT for Developers (Lorna Jane Mitchell)
## Watch
First of all, one non-Git thing that was cool was how Lorna used "watch" to monitor changes going on in 
`.git/objects` and `.git/refs/heads`. 

```bash
brew install watch
tmux
# Vertical split, e.g., w/ <ctrl>+a
# In one tmux panel
cd .git/objects
watch ls -l
# In the other panel (watch the "watch" panel as you do this)
touch somethingNew
git add somethingNew
git commit -m "Add somethingNew"
```

## Patchwork
When you stage a commit, you try to keep it atomic.  This means that instead of `git add .`, it's
much better to selectively add related files, e.g., `git add someFile someOtherFile`.  However, is granularity
at the file level the best we can do?!

No -- we can get much more granular, choosing to add (or not add) each difference in each file.

```bash
git add -p someFile
```

* YouTube: [Advanced Git for Developers](https://www.youtube.com/watch?v=duqBHik7nRo)
* http://codefoster.com/addpatch/


## Visual GitDiff
This one is so good, I updated my post about various diffs with it too.

Example:
```bash
git config --global diff.tool vimdiff
git difftool HEAD
```

## Find a Bug w/ Bisect
Looking across the commit landscape, you find yourself feeling confused. Maybe even scared.  There
is a bug lurking out there, ravaging your project... But whereTF is it?  

As written in the [Git Book](https://git-scm.com/book/en/v2/Git-Tools-Debugging-with-Git):
> Annotating a file helps if you know where the issue is to begin with. If you don’t know what is breaking, and there have been dozens or hundreds of commits since the last state where you know the code worked, you’ll likely turn to git bisect for help. The bisect command does a binary search through your commit history to help you identify as quickly as possible which commit introduced an issue.

I haven't used this yet, but it definitely sounds useful and worth looking more into.


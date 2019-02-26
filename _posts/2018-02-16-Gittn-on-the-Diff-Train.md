---
title: Gittn on the Diff Train
layout: post
tags: git unix-tools wwe
---

I have literally rebelled against using diff and git diff forever.  Thought it all
looked intimidating a long time ago, and learned to cope without them.  By cope, I mean,
I've survived diff'n files the brutish, caveman way line-by-line.  The reality is, 
before this past year, it wasn't even a huge concern: I worked on a few, mostly one-off coding/research efforts
at a time.  But nowadays? I'm constantly switching between projects at work, and working on
new ones that are extremely similar to old ones.  This means, I have a lot more need for
organization, intelligent reuse of code, and the ability to quickly determine changes I made to
projects I worked on last week, or last month, or just 1 of the 3 I worked on yesterday.  

You can say I've had my "aha" moment: "There should be a tool for this! Say what? There already is?"

# Toy Example
```bash
touch old.txt
echo 'Greetings, Earthling!' > old.txt
touch new.txt
echo '<beginGreeting>' > new.txt
echo 'greetings, eARTHLING!' >> new.txt
```

# Straight-Up Diff
```bash
diff old.txt new.txt
  1c1,2
  < Greetings, Earthling!
  ---
  > <beginGreeting>
  > greetings, eARTHLING!
```
From what I've read, the resulting diff output is considered to be machine-readable, and that the more
human-friendly stuff needs the -u flag. But I gotta say: I find it pretty readable.  

First thing to understand is that the diff output provides the steps necessary for transforming old.txt into 
new.txt.  At the top of an operation, we see some shorthand:  **1c1,2** just says that line **1** in old.txt needs to be 
**c**hanged to lines **1,2** in new.txt. The lines underneath tells us what operations to perform to get from old.txt to
new.txt:
* first, we are shown an operation on old.txt: "<" means remove
  - we remove line 1 from old.txt
* the operations below the dashed line (---) tell us the two lines we need to then add to arrive at new.txt

### Ignore Case
```
diff -i old.txt new.txt
  0a1
  > <beginGreeting>
```

If we do not care about uppercase or lower case, then the instructions are much simpler: after line **0** in
old.txt **a**dd **1** line ("<beginGreeting>") to get to a state equivalent to new.txt (ignoring any differences in case).

### Some Other Flags
The -w flag tells `diff` to ignore whitespace, which seems pretty dang useful.

# Unified Diff
This one is popular because it is similar to the output of `git diff`, so even if you like the straight-up
diff output above, you might as well get more comfy w/ unified diff.

```bash
diff -u old.txt new.txt
  --- old.txt	2018-02-16 15:29:22.000000000 -0500
  +++ new.txt	2018-02-16 15:29:23.000000000 -0500
  @@ -1 +1,2 @@
  -Greetings, Earthling!
  +<beginGreeting>
  +greetings, eARTHLING!
```

### Ignore Case
```bash
diff -u old.txt new.txt
  --- old.txt	2018-02-16 15:29:22.000000000 -0500
  +++ new.txt	2018-02-16 15:29:23.000000000 -0500
  @@ -1 +1,2 @@
  +<beginGreeting>
   Greetings, Earthling!
```

# Git Diff
This is not for comparing files, but for comparing versions of a file housed in different git commits, the working directory, 
and/or the staging area.

### Diff against previous commit
```bash
git diff HEAD
```

### Diff All Unstaged Changes in Working Directory with Staged Files
```bash
git diff
```

### Diff All Staged Files in Working Directory with Last Commit
```bash
git diff --staged
```

### Diff Files in Different Commits
```bash
git diff SHA1 SHA2
```

# VimDiff
This is a visual diff tool in Vim:
```bash
vimdiff file.txt similarFile.txt
```

Importantly, you can use vimdiff as a visual difftool in Git:
```
git config --global diff.tool vimdiff
git difftool HEAD
```

-----------------------------------------

## Some Refs
* [Github/Git Cheatsheet](https://github.com/github/training-kit/blob/master/downloads/github-git-cheat-sheet.pdf)

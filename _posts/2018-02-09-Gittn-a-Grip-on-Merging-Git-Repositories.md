---
title: Gittn' a Grip on Merging Git Repositories
layout: post
---

Let's say we have two Git repositories: `team_automations` and `side_project`.  The `team_automations` repository houses many
of our more recent, more maturely designed and versioned automation projects, while the `side_project` repository
contains one of our older, more scrappy automations. The more recent projects are all housed together, but respect an 
easy-to-understand directory structure.  This layout and its corresponding best (better?) practices weren't obvious to us when we 
were working on `side_project` (or on `other_side_project` and `experimental_project`, for that matter). But over time, we've 
gained experience with project organization, Git version control, and maintaining our own sanity. For a long time, we've respected 
the "if it ain't broken, don't fix it" rule, but our idealist tendencies are overcoming our fear (and possibly our wisdom).  Our goal
is to merge `side_project` with `team_automations`, while minimizing how many things we break, bugs we introduce, and hopefully
maintaining Git histories across both repositories.

Phew!  That was a long paragraph... But if you can relate to it, then read on!  In this post, I explore how to
use some more advanced feature of Git for merging repositories.

# Git Motivation
When starting out with Git, I truly learned the bare-bone basics: the minimum necessary for working with Git and Github:
not much more than add, commit, push, log.  This was all fine and dandy for a long while because (i) I maintained separate
repositories for each project I worked on, and (ii) I was the sole contributor to all the projects I worked on.

The first issue I ran into was the unncessary complexity involved in having a separate repository for each project,
especially when many of them are similar.  For related projects (e.g., several YouTube data collection projects), insights and 
understanding would often occur in parallel, and I would find myself updating code in each repository with similar Commit messages 
containing lessons learned.  Worse, I was sometimes updating the same code in different repositories. And even worse, I sometimes
was NOT updating the same code across all repositories.  This meant that some projects would benefit from recent improvements, 
while others would continue to have inefficiencies or duct-taped bug fixes.  

I went through some trial and error for a while, and probably still have a lot to learn.  But in terms of keeping related information
bundled, related automation projects at work benefit from being in the same repository, simply existing in different 
subdirectories.  Then, to get project-specific, some conventions can be put in place for Git commits such that one can benefit
from the more advanced features of Git Log. (Something I should read: [Atlassian -- Comparing Workflows](https://www.atlassian.com/git/tutorials/comparing-workflows))

A newer issue is that, as I work on new projects and generally overextend myself, other people are looking to takeover 
some of my older projects to add in some requested tweaks. My first reaction is, "I can do it!"  But in reality, I don't have
the time and my interest has moved on to other things.  However, I do want to clean up my projects a bit and merge 
related repositories in general.  This will make it easier on the newcomer, and easier on me to occasionally track what's going
on.  

## Git Log
Git commits are trackable 
* by author: `git log --author="Kevin"`
* by date: `git log --after="2017-12-25" --before"2018-02-02"`
  - this could also be written: `git log --since="2017-12-25" --until"2018-02-02"`
* by message content: `git log --grep="KEviN'S secrET msG" --regexp-ignore-case`, where that
last flag can actually just be `-i`
* by source code content: `git log -S"def meaningOfLife()"`
* by file: `git log -- myScript.py`

Honestly, there are a couple more features I do not fully understand 
(see: [Advanced Git Log](https://www.atlassian.com/git/tutorials/git-log)).

# Git Goin'
Ok, now we have some motivation to merge repositories, and a tool that will help us maintain and track
the larger, merged repository.  

After reading/skimming way too many [StackOverflow](https://stackoverflow.com/questions/1425892/how-do-you-merge-two-git-repositories)
ideas, arguments, and insights, and just feeling like, "Oh
funk, I really have no idea about anything in life anymore," I'm just going to focus on reading one guy's 
article and hope like hell it's what I need.  

That one article is by [Saint Gimp](https://saintgimp.org/2013/01/22/merging-two-git-repositories-into-one-repository-without-losing-file-history/).  Finger's crossed.  Whatever follows is probably a CliffNotes version of Gimp's article...maybe with a few sprinklings
of...



# Some References
* [Merging Two Git Repositories into One Repository Without Losing File History](https://saintgimp.org/2013/01/22/merging-two-git-repositories-into-one-repository-without-losing-file-history/)
* Atlassian: [Advanced Git Log](https://www.atlassian.com/git/tutorials/git-log)
* Atlassian: [Comparing Workflows](https://www.atlassian.com/git/tutorials/comparing-workflows)

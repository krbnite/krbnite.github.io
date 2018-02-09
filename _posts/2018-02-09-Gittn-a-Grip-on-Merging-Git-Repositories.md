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
funk, I really have no idea about anything in life anymore," I want just to focus on reading one guy's 
article and hope like hell it's what I need 
([Saint Gimp](https://saintgimp.org/2013/01/22/merging-two-git-repositories-into-one-repository-without-losing-file-history/). Here's 
a bit of evidence that he was after the same thing that I now seek:
> "I wanted to glue to repositories together and have them look as though they had always been one repository all along."

For my purposes, Gimp's recipe goes something like:
1. Add a remote to `side_project` from `team_automations`
2. Merge `side_project` master to `team_automations` master
3. Make a subdirectory called side\_project
4. Move all `side_project` files into this subdirectory
5. Commit the file moves

### Git Hold Up!
This solution might work, sure, but more than anything it made me realize I need to learn more about merging, submodules,
and subtrees:  exactly what I wanted to avoid above, after reading through a long list of potential strategies on 
StackOverflow.  

So, without further ado: some brief, technical excursions.

# Submodular Gitjitsu
> "It often happens that while working on one project, you need to use another project from within it."

Actually, yea -- that's true.

> "Perhaps it’s a library that a third party developed or that you’re developing separately and using in multiple parent projects. "

Go on...

> "A common issue arises in these scenarios: you want to be able to treat the two projects as separate yet still be able to 
> use one from within the other."

Ok, yea.  I've definitely had that issue.  I mean, that's even relevant to my current situation.  There are some python
libraries I've developed that are used in multiple projects.  This sounds useful.  

So -- submodules, you say? 

> "Submodules allow you to keep a Git repository as a subdirectory of another Git repository. This lets you clone 
> another repository into your project and keep your commits separate."

This sounds great, but after reading the whole [Git-SCM chapter on Submodules](https://git-scm.com/book/en/v1/Git-Tools-Submodules),
dealing with them might be more complex than it's worth.  At the least, it seems skippable for now -- onto subtrees!


# Subaboreal Gittinese
Here's the reference: [Subtree Merging](https://git-scm.com/book/en/v1/Git-Tools-Subtree-Merging)

It's basically a better way to do the same thing that submodule are used for... Seems pretty awesome, actually.

# Current State of Affairs
Ran out of Exploration Time today, and did not pull the trigger on any of this.  When I get back to this, I'm going to look 
more into [Subtree Merging](https://git-scm.com/book/en/v1/Git-Tools-Subtree-Merging) and 
[Saint Gimp's Solution](https://saintgimp.org/2013/01/22/merging-two-git-repositories-into-one-repository-without-losing-file-history/)



# Some References
* Git-SCM Book ([v1](https://git-scm.com/book/en/v1/), [v2](https://git-scm.com/book/en/v2))
  - [Git Submodules](https://git-scm.com/book/en/v1/Git-Tools-Submodules)
  - [Git Subtree Merging](https://git-scm.com/book/en/v1/Git-Tools-Subtree-Merging)
  - [Advanced Merging](https://git-scm.com/book/en/v2/Git-Tools-Advanced-Merging)
* Saint Gimp: [Merging Two Git Repositories into One Repository Without Losing File History](https://saintgimp.org/2013/01/22/merging-two-git-repositories-into-one-repository-without-losing-file-history/)
* Atlassian
  - [Advanced Git Log](https://www.atlassian.com/git/tutorials/git-log)
  - [Comparing Workflows](https://www.atlassian.com/git/tutorials/comparing-workflows)

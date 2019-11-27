---
title: Preserving History While Merging Two Repos
layout: post
tags: git easi
---

At work we have this predictive modeling project that has taken on multiple iterations due to the project
starting and stopping over time, and different individuals taking it on after others
had moved on to other things.  

During the first iteration, a data processing and machine learning pipeline was 
developed.  For <<reasons>>, the project developer discontinued work on it and
someone else took over.  The new developer liked certain parts of the project, but
disliked others (due in part to some those <<reasons>>).  Not wanting to delete 
a bunch of files or destroy what value might be stored in the pieces of the pipeline
he was going to discard, the new developer decided that starting a new repo would be 
best.  And it was so: the first several pieces of the pipeline were ported into the
new project repo and the project took flight in a new direction.

Then <<new reasons>> occurred, the new developer was gone, and the project was in limbo once again.

At this point, to my mind, the project was a nightmare that no new time or talent 
should be spared on...but nonetheless, time and talent was requested once again.  This
time I was one of two thrown into the mix.

Part of my mission was to explore <<reasons>> and <<new reasons>> to get into the
thinking process of my predecessors -- to understand what the constraints were, why
certain decisions might have been made, where bugs may reside in the code.  As I did this, I
began populating the more recent repo with a bunch of exploratory Jupyter notebooks, detailing
my detective work...  Eventually, I began changing some files here or there, trying out my own 
ideas and models... But all along, I had my own feelings of discomfort 
towards covering up too much of the past work this way.  

Yes, some things needed to change,
but it felt important that the history was preserved in a way someone could easily
search through without needing to be some kind of `git log` guru.  At one point, we even 
decided to start from scratch.  

Was I to make yet another repo?  

No, what I wanted was to have one master repo, which housed each iteration of the project
as a subdirectory.  I wanted to do this without losing any of the commit history. Now 
remember: none of us on my team are full-fledged software developers or Git experts.  We are
researchers and data scientists.  I'm sure there might be standard ways of approaching this
kind of problem, e.g., maybe keeping each version on a separate branch.  But I wanted
a solution that allowed a nontechnical manager to be able to skim files just as easily
as one of the project developers.  

Fortunately, I found someone who had a very similar need and wrote down the Git code
required:  
* [Merging Two Git Repositories into One Repository Without Losing File History](https://saintgimp.org/2013/01/22/merging-two-git-repositories-into-one-repository-without-losing-file-history/)

However, two issues:
* some of the code is specific to PowerShell on Windows
* some of the Git-specific code is outdated and needs to be updated

Below, I include the code that worked for me.

For simplicity, let's assume we have two repos -- `proj_v1` and `proj_v2`.  Let's assume that
I basically want to start a new repo, `proj_v3`, but want to make sure all three repos are
actually just subdirectories housed within the same parent repo, `proj`.  I want to merge these
without losing any history.  

```bash
# Make new "master" project direcotry
mkdir proj

# Create the new repository
cd proj
git init

# Make an initial commit (necessary to do before merging)
#  -- e.g., create a README file called README.md.new (`.new` so that merge won't have conflicts)
echo "This repo places proj_v1, proj_v2, and proj_v3 under the same roof." > README.md.new
git add .
git commit -m “Initial commit”

# Add a remote for and fetch the old repo
git remote add -f proj_v1 <proj_v1 repo URL>

# Merge the files from proj_v1/master into proj/master
git merge --allow-unrelated-histories proj_v1/master

# Move the proj_v1 repo contents into a subdirectory so they don’t collide with 
# the proj_v2 repo to be merged next
mkdir proj_v1
for item in `ls | grep  -Ev "proj_v1$|README.md.new"`; do mv $item proj_v1/; done

# Commit the move
git commit -m “Move proj_v1 contents into proj_v1/ subdir”

# Repeat above steps for proj_v2
git remote add -f proj_v2 <proj_v2 repo URL>
git merge proj_v2/master
mkdir proj_v2
for item in `ls | grep  -Ev "proj_v1$|proj_v2$|README.md.new"`; do mv $item proj_v1/; done
git commit -m “Move proj_v2 contents into proj_v2/ subdir”
```

The original code includes a bit more about branches, if you're interested in it.  This particular
project only had master branches, so it was not a concern of mine.




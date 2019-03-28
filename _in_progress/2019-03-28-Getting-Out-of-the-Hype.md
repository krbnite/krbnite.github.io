There will be some overlap in the subsections.  That's ok.  I'm just trying in general to
come up with some lessons learned over the past ~13 years or so.

# Avoid Playing Catch-Up
One of my PhD advisors, Dr. Andrew Gerrard (Andy), would always warn against
working on what's popular -- unless you just so happened to be the original, or in on 
the topic early enough.  It's inevitably a game of catching up, and trying
to stand out in a crowd of very homogeneous work.  For space weather related research,
the popular thing often closely tracked whatever NASA space mission related to the magnetosphere,
ionosphere, solar wind, or sun that was set to launch in the
next 2-3 years.  Makes sense, right?  That's where the funding is going to be -- in analyzing
the mission's data.  



# Skip the Funding Fight
The reason is simple: the currently hot topic is almost certainly overblown, and if not,
there is almost certainly several research groups focused on the issue and any related issue, 
and so the funding is difficult to get...  More importantly, because of these hype clusters,
there is often a huge assortment of interesting problems ignored and unsolved...


In data science circles these days, it's often something to do with
neural networks.

Andy's advice here would be to run.  



# Respect the Law of Limiting Originality
So the latest craze inspires you, and you think you have some great ideas!  Well, be honest
with yourself: how original are those ideas likely to be?  At least treat yourself to
a thorough Google search:  how many blog posts or StackExchange comments come dangerously close
to your idea?  Or on Google Scholar:  how many papers look like they basically cover your
idea?  The point is to be rigorously honest with yourself:  yes, you can work on your idea and
push the envelope further, but will it be an incremental push in a long succession of 
similar, incremental pushes?  Will your voice be heard in the raging sea of similar 
voices?  Not everyone will, can, or should be working on original or unpopular ideas, but
at least be honest with yourself about the expectations:  for example, at a conference, 
if your presentation  is yet another "popular craze" piece, expect general interest, but
some yawns, eye rolls, and stupid questions from people who are also excitedly trying to
keep up with the craze and (a) want to prove they know things too, or (b) want to prove that
you do not know things -- thus shouldn't be trying to hang with the cool kids. 

Point is, once you stop following the craze, the crowd and mentality will shift:  how many
times have you been at a conference of nearly everyone doing "popular craze" pieces, except
that one presentation you sat in on where the presenter talked about something completely
unrelated, but so damn insightful -- and rebellious!  That person stood out, right?  


# The Drone Risk
You might apply for a job that is interested in the "popular craze", and you might be 
asked to come present on something you've done that can showcase your skill set.  You bet your ass
that in the data science field, the "popular craze" thing is neural networks -- and your interviewer
wants to know that you are aware of it, and even particularly skilled at it, but also seriously wants
to throw out any potentially false positives!  If you come in and have 10 minutes to talk, and if
you choose to talk about the broadstrokes of a neural network project, you are going to sound like
a billion other candidates who came in and did the same.  

You're better off presenting on a linear regression you've done!  At the very least, this 
will wake up the interviewers and their pals in the room.  

Interviewer: (thinking): "Who is this ridiculously crazy,
rebel bad ass coming into my room and showing me a goddam linear regression!"

Others in room: (smirking, whispering)

Interviewer:  (aloud):  "Interesting.  Wonder why you chose to do it that way?"

YOU:  (aloud):  ...excellent explanation about how this worked nearly as well as other methods, is highly interpretable, was easy to explain to the marketing team and help deploy with the engineering team, and helped increase revenue streams, 
etc, etc... (all the stuff your interviewers truly care about on a day-to-day level)

Interview's Crony:  (aloud):  "Surely, a hidden layer or two would have made this better."

You:  ...chance to show that you are fully aware of neural networks, hidden layers, activation functions, and whatever...

Interviews:  (aloud):  All good points.


# The Sales Pitch
Do you know when pitching a "deep neural net" works really well?  

One example is when you're talking to a bunch of data science enthusiasts that are currently
seeking out their first job.  

Another great place to pitch this might be on Twitter.  

In the business world, it might work when casually discussing new, exciting things with
the department head (say 3-4 notches above you on the totem pole) who is sincerely interested in all the 
buzzwords.  Other members on your data science team might also share a lament session with how
it would be awesome to do X, Y, and Z.  

Almost no one else will be nearly as excited.  That is, the other departments, like marketing, advertising, or legal,
who you are supposed to work with.  For one, they likely think much less of you and data science than you expect.  For
example, they might think they know better and do not need your help.  Fact is, pressure is on them to work
with you and your team, and it might not be their choice to do so... But, the decisions about your work and the quality
of your work will still be theirs.  Expect to be questioned at every move before you build trust and common 
language...  Expect them to disbelieve the hype...  Expect to be reasonable, to listen, to understand where
they are coming from... Expect to not push your own agenda on them!





Be aware: you will not have the data required to directly solve the problems these departments are having, and
you will often have messy data that appears almost nonsensical to you.  Everyone talks about the end of "feature
engineering", but at the same time you'll hear about how 80% of data science work is feature engineering and
data clean-up.  The first one is mostly hype; the second one is what matters!  Be the best damn data cleaning,
feature engineering maniac you can be!  Get comfortable starting with unfamiliar data sets, the reshaping process
(e.g., transforming categorical variables into dummies), the imputation-or-not process, and so on.  Get comfy
asking questions and coming up with strategies with the domain experts.  Hell, get them to think everything
was all their idea.  Leave ego at home.  


Always ask yourself if a metric means what you think it means...  Forget models for a second.  If you 
are given a data set, the first thing you will do is try to understand it and explain it to
stakeholderes...  You will inevitably hear someone say something like, "On average, people watch 2
hours of TV a day."  What does that mean?  I can guarantee there are people in the room who think
it means that people more-or-less watch 2 hours of TV a day.  But if you look at the data distribution,
it's not normal -- log-normal at best!  Maybe power-law distributed.  


Median is a little bit easier
to understand: "On median, people watch 0 hours of TV a day."  This means that the bottom 50% of customers
watch 0 hours a day, and the top 50% watches 0 or more hours a day.  This will run into some problems
though: (1) the boss won't want to hear it, and (2) be careful about integration time.  Both are kind of
related: the boss **knows** TV is being watched because the average is 2 hours per day, and so the median
just sounds wrong to them -- and more importantly, doesn't help tell the story that needs to be told to 
their manager!  Importantly, what does this tell you: the daily median might be 0, but the weekly
median might be 1 hour, and the monthly might be 8 hours.  The daily median makes it sound and feel like
people don't watch TV, but it might be better interpreted that people do not watch every day, but they do
typically watch at one point every week, and in general have heavier watching at certain points during the month!

Point is, don't fool yourself, don't fool your boss, don't believe the too-good-to-be true metrics, but
also don't believe the too-negative-to-be-true metrics either...  Metrics help tell a story: your job is to
look at enough metrics to ensure you are telling the right story.  Or, importantly, you need to help tell
the a persuasive story that helps your manager and, ultimately, your company.  So, seriously, if you have
to opt between the "0 hours on median per day" story and the "2 hours on average per day" story, choose 
the latter -- for starters.  Then move into the daily, weekly, and monthly medians when people are ready
to listen -- and discuss how this is an opportunity:  the company can target resources during optimal 
times, at the aggregated and individualized levels!


# Avoid Stupid Mistakes
If your model is awesome on the first go, then you've done something wrong -- or at least, you should
convince yourself of the likelihood that you've done something wrong, then investigate!

The newbiest of newb mistakes is leaving in IDs -- customer ID in marketing, patient ID in healthcare, and
so on.  If you use a deep learning network with enough parameters, it will simply learn to become
an ID/outcome dictionary.  

Second up is something like area code or zip code.  Unless you have multiple units/customers/patients per
area/zip code (say 30+), you are likely going to run into the dictionary principle again.  



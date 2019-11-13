---
title: Hyde and Search
layout: post
tags: jekyll easi
---

I finally got around to adding a collated tags page.  And also Search.


# Installing Jekyll again...
To experiment with Jekyll code and how it affects my site's appearance, I wanted to
work w/ `jekyll watch` (if I remembered the command correctly) on my computer, instead
of just writing directly on GitHub like I usually do when writing blogs.  But new laptop,
so I had to reinstall Jekyll...and ruby.

```
brew install ruby
printf '\n\n#Add brewed ruby to path \nexport PATH=/usr/local/opt/ruby/bin:$PATH \n' >> ~/.bash_profile'

gem install --user-install bundler jekyll
```

uh... can't get it to install.. throwing errors

Some guy rec'd this:
```
echo '# Install Ruby Gems to ~/gems' >> ~/.bash_profile
echo 'export GEM_HOME=$HOME/gems' >> ~/.bash_profile
echo 'export PATH=$HOME/gems/bin:$PATH' >> ~/.bash_profile
source ~/.bash_profile
```

still doesn't work... Tried install `rbenv` too... nothing...

Come back to this later...

# References
* Long Qian: [Jekyll Tags on Github Pages](http://longqian.me/2017/02/09/github-jekyll-tag/)
* https://blog.webjeda.com/instant-jekyll-search/
* https://jekyllrb.com/docs/installation/macos/

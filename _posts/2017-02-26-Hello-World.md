---
layout: post
title: Hello World! (Want your own GitHub blog?)
---

Want to host your blog on GitHub? I got up and running by following this 
[how-to guide](https://www.smashingmagazine.com/2014/08/build-blog-jekyll-github-pages/)
from [SmashingMagazine](https://www.smashingmagazine.com).

## The Quick One-Two (3, 4, 5, 6, 7)
1. Fork [https://github.com/barryclark/jekyll-now](https://github.com/barryclark/jekyll-now), 
   which is a GitHub blog starter kit.
2. Change the starter kit's repository name to yourUserName.github.io, which lets GitHub know
   this is the repository you want to host your site in.
3. Get a local copy of your site: git clone https://github.com/yourUserName/yourUserName.github.io 
4. Edit the \_config.yaml (e.g., change website name, bio pic, etc)
5. Stage (git add \*), commit (git commit \*), and push (git push)
6. Make first blog entry by editing the provided MarkDown file in \_posts/, and 
   referencing this 
   [MarkDown cheatsheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet),
   if your eyes start crossing funny. 
7. Learn more about customizing the YAML options for your blog entries (e.g., how to use
   tags, categories, and more): 
   [http://jekyllrb.com/docs/frontmatter/](http://jekyllrb.com/docs/frontmatter/)

## Finishing Moves
Do yourself a favor and install Jekyll.  Otherwise, there's going to be a
lot of stage-commit-push.
[Get jekyll](http://jekyll.tips/jekyll-casts/install-jekyll-on-os-x/) immediately!

1. sudo gem install github-pages
2. sudo gem install jekyll
3. cd path/to/local/copy/of/yourUserName.github.io
4. jekyll watch --server
5. open http://0.0.0.0:4000/

Disqus: Want comments? Set up a [disqus account](https://disqus.com/). 
When logged in to your
Disqus account, click on Settings > Add disqus to site.  The site will
help you figure it all out.  Update your _config.yml file with your
Disqus short-name.  (Follow the Disqus directions and you will
know what that means.)


## More Info
The [SmashingMagazine](https://www.smashingmagazine.com/2014/08/build-blog-jekyll-github-pages/)
article is much more detailed, including info on how to 
use your own domain name, how to import blog posts from WordPress, how to change
your blog's template/theme, and more. So check it out!

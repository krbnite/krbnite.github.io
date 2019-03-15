---
layout: post
title: Relearning LaTeX
tags: latex research easi
---

Haven't used LaTeX much since I finished my PhD dissertation. I did try using it when I first
began at WWE to write up mini papers on the projects we were working on... My boss said, "This is
nice, but unnecessary."  Haha -- and that was that.

Now I'm at a more research-oriented workplace, where we actually plan to write up papers now and then.  Amazingly
enough, with all the sharing tools we already have or that are generally available, it still seems like there
is no agreed upon "great way" of collaborating on a research paper.  Caveat: I didn't really google that too 
much.  For example, my manager first wanted to use EndNote -- she wanted to write in Word
and use EndNote to auto-generate the propert formatting and the bibliography.  I hadn't used EndNote previously,
but she was really enthusiastic about it and I was open to it.  Also, I figured since we use SharePoint, we would
have a form of version control built in...  But -- and this is all hearsay -- apparently
the EndNote product was sold to a new company, is no longer great, and she is not interested in using it.  

I mentioned LaTex and BibTex a few times... Didn't really gain much traction.  But nothing else did either.  

We are working on a review, and so there are a ton of references.  Another challenge altogether.  We also have
ReadCube -- and we wondered if that would be helpful.  I found that it was.  Basically, you want the Chrome
extension and you want to create a ReadCube list dedicated to your paper.  To generate `.bib` files for your paper,
select all the papers in the list, then 2-finger click to bring up the export options.  

Ok, cool -- so now we have an agreed upon way of tracking papers and generating references that BibTex
can use... Maybe we should use LaTeX afterall.

So here I am, writing a `.tex` file and I think: "Uh, wow, I don't remember anything."  One of the hard parts
of starting from an empty canvas is the head matter: all of the stylization and package commands, etc.  But you
can get templates easily enough, e.g., I'm currently using the [PLOS template](https://www.latextemplates.com/template/public-library-of-science-plos).

Cool: I'm up and running... But a new question hits me: "How do I compile the PDF file again?"

I know from the command line there is some crazy sequence, like:
```
pdflatex
bibtex
pdflatex
pdflatex
```

But I never did that while writing LaTex code... Way back when, I was introduced to [TeX-9](https://www.vim.org/scripts/script.php?script_id=3508) 
in Vim, where you you can soft compile like `/k` and hard compile like `/K`.  So I installed that onto my work
computer:
```
# assuming you use Vim and have pathogen installed
cd ~/.vim/bundle
git clone https://github.com/vim-scripts/TeX-9
```

Oops!  Before compiling, make sure you have the LaTeX tools installed!

```
# assuming you're working on a mac
brew cask install mactex
```

Then make sure to put commands like `pdflatex` and `bibtex` into your PATH:
```
# add this line to ~/.bash_profile if you know what's best for ya!
export PATH=$PATH:/Library/TeX/Distributions/.DefaultTeX/Contents/Programs/texbin/
```


Ultimately, my boss likes Word.  I've found that people tend to recommend two methods:
1. Load the PDF into Word (works reasonably well)
2. Use pandoc 
  - if necesssary: `brew install pandoc`
  - then: `pandoc -f latex -t docx file.tex -o file.docx`
  
I tested out the pandoc method: it worked very well!  The formatting was not the same as the PDF, but the overall delivery 
was excellent.  Certainly good enough to send out for comments.




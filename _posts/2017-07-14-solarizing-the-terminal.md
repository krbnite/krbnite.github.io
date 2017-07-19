---
layout: post
title: Solarizing the Terminal
---

In RStudio, I use the "Material" color theme, which has a midnight blue background similar to the popular "solarized" theme.
In my never-ending quest to transform my Terminal/Vim/iPython set up into something more like RStudio, I wanted to figure out
how to do this in Vim.

The [solarized theme](https://github.com/altercation/solarized) has been created for many
environments, including Vim.  However, one thing I learned after an embarrassing amount of time
is that the Vim-specific package works best for Vim GUIs like, Mac Vim.  If you follow
the advice about using [Vim in Mac's Terminal](http://ethanschoonover.com/solarized/vim-colors-solarized) 
carefully, you will learn that the best route here
is to solarize the Terminal itself --- in which case, Vim will be solarized by default.

```
git clone https://github.com/altercation/solarized
cd solarized/osx-terminal.app-colors-solarized/
open Solarized\ Dark\ ansi.terminal   # opens new colorized terminal and automatically
open Solarized\ Light\ ansi.terminal   #     loads Dark / Light Palette into system
# To set as default, in mac's top-most bar:  
#        Terminal > Preferences > click on one / set as default
```

If you do this, you have to discard any of the advice associated with the Vim-specific solarized package. That is,
comment out any of this stuff in your .vimrc file (if it's there):
```vim
"set term=xterm  
"syntax enable
"set background=dark
"colorscheme solarized
"let g:solarized_termcolors=16 "default, if Terminal not solarized then  256
```

To my surprise, I actually like the light solarization for Terminal better than dark.


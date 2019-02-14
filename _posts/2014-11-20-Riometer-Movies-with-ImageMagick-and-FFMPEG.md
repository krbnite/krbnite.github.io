---
title: Riometer Movies with ImageMagick and FFMPEG
layout: post
tags: riometer space-weather graduate-school
---



Let's say there exists a server with a few years worth of daily riometer images.  And suppose
we want to download all of 'em and make some time-lapse-like movies for each year of data.  First,
how do we quickly download 1000s of images?  Secondly, how do we glue 'em all together into a movie?

# WGET: Download En Masse
This is a great use case for the wget command line tool:

```bash
wget -r -nH --cut-dirs=2 --no-parent --reject="index.html*" <IpAddressOrURL>
```

* -r:  recursively retrieve from directory
* -nH:  no host directories: disable generation of host-prefixed directories on local machine; i.e., no URL name in directory names on your machine
* --cut-dirs=2:  when saving to local machine, cut out the first two directory names after the host name
* --no-parent:  do not ever *ascend* to the parent directory when downloading recursively; this is useful since it guarantees only files below a selected hierarchy will be downloaded
* --reject="pattern":  used to specify comma-separated lists of file name suffixes or patterns to accept or reject. Note that if any of the wildcard characters, *, ?, [ or ], appear in an element the list, it will be treated as a pattern, rather than a suffix.

# ImageMagick: Make Some GIFs
This is the type of command you should issue:

```bash
convert -delay 20 -loop 0 myPics*.ext myMov.gif
```

* \* is a wildcard: your pics should be numbered with same prefix
* .ext stands for whatever extension you have compatible w ImageMagick, e.g., jpg
* The "delay" flag is in hundredths of a second (so this is 5 frames per sec)
* The "loop" flag tells ImageMagick to repeat the animation ad infinitum.

Unfortunately for me, I had to troubleshoot for a bit.  Let's see why!

## First Error
```bash
dyld: Library not loaded: /usr/local/lib/libfreetype.6.dylib 
Referenced from: /usr/local/bin/convert 
Reason: image not found 
Trace/BPT trap: 5
```

Ok, so let's try a few things:
* brew update
  - still get the same error
* brew upgrade
  - still get the same error
* brew doctor  
  - found out it could have to do with broken symlinks
* brew unlink imagemagick; brew link imagemagick
  - still get the same error
* found out it could have to do with broken symlinks of its dependencies
  - figured out how to list dependencies
  - brew deps imagemagick
* unlinked and relinked all dependencies 
  NEW ERROR!

## Second Error
```bash
convert: no decode delegate for this image format `JPEG' @ error/constitute.c/ReadImage/501. 
convert: no images defined `movie.gif' @ error/convert.c/ConvertImageCommand/3187.
```

I first tried to use the "debug all" flag on convert
```bash
convert -delay 20 -loop 0 jack*.jpg jack.gif -debug all
```

But holy shit did a lot of text hit the screen...almost impenetrable! So I tried linking/relinking all brew formulas 
using this nifty piece of code:
```bash
brew list | xargs -I % sh -c 'brew unlink %; brew link %'
```

Lots of stuff happened! But still: problem not solved.

Hmm... Even though my ImageMagick dependencies were installed, for some reason JPEG was not listed as 
an ImageMagick "delegate":

<figure>
  <img src="/images/image-magick-delegates.png" width="400">
</figure>

So I googled "homebrew imagemagick delegates" and up popped this 
[link that saved the day](https://stackoverflow.com/questions/5624778/imagemagick-jpeg-decode-delegate-missing-with-os-x-homebrew-install)

THE SOLUTION WAS:
```bash
brew uninstall jpeg
brew uninstall imagemagick
brew install --force jpeg
brew install --force imagemagick
```

Yes! I was then able to make some riometer movies.

Some downsides:
* it took sooooo long to create a yearly GIF (365 separate images)
* also, the delay I chose was way too short
  - had to run it all again :-(

# From GIF to MP4: The Desire for Pause
* Turns out I never had to make a GIF first
* Turns out ImageMagick is unnecessary for any of this: just need FFMPEG

### 1-second frames
 ```bash
ffmpeg -framerate 1 -pattern_type glob -i '*.png' -c:v libx264 -pix_fmt yuv420p MCM_2013.mp4
```

### Very Fast Year
```bash
ffmpeg -pattern_type glob -i '*.png' -c:v libx264 -pix_fmt yuv420p MCM_2013.mp4
```

For the earlier years, the riometer images were saved as as GIFs, which have to be converted to PNGs before using FFMPEG:
```bash
# CONVERT ENTIRE DIRECTORY AT ONCE  
#  -- mogrify is an ImageMagick command
mogrify -format png *.gif 
```

The method for folders full of GIFs:
```bash
mogrify -format png *.gif
rm *.gif
ffmpeg -framerate 1 -pattern_type glob -i '*.png' -c:v libx264 -pix_fmt yuv420p MCM_2009_1fps.mp4
ffmpeg -framerate 5 -pattern_type glob -i '*.png' -c:v libx264 -pix_fmt yuv420p MCM_2009_5fps.mp4
ffmpeg -framerate 10 -pattern_type glob -i '*.png' -c:v libx264 -pix_fmt yuv420p MCM_2009_10fps.mp4
rm *.png
```

---------------------------

Cool write-up on using FFMPEG:
* http://blog.room208.org/post/48793543478

Speed up / Slow down a video
https://trac.ffmpeg.org/wiki/How%20to%20speed%20up%20/%20slow%20down%20a%20video

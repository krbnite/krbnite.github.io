It's 10pm on a Wednesday night and I'm tired.  I should go to bed!  But no, dear Reader.  Instead
I'm going to watch through this [Face Recognition](https://www.linkedin.com/learning/deep-learning-face-recognition/build-cutting-edge-facial-recognition-systems)
lecture on Lynda.  It's one of those things where a part of me is like, "I already know this stuff," and
the other part is like, "Yea, but I never saw that phrase 'histogram of oriented gradients' before."  Then the first
part of me, knowing the second part has a decent point, is like, "Oh fine, whatever!" 

And so here I am!  And here you are, dear Reader.  We'll both brush up on some face recognition using
CNNs and whatever this lecture throws our way.  And in the process, maybe we'll learn something new!

Rock on!

# Recognizing the Particulars of a Skull's Fleshy Overlay 
Though remembering someone's name can be a real bitch, recognizing them in a crowd and
realizing you don't remember their name is easy-peasy!  As humans, we can take 
facial recognition for granted.  We're generally so good at it, that we see face where face
don't exist.

PIC_EXAMPLE1
PIC_EXAMPLE2

But how would you program that ability on a computer?

People have been trying to figure this out since the 1960s.  The earlier systems were rules based,
and were best at filtering out non-matches rather than identifying the true match.  The last step
was left for a human, who would select from a short list of candidates.  Between the 1970s and 2000,
the systems employed more and more complex statistical procedures, but still were never great.

No, greatness only came when computational power was great enough, and the availability of data voluminous
enough, to usher in the era of deep learning -- that is, a time when neural networks came into 
fruition.  Let's face it: for face recognition, an extra key ingredient has been the advent and subsequent
explosive growth of social networks, and unfettered sharing of selfies.  With this in mind, it should 
be no surprise that companies like Facebook and Google are leaders in this space.

The use of face recognition systems is probably more widespread than you think.  We all know it happens
when we upload a pic to Facebook -- a few years ago, it would ask, "Is this you?"  Nowadays, it just knows.  Same
thing happens when using an iPhone or Google Photos.  But it's more pervasive than that. "Whether you're uploading a 
picture to a social network, walking into an airport, or looking at an
electronic billboard, there's a good chance that face recognition systems are in use."  

Face recognition systems can be designed for pursuits such as:
* identity verification (e.g., can use 1-shot learning for this)
* organizing and/or sorting photos by individual 
* tracking an indidival through a network of camera feeds
* counting the number of people in a pic
* counting the number of unique people that appear in a camera feed
* identifying individuals with similar appearances

You can throw together a CNN from scratch or in Keras...but these days you can also use one of many
APIs that will do it all for you. For example, Amazon's Rekognition API performs face recognition,
as well as emotion detection, and motion tracking.  Microsoft's Azure Face API also detects age and
gender.  This is all cloud stuff though -- it can cost a few bits of coin per use, and also requires
you to upload your data and possibly use associated cloud services (e.g., storage).  Also, if you're
a DIY guy or gal or non-binary, then this just doesn't sit right!

But just how DIY are you?  As Carl Sagan said, "If you wish to make an apple pie from scratch,
you must first invent the universe."  I think for an intermediate, acceptable level of engineering, it is 
ok to use some open source tools.  Go ahead and stand on the shoulders of giants.  Let creation beget
creation!

The tools recommended by the course instructor are [OpenFace](https://cmusatyalab.github.io/openface/) and 
[dlib](http://dlib.net/).  I honestly have never used or heard of either.  Back in my face recogntion days circa
2017 when I was working at WWE and doing Udacity's deep learning nanodegree, I used TensorFlow, Keras, and
transfer learning for this stuff.  In this course, we use a Python API to dlib, written by the course
instructor, called `face_recognition`.

# Basic Recipe for Face Recogntion
1. Locate and extract:  given an image, scan through and locate all faces; for each face, extract a cropped
sub-image.
2. Identify facial features:  Gain a better understanding of the orientation of the face -- how is it turned,
and what parts are obscured?  Locate and map each facial feature, which will be used as data along with the 
cropped image itself.
3. Align faces into standard pose: using the facial feature mapping and an average facial feature mapping (positioning
template) for faces looking directly into the camer, transform the cropped face image into a face-forward image.
4. Represent the face as a vector of measurements.  In this representation, images of the same person should 
map to similar vectors (i.e., vectors of similar magnitude and orientation).  This vector can be composed in various
ways, e.g., using a neural network embedding, etc.
5. Compare to other known faces, e.g., use the Euclidean distance metric between facial vectors.


## Face Detection (Locate & Extract)
We will use a sliding window approach with a machine learning classifier.  The classifier will
simply assess whether or not a face is present in each instance of the sliding window.  To do this
is simple:  1. train the classifier on cropped images labeled as 1 or 0, face or no face.  2. Slide
window across an image and extract any window in which a face is detected.

There are 3 face detection algorithms covered in this course.  In order of increasing accuracy:
* Viola-Jones
  - tree-based method that indicates the presence of a face if a lightness/darkness pattern 
    congruent with faces is detected
  - low accuracy (lots of false positives), but computationlly simple and quick (basically, 
    no reason to use it)
* Histogram of Oriented Gradients (HOG) 
  - This is the one that got me to watch the course
  - Looks for shifts from light to dark regions
  - Slower than VJ, but more accurate
  - Less accurate than CNNs, but do not require special hardware, etc
* Convolutional Neural Networks (CNNs) 
  - There it is!  This is the one I expected this course to be about.


....section 3....







Connection with one-shot learning:
> "Zero-data learning shares some similarities with one-shot learning which can be seen as a particular case of 
> zero-data learning where the description of a class is a prototypical sample from that class in X^n."

This is particularly relevant to this early paper, where they use "7 by 5 pixels 'canonical' representations" of
of numbers and letters "task description binary vectors...inspired by sys font characters of size 8."  That is,
their description vectors are basically low-resolution, miniturized versions of the input data set.  I actually 
thought this was interesting: it's much closer to one-shot learning than later ZSL projects that use, say, a 
word embedding vector as a description for an image.  Makes me think: a fun project would be do something similar
with a limited animal data set, where the description vectors can be cartoonized versions of the animals.

Connection with recommendation engines:
> "Finally, though zero-data learning has never been investigated as a general learning framework, it has been tackled 
> in particular application settings before. In the context of recommender systems, it is known as the "cold start"
> problem (Schein et al. 2002) where a task corresponds to an object to recommend." 

Combo of human and machine intelligence:
> "[Zero-data learning] can then be considered as falling between human engineered systems, which requires a full, 
> manually-specified description of a new task's solution, and pure machine learning, which requires 
> data for that task to come up with an appropriate model for the solution. Zero-data learning combines 
> aspects of both approaches, taking advantage of human knowledge (task descriptions) and training data (from 
> related tasks), in such a way that it needs less human labor and training data for the new task."

After the first paper I read about ZSL, my immediate reaction was: "This is beautiful: it combines classical
engineering approaches with machine learning -- instead of just relying on one or the other."  The above quote
explains ZSL as just that.

(Larochelle, Erhan, and Bengio [2007]: [Zero-data Learning of New Tasks](http://www.aaai.org/Papers/AAAI/2008/AAAI08-103.pdf))

In this early paper on zero-shot learning (ZSL), the authors purposely train on a set of classes/tasks
that is disjoint from the test set of classes/tasks.  

> "When evaluating zero-data learning algorithms, we wish to measure the extent to which they can compensate for the
> lack of labeled data by using the description vectors. To accomplish this, we separate the training data D_train and
> the test data D_test in such a way that the training and test sets contain labeled data from different classes or 
> tasks."

Later on down the line, this practice is modified a bit,
e.g., a few test set metrics are determined -- one on classes not seen in training, another on onlh classes
seen in training, and an overall score.  


For a presentation-style breakdown of this paper, refer to:
* http://info.usherbrooke.ca/hlarochelle/publications/snowbird-poster-slides-2007.pdf


# Input Space ZSL
This one seems almost directly analogous to Siamese networks from one-shot learning... At least in that you
train the model to always accept the original input vector x along with a description vector d(z), then
train it to a binary {0|1} output corresponding to whether x and d(z) actually belong together... I think... Need
to resolve my understanding of this better.  Both paper and prez kind of leave me feeling like, "I think
I get it... Yea, I... Wait, no. Maybe!"



-------------------------------------------

Can't (easily) get access to the license plate data they used...  So I found a few I might use

* [MNIST](http://yann.lecun.com/exdb/mnist/)
  - this is the standard dataset used all the time
* [The Chars74K dataset](http://www.ee.surrey.ac.uk/CVSSP/demos/chars74k/)
  - this is a "characters out in the wild" data set, so is more advanced than I want for a first project
  

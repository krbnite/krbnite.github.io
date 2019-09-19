Ok, this might be covered in one of the papers I haven't read yet (e.g., Deep Forests), but
I figured I'd jot this idea down and do some fun experiments with it.  Basically, an oft-cited
goal in deep learning is, well, going deep!  That is, adding more layers.  Often DNNs are 
compared with Random Forests, e.g. which is better, people like to ask.  That got me thinking:
why not combine some elements from RFs to simple DNN models.  My idea is not to just bag a neural 
network! With RFs, we bag (bootstrap the data for each model, then aggregate model outputs), but
we also subsample the features at each node in a tree.  That is, we enforce the trees to really have
to take on different perspectives and vantage points.  In DNNs, we might not subsample the features
at each node or layer, but there are other randomizations we can do.  

0. We can bootstrap the data.
1. We can subsample the features at the input layer.
2. For each base model, we can randomly choose how many features to subsample.
3. We can randomize how many layers are used.
4. We can randomize layer widths. 
5. We can randomize weight initializations.
6. We can randomize activation functions.
7. We can randomize regularization procedures (e.g., whether to use dropout, to inject 
   some Gaussian noise at a layer, or use no regularization).
8. We can randomize the regularization parameters (e.g., keep probability for dropout, or
   the amount of Guassian noise injected at a layer).

You get the idea!

Here are general ways of creating ensembles:
1. Combine base classifiers in different ways (e.g., bag some, boost some).
2. Use different base classifiers (e.g., model stacking).
3. Use different feature subsets.
4. Use different data subsets.

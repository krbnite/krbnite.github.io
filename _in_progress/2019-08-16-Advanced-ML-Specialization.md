---
title: AML Specialization
layout: post
tags: easi
---


Starts with linear models -- that is, linear regression as the simplest neural network.  The linear 
model has d+1 parameters, representing the d weights and 1 bias term, where d is the number of input features.

```
x[i]: the ith data point (i.e., x[i,:] in more pythonic syntax)
x[i,j]: the jth feature of the ith data point
y[i]: the ith target value/label
a(x): the model/hypothesis
```

Importantly, in this course, we have the row-wise data vectors ("records"), so the model looks like:
```
y = a(x) = xW+b
```

Or using Einsteinian index notation: sum repeated indices, where the usual covariant/contravariant (bottom/top) indices
are somewhat denoted by being in different "slots" (e.g., an "i" in a row slot in one array, and an "i" in a col slot
of another): 


```
p[i,] = x[i,j]W[j,] + b
# where all b[i,] = b, and p[i,] is prediction/estimate of y[i,]
```

Loss function
```
# MSE
L(w) = (1/d)||p - y||^2
```

We want to minimize loss by finding the weight set, w, that minimizes the 
lost function:
```
min(w){L(w)}
```

For this linear model, there actually is an exact solution (though for very high dimensional
data, it is hard/time-consuming to compute):
```
w = invert(t(x)x) t(x)y # (x^T x)^(-1) x^T y
```

The NN way is to use gradient descent to find an estimate of the solution.

Linear classification
```
z = (x[1]w+b, ..., x[N]w+b)
z' = (exp(z[1]), ..., exp(z[N]))
p = z'/Q(z')  
# Q(z') is partition function (sum over components of z')
# p is known as softmax in ML (Boltzman distro in physics)
```

Iverson bracket
```
[statement] = {1: true | 0: false}
```


Some extra reading:
* Daniel Godoy: [Understanding Binary Cross Entropy Log Loss: A Visual Explanation](https://towardsdatascience.com/understanding-binary-cross-entropy-log-loss-a-visual-explanation-a3ac6025181a)
* Chris Olah: [Visual Information Theory](http://colah.github.io/posts/2015-09-Visual-Information/)



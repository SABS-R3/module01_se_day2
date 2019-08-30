---
title: "Scientific Programming with NumPy"
teaching: 60
exercises: 20
questions:
- "What are the differences between NumPy arrays and Python lists?"
- "How do I extract subsets of data from a dataset?"
- "How can I use NumPy to tell me basic statistical properties about my data?"
objectives:
- "Explain the key similarities and differences between NumPy arrays and Python lists."
- "Select subsets of data from a NumPy array using Python slicing."
- "Efficiently perform elementwise operations on data."
- "Obtain basic characteristics of tabular data using NumPy's statistical functions."
- "Increase the size of a NumPy array."
keypoints:
- "Processing NumPy arrays is generally much faster than processing Python lists."
- "NumPy arrays have specialised capabilities to support complex mathematical operations, and are less flexible that Python lists."
- "Slicing NumPy arrays returns a reference to the original dataset, not a copy of it like with Python lists."
- "NumPy arrays only hold elements of a single data type and are generally fixed in size."
- "Use `numpy.mean(array)`, `numpy.max(array)`, and `numpy.min(array)` to calculate simple statistics."
- "Use `numpy.mean(array, axis=0)` or `numpy.mean(array, axis=1)` to calculate statistics across the specified axis."
---

## Introduction to NumPy

See topic [slides](../slides/11-scientific-numpy.html).

## NumPy Arrays vs Python Lists

NumPy's array type represents a multidimensional matrix *M<sub>i,j,k...n</sub>*

The NumPy array seems at first to be just like a list:

~~~
import numpy as np
my_array = np.array(range(5))
my_array
~~~
{: .language-python}

~~~
array([0, 1, 2, 3, 4])
~~~
{: .output}

Ok, so they *look* like a list.

~~~
my_array[2]
~~~
{: .language-python}

~~~
2
~~~
{: .output}

We can access them like a list.

We can also access NumPy arrays as a collection in `for` loops:

~~~
for element in my_array:
    print("Hello" * element)
~~~
{: .language-python}

~~~
Hello
HelloHello
HelloHelloHello
HelloHelloHelloHello
~~~
{: .output}

However, we can also see our first weakness of NumPy arrays versus Python lists:

~~~
my_array.append(4)
~~~
{: .language-python}

~~~
---
AttributeError                            Traceback (most recent call last)
<ipython-input-10-b12177763178> in <module>()
----> 1 my_array.append(4)

AttributeError: 'numpy.ndarray' object has no attribute 'append'
~~~
{: .output}

For NumPy arrays, you typically don't change the data size once you've defined your array, whereas for Python lists, you can do this efficiently. Also, NumPy arrays only contain one datatype. However, you get back lots of goodies in return...

## Elementwise Operations

One great advantage of NumPy arrays is that most operations can be applied element-wise automatically, and in a very Pythonic way!

~~~
my_array + 2
~~~
{: .language-python}

`+` in this context is an elementwise operation performed on all the matrix elements.

~~~
array([2, 3, 4, 5, 6])
~~~
{: .output}

These are known as 'vectorised' operations, and are very fast. We'll be looking at making use of these in more detail next week when we look at optimisation.

But for now, let's take a brief look at a basic example that demonstrates the performance of NumPy over Python lists. First, using Python lists we can do the following, that creates a 2D list of size 10000x10000, sets all elements to zero, then adds 10 to all those elements:

~~~
%%timeit
nested_list = [[0 for _ in range(10000)] for _ in range(10000)]
nested_list = [[x+10 for x in column] for column in nested_list]
~~~
{: .language-python}

Here, we use `%timeit` magic to run this cell a number of times and display an average time for how long it took to run. More [information on timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit) is available.

That took a while! In NumPy we replicate this by doing:

~~~
%%timeit
import numpy
array = numpy.zeros((10000, 10000))
array = array + 10
~~~
{: .language-python}

Here, we import the NumPy library, use a specialised function to set up a NumPy array of size 10000x10000 with elements set to zero, and then - in a very Pythonic way - add 10 to each element. It's simpler, closer to our conceptual model of what we want to achieve, and it's much faster.

## Revisiting our Inflammation Dataset

Let's take a more in-depth look at our CSV inflammation dataset we looked at previously.

As before, we can use NumPy to load our dataset into a Python variable:

~~~
data = numpy.loadtxt(fname='inflammation-01.csv', delimiter=',')
data
~~~
{: .language-python}

~~~
array([[0., 0., 1., ..., 3., 0., 0.],
       [0., 1., 2., ..., 1., 0., 1.],
       [0., 1., 1., ..., 2., 1., 1.],
       ...,
       [0., 1., 1., ..., 1., 1., 1.],
       [0., 0., 0., ..., 0., 2., 0.],
       [0., 0., 1., ..., 1., 1., 0.]])
~~~
{: .output}

As a reminder, each row holds information for a single patient, and the columns represent successive days. Each value represents a single inflammation reading for a patient.

> ## In the Corner
>
> What may also surprise you is that when Python displays an array,
> it shows the element with index `[0, 0]` in the upper left corner
> rather than the lower left.
> This is consistent with the way mathematicians draw matrices
> but different from the Cartesian coordinates.
> The indices are (row, column) instead of (column, row) for the same reason,
> which can be confusing when plotting data.
{: .callout}

### Examining our Dataset

Let's ask what type of thing `data` refers to:

~~~
print(type(data))
~~~
{: .language-python}

~~~
<class 'numpy.ndarray'>
~~~
{: .output}

The output tells us that `data` currently refers to an N-dimensional array, the functionality for which is provided by the NumPy library.

> ## Data Type
>
> A Numpy array contains one or more elements of the same type. The `type` function will only tell you that a variable is a NumPy array but won't tell you the type of thing inside the array. We can find out the type of the data contained in the NumPy array.
>
> ~~~
> print(data.dtype)
> ~~~
> {: .language-python}
>
> ~~~
> dtype('float64')
> ~~~
> {: .output}
>
> This tells us that the NumPy array's elements are floating-point numbers.
{: .callout}

With the following command, we can see the array's *shape*:

~~~
print(data.shape)
~~~
{: .language-python}

~~~
(60, 40)
~~~
{: .output}

The output tells us that the `data` array variable contains 60 rows and 40 columns, as we would expect.

We can also access specific elements of our 2D array (such as the first value) like this:

~~~
data[0, 0]
~~~
{: .language-python}

~~~
0.0
~~~
{: .output}

Or the value in the middle of the dataset:

~~~
data[30, 20]
~~~
{: .language-python}

~~~
13.0
~~~
{: .output}


### Slicing our Inflammation Data

An index like `[30, 20]` selects a single element of an array, but similar to how we slice lists, we can slice whole sections of NumPy arrays as well.

For example, we can select the first ten days (columns) of values for the first four patients (rows) like this:

~~~
data[0:4, 0:10]
~~~
{: .language-python}

~~~
[[ 0.  0.  1.  3.  1.  2.  4.  7.  8.  3.]
 [ 0.  1.  2.  1.  2.  1.  3.  2.  2.  6.]
 [ 0.  1.  1.  3.  3.  2.  6.  2.  5.  9.]
 [ 0.  0.  2.  0.  4.  2.  2.  1.  6.  7.]]
~~~
{: .output}

So here `0:4` means, "Start at index 0 and go up to, but not including, index 4." Again, the up-to-but-not-including takes a bit of getting used to, but the rule is that the difference between the upper and lower bounds is the number of values in the slice.

And as we saw with lists, we don't have to start slices at 0:

~~~
data[5:10, 0:10]
~~~
{: .language-python}

Which will show us data from patients 5-9 (rows) across the first 10 days (columns):

~~~
[[ 0.  0.  1.  2.  2.  4.  2.  1.  6.  4.]
 [ 0.  0.  2.  2.  4.  2.  2.  5.  5.  8.]
 [ 0.  0.  1.  2.  3.  1.  2.  3.  5.  3.]
 [ 0.  0.  0.  3.  1.  5.  6.  5.  5.  8.]
 [ 0.  1.  1.  2.  1.  3.  5.  3.  5.  8.]]
~~~
{: .output}

We also don't have to include the upper and lower bound on the slice. If we don't include the lower bound, Python uses 0 by default; if we don't include the upper, the slice runs to the end of the axis, and if we don't include either (i.e., if we just use ':' on its own), the slice includes everything:

~~~
small = data[:3, 36:]
small
~~~
{: .language-python}

The above example selects rows 0 through 2 and columns 36 through to the end of the array:

~~~
[[ 2.  3.  0.  0.]
 [ 1.  1.  0.  1.]
 [ 2.  2.  1.  1.]]
~~~
{: .output}

> ## Numpy Memory
>
> Numpy memory management can be tricksy:
>
> ~~~
> x = np.arange(5)
> y = x[:]
> ~~~
> {: .language-python}
>
> ~~~
> y[2] = 0
> x
> ~~~
> {: .language-python}
>
> ~~~
> array([0, 1, 0, 3, 4])
> ~~~
> {: .output}
>
> It does not behave like lists!
>
> ~~~
> x = list(range(5))
> y = x[:]
> ~~~
> {: .language-python}
>
> ~~~
> y[2] = 0
> x
> ~~~
> {: .language-python}
>
> ~~~
> [0, 1, 2, 3, 4]
> ~~~
> {: .output}
>
> We must use np.copy to force separate memory. Otherwise NumPy tries its hardest to make slices be views on data.
{: .callout}

### Elementwise Operations on Multiple Arrays

As we've seen, arrays also know how to perform common mathematical operations on their values element-by-element:

~~~
doubledata = data * 2.0
~~~
{: .language-python}

Will create a new array `doubledata`, each element of which is twice the value of the corresponding element in `data`:

~~~
print('original:')
data[:3, 36:]
print('doubledata:')
doubledata[:3, 36:]
~~~
{: .language-python}

~~~
original:
[[ 2.  3.  0.  0.]
 [ 1.  1.  0.  1.]
 [ 2.  2.  1.  1.]]
doubledata:
[[ 4.  6.  0.  0.]
 [ 2.  2.  0.  2.]
 [ 4.  4.  2.  2.]]
~~~
{: .output}

If, instead of taking an array and doing arithmetic with a single value (as above), you did the arithmetic operation with another array of the same shape, the operation will be done on corresponding elements of the two arrays:

~~~
tripledata = doubledata + data
~~~
{: .language-python}

Will give you an array where `tripledata[0,0]` will equal `doubledata[0,0]` plus `data[0,0]`,
and so on for all other elements of the arrays.

~~~
print('tripledata:')
print(tripledata[:3, 36:])
~~~
{: .language-python}

~~~
tripledata:
[[ 6.  9.  0.  0.]
 [ 3.  3.  0.  3.]
 [ 6.  6.  3.  3.]]
~~~
{: .output}

> ## Stacking Arrays
>
> Arrays can be concatenated and stacked on top of one another,
> using NumPy's `vstack` and `hstack` functions for vertical and horizontal stacking, respectively.
>
> ~~~
> import numpy
>
> A = numpy.array([[1,2,3], [4,5,6], [7,8,9]])
> print('A = ')
> print(A)
>
> B = numpy.hstack([A, A])
> print('B = ')
> print(B)
>
> C = numpy.vstack([A, A])
> print('C = ')
> print(C)
> ~~~
> {: .language-python}
>
> ~~~
> A =
> [[1 2 3]
>  [4 5 6]
>  [7 8 9]]
> B =
> [[1 2 3 1 2 3]
>  [4 5 6 4 5 6]
>  [7 8 9 7 8 9]]
> C =
> [[1 2 3]
>  [4 5 6]
>  [7 8 9]
>  [1 2 3]
>  [4 5 6]
>  [7 8 9]]
> ~~~
> {: .output}
>
> Write some additional code that slices the first and last columns of our inflammation `data` array,
> and stacks them into a 60x2 array, to give us data from the first and last days of our trial across all patients.
> Make sure to `print` the results to verify your solution.
>
> > ## Solution
> >
> > A 'gotcha' with array indexing is that singleton dimensions
> > are dropped by default. That means `data[:, 0]` is a one dimensional
> > array, which won't stack as desired. To preserve singleton dimensions,
> > the index itself can be a slice or array. For example, `data[:, :1]` returns
> > a two dimensional array with one singleton dimension (i.e. a column
> > vector).
> >
> > ~~~
> > D = numpy.hstack([data[:, :1], data[:, -1:]])
> > print('D = ')
> > print(D)
> > ~~~
> > {: .language-python}
> >
> > ~~~
> > D =
> > [[0. 0.]
> >  [0. 1.]
> >  ...
> >  [0. 0.]
> >  [0. 0.]]
> > ~~~
> > {: .output}
> {: .solution}
{: .challenge}


### More Complex Operations

Often, we want to do more than add, subtract, multiply, and divide array elements. NumPy knows how to do more complex operations, too. If we want to find the average inflammation for all patients on all days, for example, we can ask NumPy to compute `data`'s mean value:

~~~
print(numpy.mean(data))
~~~
{: .language-python}

~~~
6.14875
~~~
{: .output}

`mean` is a function that takes an array as an argument.

> ## Not All Functions Have Input
>
> Generally, a function uses inputs to produce outputs.
> However, some functions produce outputs without
> needing any input. For example, checking the current time
> doesn't require any input.
>
> ~~~
> import time
> print(time.ctime())
> ~~~
> {: .language-python}
>
> ~~~
> Sat Mar 26 13:07:33 2016
> ~~~
> {: .output}
>
> For functions that don't take in any arguments,
> we still need parentheses (`()`)
> to tell Python to go and do something for us.
{: .callout}

NumPy has lots of useful functions that take an array as input.

Let's use three of those functions to get some descriptive values about the dataset. We'll also use multiple assignment, a convenient Python feature that will enable us to do this all in one line.

~~~
maxval, minval, stdval = numpy.max(data), numpy.min(data), numpy.std(data)

print('max inflammation:', maxval)
print('min inflammation:', minval)
print('std deviation:', stdval)
~~~
{: .language-python}

Here we've assigned the return value from `numpy.max(data)` to the variable `maxval`, the value
from `numpy.min(data)` to `minval`, and so on.

~~~
max inflammation: 20.0
min inflammation: 0.0
std deviation: 4.61383319712
~~~
{: .output}

When analyzing data, though, we often want to look at variations in statistical values, such as the maximum inflammation per patient or the average inflammation per day. One way to do this is to create a new temporary array of the data we want, then ask it to do the calculation:

~~~
numpy.max(data[0, :])
~~~
{: .language-python}

So here, we're looking at the maximum inflammation across all days for the first patient, which is

~~~
18.0
~~~
{: .output}

What if we need the maximum inflammation for each patient over all days (as in the next diagram on the left) or the average for each day (as in the diagram on the right)? As the diagram below shows, we want to perform the operation across an axis:

FIXME: Operations Across Axes image - /fig/python-operations-across-axes.png

To support this functionality, most array functions allow us to specify the axis we want to work on. If we ask for the average across axis 0 (rows in our 2D example), we get:

~~~
print(numpy.mean(data, axis=0))
~~~
{: .language-python}

~~~
[  0.           0.45         1.11666667   1.75         2.43333333   3.15
   3.8          3.88333333   5.23333333   5.51666667   5.95         5.9
   8.35         7.73333333   8.36666667   9.5          9.58333333
  10.63333333  11.56666667  12.35        13.25        11.96666667
  11.03333333  10.16666667  10.           8.66666667   9.15         7.25
   7.33333333   6.58333333   6.06666667   5.95         5.11666667   3.6
   3.3          3.56666667   2.48333333   1.5          1.13333333
   0.56666667]
~~~
{: .output}

As a quick check, we can ask this array what its shape is:

~~~
print(numpy.mean(data, axis=0).shape)
~~~
{: .language-python}

~~~
(40,)
~~~
{: .output}

The expression `(40,)` tells us we have an N×1 vector, so this is the average inflammation per day for all patients. If we average across axis 1 (columns in our 2D example), we get:

~~~
patients_avg = numpy.mean(data, axis=1)
patients_avg
~~~
{: .language-python}

~~~
[ 5.45   5.425  6.1    5.9    5.55   6.225  5.975  6.65   6.625  6.525
  6.775  5.8    6.225  5.75   5.225  6.3    6.55   5.7    5.85   6.55
  5.775  5.825  6.175  6.1    5.8    6.425  6.05   6.025  6.175  6.55
  6.175  6.35   6.725  6.125  7.075  5.725  5.925  6.15   6.075  5.75
  5.975  5.725  6.3    5.9    6.75   5.925  7.225  6.15   5.95   6.275  5.7
  6.1    6.825  5.975  6.725  5.7    6.25   6.4    7.05   5.9  ]
~~~
{: .output}

Which is the average inflammation per patient across all days.

> ## Change In Inflammation
>
> This patient data is _longitudinal_ in the sense that each row represents a
> series of observations relating to one individual.  This means that
> the change in inflammation over time is a meaningful concept.
>
> The `numpy.diff()` function takes a NumPy array and returns the
> differences between two successive values along a specified axis.  For
> example, a NumPy array that looks like this:
>
> ~~~
> npdiff = numpy.array([ 0,  2,  5,  9, 14])
> ~~~
> {: .language-python}
>
> Calling `numpy.diff(npdiff)` would do the following calculations and
> put the answers in another array.
>
> ~~~
> [ 2 - 0, 5 - 2, 9 - 5, 14 - 9 ]
> ~~~
> {: .language-python}
>
> ~~~
> numpy.diff(npdiff)
> ~~~
> {: .language-python}
>
> ~~~
> array([2, 3, 4, 5])
> ~~~
> {: .language-python}
>
> Which axis would it make sense to use this function along?
>
> > ## Solution
> > Since the row axis (0) is patients, it does not make sense to get the
> > difference between two arbitrary patients. The column axis (1) is in
> > days, so the difference is the change in inflammation -- a meaningful
> > concept.
> >
> > ~~~
> > numpy.diff(data, axis=1)
> > ~~~
> > {: .language-python}
> {: .solution}
>
> If the shape of an individual data file is `(60, 40)` (60 rows and 40
> columns), what would the shape of the array be after you run the `diff()`
> function and why?
>
> > ## Solution
> > The shape will be `(60, 39)` because there is one fewer difference between
> > columns than there are columns in the data.
> {: .solution}
>
> How would you find the largest change in inflammation for each patient? Does
> it matter if the change in inflammation is an increase or a decrease?
>
> > ## Solution
> > By using the `numpy.max()` function after you apply the `numpy.diff()`
> > function, you will get the largest difference between days. We can *functionally
> > compose* these together.
> >
> > ~~~
> > numpy.max(numpy.diff(data, axis=1), axis=1)
> > ~~~
> > {: .language-python}
> >
> > ~~~
> > array([  7.,  12.,  11.,  10.,  11.,  13.,  10.,   8.,  10.,  10.,   7.,
> >          7.,  13.,   7.,  10.,  10.,   8.,  10.,   9.,  10.,  13.,   7.,
> >         12.,   9.,  12.,  11.,  10.,  10.,   7.,  10.,  11.,  10.,   8.,
> >         11.,  12.,  10.,   9.,  10.,  13.,  10.,   7.,   7.,  10.,  13.,
> >         12.,   8.,   8.,  10.,  10.,   9.,   8.,  13.,  10.,   7.,  10.,
> >          8.,  12.,  10.,   7.,  12.])
> > ~~~
> > {: .language-python}
> >
> > If inflammation values *decrease* along an axis, then the difference from
> > one element to the next will be negative. If
> > you are interested in the **magnitude** of the change and not the
> > direction, the `numpy.absolute()` function will provide that.
> >
> > Notice the difference if you get the largest _absolute_ difference
> > between readings.
> >
> > ~~~
> > numpy.max(numpy.absolute(numpy.diff(data, axis=1)), axis=1)
> > ~~~
> > {: .language-python}
> >
> > ~~~
> > array([ 12.,  14.,  11.,  13.,  11.,  13.,  10.,  12.,  10.,  10.,  10.,
> >         12.,  13.,  10.,  11.,  10.,  12.,  13.,   9.,  10.,  13.,   9.,
> >         12.,   9.,  12.,  11.,  10.,  13.,   9.,  13.,  11.,  11.,   8.,
> >         11.,  12.,  13.,   9.,  10.,  13.,  11.,  11.,  13.,  11.,  13.,
> >         13.,  10.,   9.,  10.,  10.,   9.,   9.,  13.,  10.,   9.,  10.,
> >         11.,  13.,  10.,  10.,  12.])
> > ~~~
> > {: .language-python}
> {: .solution}
{: .challenge}

## Multi-dimensional Arrays

FIXME: show reshape(2, 30) to get patients_avg array split into two sub-arrays

### Populating Arrays with Stepwise Data

FIXME: add new dimension to inflammation data, populate it

### Dot Products

FIXME: theoretical example from RSD materials

{% include links.md %}
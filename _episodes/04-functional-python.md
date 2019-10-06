---
title: "Functional Programming in Python"
teaching: 0
exercises: 0
questions:
- "Key question (FIXME)"
objectives:
- "First learning objective. (FIXME)"
keypoints:
- "First key point. Brief Answer to questions. (FIXME)"
---

## The Functional Paradigm


## Comprehensions

Comprehensions are a more **Pythonic** way to do some of these operations.

### List Comprehensions

List comprehensions 

~~~
print([i for i in range(5)])
print([2 * i for i in range(5)])
~~~
{: .language-python}

~~~
[0, 1, 2, 3, 4]
[0, 2, 4, 6, 8]
~~~
{: .output}

~~~
print([2 * i for i in range(5) if i % 2 == 0])
~~~
{: .language-python}

~~~
[0, 4, 8]
~~~
{: .output}

### Dictionary and Set Comprehensions

Exactly the same as list comprehensions, but using the dictionary or set literal syntax.

~~~
print({i: 2 * i for i in range(5)})
~~~
{: .language-python}

~~~
{0: 0, 1: 2, 2: 4, 3: 6, 4: 8}
~~~
{: .output}

~~~
print({2 * i for i in range(5)})
~~~
{: .language-python}

~~~
{0, 2, 4, 6, 8}
~~~
{: .output}

## Generators

### Generator Comprehensions

Generator comprehensions look very similar to the comprehensions we've seen previously.
What happens if we try to use them in the same was as we did?

~~~
print((2 * i for i in range(5)))
~~~
{: .language-python}

~~~
<generator object <genexpr> at 0x7efc21efcdd0>
~~~
{: .output}

Generator expressions are not evaluated until you iterate over them.

~~~
for i in (2 * i for i in range(5)):
    print(i)
~~~
{: .language-python}

~~~
0
2
4
6
8
~~~
{: .output}

Performance characteristics.

If we initialise and fully iterate over each comprehension, the time taken is very similar.

~~~
%%timeit
l = [2*x for x in range(1000000)]
for x in l:
    pass
~~~
{: .language-python}

~~~
66 ms ± 492 µs per loop (mean ± std. dev. of 7 runs, 10 loops each)
~~~
{: .output}

~~~
%%timeit
l = (2*x for x in range(1000000))
for x in l:
    pass
~~~
{: .language-python}

~~~
65.4 ms ± 2.3 ms per loop (mean ± std. dev. of 7 runs, 10 loops each)
~~~
{: .output}

If we initialise both a list and a generator comprehension and store them, but do not iterate over them, the generator is around 100,000x faster.

~~~
%%timeit
l = [2*x for x in range(1000000)]
~~~
{: .language-python}

~~~
59.7 ms ± 372 µs per loop (mean ± std. dev. of 7 runs, 10 loops each)
~~~
{: .output}

~~~
%%timeit
l = (2*x for x in range(1000000))
~~~
{: .language-python}

~~~
494 ns ± 11.5 ns per loop (mean ± std. dev. of 7 runs, 1000000 loops each)
~~~
{: .output}

If we initialise both versions, but only partially iterate over each, then we can see the benefit of using a generator comprehension over a list comprehension.

~~~
%%timeit
l = [2*x for x in range(1000000)]
for x in l:
    if x > 10:
        break
~~~
{: .language-python}

~~~
63.5 ms ± 1.77 ms per loop (mean ± std. dev. of 7 runs, 10 loops each)
~~~
{: .output}

~~~
%%timeit
l = (2*x for x in range(1000000))
for x in l:
    if x > 10:
        break
~~~
{: .language-python}

~~~
988 ns ± 5.76 ns per loop (mean ± std. dev. of 7 runs, 1000000 loops each)
~~~
{: .output}


Performance in memory and time - use iPython timeit magic

Counting papers from academics

Sorting papers by title, day

Needs to end day with 2d array of papers published per person per day - run a simulation of people publishing on random days

## Decorators
Function that accepts a function and returns a (different) function

~~~
def decorate_hello(fun):
    def inner(*args, **kwargs):
        print('Hello!')
        return fun(*args, **kwargs)
    
    return inner

@decorate_hello
def say_world():
    print('World!')

say_world()
~~~
{: .language-python}

~~~
Hello!
World!
~~~
{: .output}

{% include links.md %}

